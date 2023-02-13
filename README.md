# DevSecOps-terraform-ec2-jenkins-SAST-AQUA-SCA-DAST-AWS-EKS-infra

Helpful Terraform Links:

    Terraform Language Documentation
    Resource: aws_security_group
    Resource: aws_instance

Step 0: Initialize Terraform

terraform init

Step 1: Plan Resources

terraform plan -var-file="vars/dev-east-1.tfvars"

Step 2: Apply Resources

terraform apply -var-file="vars/dev-east-1.tfvars"

Step 3: SSH to instance to get the admin password

chmod 400 <keypair>
ssh -i <keypair> ec2-user@<public_dns>
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

Some useful commands:
cat /home/ec2-user/.kube/config  #to get context information of kubernetes cluster
kubectl create namespace test #to create namespace in kubernetes cluster
kubectl get deployments --namespace=test #to get deployments in a namespace in kubernetes cluster
kubectl get svc --namespace=test #to get services in a namespace in kubernetes cluster
kubectl delete all --all -n test #to delete everything in a namespace in kubernetes cluster
docker system prune  # to delete unused docker images to cleanup memeory on system 
docker image rm imagename  # to delete a docker image

#create EKS cluster
eksctl create cluster --name kubernetes-cluster --version 1.23 --region us-west-2 --nodegroup-name linux-nodes --node-type t2.xlarge --nodes 2 

#delete EKS cluster
eksctl delete cluster --region=us-east-1 --name=kubernetes-cluster #delete eks cluster
