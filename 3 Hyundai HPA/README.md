# Problem:
<div align="center">
  <div align="center">
  Gabriel Works on an dealership. This enterprise sell cars of the Hyundai, like Hyundai HB20. Your boss ask to him to build an infrastructure to host the site of dearlership, this infrastructure it has to be scalable, available, securely and efficiently using Docker (This image contain the web site of hyundai) and Kubernetes (using concepts of HPA, replicaSet, readinessProbe and ivenessProbe)
    <div>
      <h1>Gabriel creates the "Hyundai HPA"</h1>
      <img src="Hyundai/HPA.png" width="200">
    </div>
    </div>
</div>

# SERVICES AND TOOLS USED:
<div align="center">
  <img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white" hspace=2>
  <img src="https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white" hspace=2>
  <img src="https://img.shields.io/badge/Amazon_AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white" hspace=2>
  <div align="center">
    <img src="https://img.shields.io/badge/Visual_Studio_Code-0078D4?style=for-the-badge&logo=visual%20studio%20code&logoColor=white" hspace=2>
    <img src="https://img.shields.io/badge/GIT-E44C30?style=for-the-badge&logo=git&logoColor=white" hspace=2>
  </div>
  <div align="center">
    <img src="https://collabnix.com/wp-content/uploads/2020/05/1200px-Kubernetes_logo_without_workmark-1.png" width=100>
    <img src="https://cdn-icons-png.flaticon.com/512/919/919853.png" width=95 hspace=5>
    <img src="https://www.pngkey.com/png/full/898-8982104_aws-codebuild.png" width=75 hspace=5>
  </div>
</div>

# STEPS IN CLOUD:
---
# REQUIRIMENTS
- Github Repository:
  - Repository for the Project (hyundai-project)
  - Repository for the k8s manifests (hyundai-project-hml)
- AWS Account
- Docker HUB Account
- AWS IAM
- AWS EKS
- AWS ECR
- AWS EC2 (t2.small)
- AWS CodePipeline
- AWS CodeBuild
- K8s + Docker
- Ubuntu to Facilitate Configuration
- Kubectl CLI
- AWS CLI + AWS Credentials (Access Key + Secret Access Key)
- EKSCTL CLI
- Docker CLI

---
# STEP 1 - AWS Enviroment 1
- Create an AWS Account
- In AWS Account, select the us-east-1 to main region
- Go to the IAM:
  - Create an User in IAM
  - Add the permission for access Cloud Console and CLI
  - Add the permission: Administrator Access (It's not the best practice, but is temporarily)
  - Save the Login URL, save Access Key and Secret Access Key
  - Login into your new account
- In Ubuntu (x86 / 64-bit), download AWS CLI with these steps:
  - sudo apt-get update
  - curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  - unzip awscliv2.zip
  - sudo ./aws/install

- Create an Docker Hub Account (Save the User and Password)
- Create an cluster EKS with these steps (in this STEP it is necessary EKSCTL CLI):
  - Configure your credentials in AWS => RUN the command: aws configure
  - Create an EKS CLUSTER => RUN the command: eksctl create cluster -f EKS-Cluster.yaml
  - Wait for the Cloudformation Stack to be ready!

# STEP 2 - AWS CodeBuild: 
- Create and AWS CodeBuild project:
  - 

# STEP 3 - AWS CodePipeline:
- Create and AWS CodePipeline project:


# STEP 4 - Github Repositories:
- Create an repository with the name: hyundai-project
  - Our CodePipeline use this repository to check periodiacally updates
  - Our CodeBuild use this repository where contains the buildspec.yaml 
- Create an repository with the name: hyundai-project-hml:
  - OBS: Our buildspec.yaml script check this repository with the "-hml" for the upload of deployment
