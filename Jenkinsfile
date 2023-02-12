 pipeline {
    agent any
    tools {
        maven 'maven 3.6.0'
    }
    // environment {
    //     NEXUS_VERSION = "nexus3"
    //     NEXUS_PROTOCOL = "http"
    //     NEXUS_URL = "172.31.40.209:8081"
    //     NEXUS_REPOSITORY = "vprofile-release"
	// NEXUS_REPO_ID    = "vprofile-release"
    //     NEXUS_CREDENTIAL_ID = "nexuslogin"
    //     ARTVERSION = "${env.BUILD_ID}"
    // }
    stages{

    stage('UNIT TEST'){
        steps {
            sh 'mvn test'
        }
    }
    stage('CHECKSTYLE CODE ANALYSIS'){
        steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
            }
        }
    }
    stage('SAST SCANNING. @SONAR/QUALITY-GATES') {
        steps {
          sh """ mvn sonar:sonar \
	    	    -Dsonar.projectKey=easybuggy \
            	-Dsonar.host.url=http://54.226.168.88:9000 \
            	-Dsonar.login=38df27852cfca6f019cba5094b1e34fb3c6ae3d4"""
            }
        }
    stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
            }
        }
    }
    stage('SCA SCANNING. @SNYK.. ') {
        steps {		
			withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
			sh 'mvn snyk:test -fn'
			}
		}
    }
    stage('BUILDING DOCKER IMAGE') { 
        steps { 
            withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
            script{
                app =  docker.build("asg")
                }
            }
        }
    }
    stage("SCANING DOCKER IMAGE. @AQUA "){
			steps	{
				aqua customFlags: '', hideBase: false, hostedImage: '', localImage: registry + ":$BUILD_NUMBER", locationType: 'local', notCompliesCmd: '', onDisallowed: 'ignore', policies: '', register: false, registry: '', showNegligible: false
		}
	}
    stage('UPLOADING ARCHIFACTS @ECR') {
        steps {
            script{
                docker.withRegistry('https://145988340565.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                app.push("latest")
                    }
                }
            }
    }
    // stage("Publish Artifacts to Nexus Repository Manager") {
    //     steps {
    //         script {
    //             pom = readMavenPom file: "pom.xml";
    //             filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
    //             echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
    //             artifactPath = filesByGlob[0].path;
    //             artifactExists = fileExists artifactPath;
    //             if(artifactExists) {
    //                 echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version} ARTVERSION";
    //                 nexusArtifactUploader(
    //                     nexusVersion: NEXUS_VERSION,
    //                     protocol: NEXUS_PROTOCOL,
    //                     nexusUrl: NEXUS_URL,
    //                     groupId: pom.groupId,
    //                     version: ARTVERSION,
    //                     repository: NEXUS_REPOSITORY,
    //                     credentialsId: NEXUS_CREDENTIAL_ID,
    //                     artifacts: [
    //                         [artifactId: pom.artifactId,
    //                         classifier: '',
    //                         file: artifactPath,
    //                         type: pom.packaging],
    //                         [artifactId: pom.artifactId,
    //                         classifier: '',
    //                         file: "pom.xml",
    //                         type: "pom"]
    //                     ]
    //                 );
    //             } 
	//     else {
    //                 error "*** File: ${artifactPath}, could not be found";
    //             }
    //         }
    //     }
    // }
    //     stage('Approval') {
    //         steps {
    //         input('Do you want to proceed?')
    //     }
    // }

    stage('DEPLOYING WEBAPP TO EKS @devsecops namespace') {
	    steps {
	        withKubeConfig([credentialsId: 'kubelogin']) {
		    sh('kubectl delete all --all -n devsecops')
		    sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		    }
	    }
   	}
    stage('WAITING FOR WEBSITE TO BECOME ACTIVE'){
	   steps {
		   sh 'pwd; sleep 180; echo "Application Has been deployed on K8S"'
	   	}
	}
	   
	stage('DAST SCANNING @OWASP-ZAP') {
        steps {
		    withKubeConfig([credentialsId: 'kubelogin']) {
			    sh('zap.sh -cmd -quickurl http://$(kubectl get services/asgbuggy --namespace=devsecops -o json| jq -r ".status.loadBalancer.ingress[] | .hostname") -quickprogress -quickout ${WORKSPACE}/zap_report.html')
				archiveArtifacts artifacts: 'zap_report.html'
		    }
	    }
    }
    stage('SENDING SLACK NOTIFICATION') {
        steps {
            sh ''
        }
        post{
            always {
                sh "echo Slack Notifications."
                slackSend channel: '#jenkinscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
            }
        }
    }
  }
}

