# DevSecOps-terraform-ec2-jenkins-SAST-AQUA-SCA-DAST-AWS-EKS-infra

Helpful Terraform Links:

    Terraform Language Documentation
    Resource: aws_security_group
    Resource: aws_instance
    Download the code in in this repository "Main branch": https://github.com/awanmbandi/eagles-batch-devops-projects.git

Step 0: Initialize Terraform

- terraform init

Step 1: Plan Resources

- terraform plan -var-file="vars/dev-east-1.tfvars"

Step 2: Apply Resources

- terraform apply -var-file="vars/dev-east-1.tfvars"

Step 3: SSH to instance to get the admin password

- chmod 400 <keypair>
- ssh -i <keypair> ec2-user@<public_dns>
- sudo cat /var/lib/jenkins/secrets/initialAdminPassword

# Some useful Kubernetes commands:

- $ cat /home/ec2-user/.kube/config  --> to get context information of kubernetes cluster.
- $ kubectl create namespace test --> to create namespace in kubernetes cluster.
- $ kubectl get deployments --namespace=devsecops --> to get deployments in a namespace in kubernetes cluster.
- $ kubectl get svc --namespace=devsecops --> to get services in a namespace in kubernetes cluster.
- $ kubectl delete all --all -n devsecops --> to delete everything in a namespace in kubernetes cluster.
- $ kubectl apply -f deployment.yml/service.yml
- $ kuebctl get pod/node/deployment/svc --all-namespaces
- $ kubectl get pod -o wide
- $ kubectl describe pod/svc 
- $ kubectl explain pod/svc
- $ kubectl delete pod/deployment/service
- $ kuebctl describe pod/podname
- $ kubectl apply -f deployment.yml/service.yml
- $ kubectl exec -it podname bash

# Some useful Docker command 

- $ docker system prune  --> to delete unused docker images to cleanup memeory on system 
- $ docker rmi -f $(docker images -aq) --> to delete a docker image
- $ docker rm -f $(docker ps -aq)
- $ docker pull/run <Images>
- $ docker start/stop/restart <containerName>
- $ docker inspect <ContainerName>
- $ docker ps / ps -a
- $ docker images
- $ docker exec -it -u -0 <ContainerName> bash
- $ docker exec <ContainerName> cat /etc/os-release
- $ docker volume/network create <name>
- $ docker build -t <MyImageName> .
- $ docker login
- $ docker history/push/search/tag/cp

# Note!
-  -it / -d --> interactive terminal / -d detached mode
-  -v --> to attach a volume
-  -p --> for port mapping
-  --network --> attach to a specifiec network-
  
-  CMD vs ENTRYPOINT
-  CMD --> Can be overwritten during container lounch time 
-  CMD --> Specifies the arguments to be passed to the ENTRYPOINT
-  ENTRYPOINT --> Cannot be overwritten
-  ENTRYPOINT --> Allows you to configure a container that will run as an executable
-  ENTRYPOINT --> Instruction specifies commands that when the container starts.

# Create EKS cluster

- $ eksctl create cluster --name kubernetes-cluster --version 1.23 --region us-east-1 --nodegroup-name linux-nodes --node-type t2.xlarge --nodes 2 

# Delete EKS cluster

- $ eksctl delete cluster --region=us-east-1 --name=kubernetes-cluster #delete eks cluster
