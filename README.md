# Deployment of Super Mario on Kubernetes using Terraform

## Introduction
This project involves deploying the Super Mario game onto a Kubernetes cluster using Terraform for infrastructure provisioning. Terraform defines and creates necessary infrastructure components, such as Kubernetes clusters, networking resources, and storage. Kubernetes then manages the deployment, scaling, and orchestration of the Super Mario game application within the cluster.

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Infrastructure Setup](#infrastructure-setup)
   - [Launch & Configure EC2 Instance](#launch--configure-ec2-instance)
   - [Setup Docker, Terraform, AWS CLI & Kubectl](#setup-docker-terraform-aws-cli--kubectl)
   - [Create IAM Role for EC2](#create-iam-role-for-ec2)
   - [Attach IAM Role to EC2](#attach-iam-role-to-ec2)
   - [Create an S3 Bucket](#create-an-s3-bucket)
4. [Deploying Super Mario](#deploying-super-mario)
   - [Clone Terraform Repository](#clone-terraform-repository)
   - [Initialize Terraform](#initialize-terraform)
   - [Validate & Apply Terraform Plan](#validate--apply-terraform-plan)
   - [Configure Kubernetes Deployment & Service](#configure-kubernetes-deployment--service)
   - [Accessing the Game](#accessing-the-game)
5. [Cleanup Resources](#cleanup-resources)
6. [Troubleshooting](#troubleshooting)
7. [Conclusion](#conclusion)

## Prerequisites
- AWS Account (Free Tier or Paid)
- AWS CLI installed and configured
- Terraform installed
- Docker installed
- Kubectl installed

## Infrastructure Setup
### Launch & Configure EC2 Instance
1. Open AWS Console and search for **EC2**.
2. Click **Launch Instances**.
3. Set instance name to **Terraform Mario** and select **Ubuntu 22.04 LTS**.
4. Choose **t2.micro** instance type.
5. Create a new key pair named **mariokey** and download it.
6. Configure networking with a new security group allowing **HTTPS and HTTP**.
7. Click **Launch Instance** and connect via **EC2 Instance Connect**.
8. Update package lists:
   ```sh
   sudo su
   apt update -y
   ```

### Setup Docker, Terraform, AWS CLI & Kubectl
#### Install Docker
```sh
apt install docker.io -y
usermod -aG docker $USER
newgrp docker
```
#### Install Terraform
```sh
sudo apt install wget -y
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform -y
```
#### Install AWS CLI
```sh
sudo apt install unzip curl -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
#### Install Kubectl
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### Create IAM Role for EC2
1. Open **IAM** in AWS Console.
2. Click **Roles > Create Role**.
3. Select **EC2**, click **Next**.
4. Choose **Administrator Access** and name it **Mario_Game**.
5. Click **Create Role**.

### Attach IAM Role to EC2
1. Navigate to **EC2 Dashboard**.
2. Select the **Terraform Mario** instance.
3. Click **Actions > Security > Modify IAM Role**.
4. Choose **Mario_Game** role and update.

### Create an S3 Bucket
1. Open **S3** in AWS Console.
2. Click **Create Bucket**.
3. Enter a unique bucket name.
4. Disable public access and confirm.

## Deploying Super Mario
### Clone Terraform Repository
```sh
mkdir super_mario
cd super_mario
git clone https://github.com/k21academyuk/Deployment-of-super-Mario-on-Kubernetes-using-terraform.git
cd Deployment-of-super-Mario-on-Kubernetes-using-terraform/EKS-TF
```
### Initialize Terraform
```sh
terraform init
```
### Validate & Apply Terraform Plan
```sh
terraform validate
terraform plan
terraform apply -auto-approve
```
### Configure Kubernetes Deployment & Service
```sh
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```
### Accessing the Game
1. Get Load Balancer URL:
   ```sh
   kubectl describe service mario-service
   ```
2. Copy the LoadBalancer Ingress URL and open it in a browser.

## Cleanup Resources
```sh
kubectl delete service mario-service
kubectl delete deployment mario-deployment
cd EKS-TF
tf destroy -auto-approve
```
Terminate EC2 Instance:
1. Go to **EC2 Dashboard**.
2. Select **Terraform Mario** instance.
3. Click **Actions > Terminate**.

## Troubleshooting
**Issue: Empty Response Error**
- **Cause**: No running pods.
- **Fix**:
  ```sh
  kubectl apply -f deployment.yaml
  kubectl get pods
  ```
  Once pods are running, access the LoadBalancer URL again.

## Conclusion
This project showcases the deployment of Super Mario on Kubernetes using Terraform for infrastructure setup. By leveraging Terraform and Kubernetes, the deployment ensures scalability, reliability, and efficient management of the game environment.
