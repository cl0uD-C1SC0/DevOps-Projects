# Problem:
<div align="center">
  <div align="center">
  Gabriel Works on an dealership. This enterprise sell cars of the Hyundai, like Hyundai HB20. Your boss ask to him to build an infrastructure to host the site of dearlership, this infrastructure it has to be scalable, available, securely and efficiently using Docker (This image contain the web site of hyundai) and Kubernetes (using concepts of HPA, replicaSet, readinessProbe and ivenessProbe) and the big requirement: Automate what you can!!!
    <div>
      <h1>Gabriel creates the "Hyundai HPA"</h1>
      <img src="Hyundai/HPA.png" width="200">
    </div>
    </div>
</div>

---
<div align="center">
      <h1>SERVICES AND TOOLS USED</h1>
</div>
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

## Before Start:
  PT-BR: Toda a arquitetura do projeto, arquivos, manifestos, scripts e configurações foram feitas por mim (José Gabriel), tentei visar as boas práticas o máximo possível, porém, é um projeto para fins de estudo e aprendizado, onde aprendi e dediquei muito para chegar até a etapa final! Obrigado por estar vendo meu projeto e fica aqui um conselho: Conecte-se comigo no LinkedIn. Basta clicar no símbolo do LinkedIn que se encontra no final deste README! Obrigado! 
  
  EN-US: The entire project architecture, files, manifest, scripts and configurations were made by me (José Gabriel), i try look to best practices the maximum possible, but, it's a study and learning project, where i learned and dedicated a lot to reach the end! Thanks for see my project and stay here for a advice: Connect to me in my LinkedIn. Just click on the linkedIn symbol in the end of this README! Thanks!

OBS: The project version currently is v1, therefore, in v2 there will be improvements, wait!

---
# STEPS IN CLOUD:
## REQUIRIMENTS
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
## STEP 1 - AWS Enviroment 1
- Create an AWS Account
- In AWS Account, select the us-east-1 to main region
- Go to the IAM:
  - Create an User in IAM
  - Add the permission for access Cloud Console and CLI
  - Add the permission: Administrator Access (It's not the best practice, but is temporarily)
  - Save the Login URL, save Access Key and Secret Access Key
  - Login into your new account
- In Ubuntu (x86 / 64-bit), download AWS CLI with these steps:
  ```
  sudo apt-get update
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  ```
- Create an Docker Hub Account (Save the User and Password)
- Create an cluster EKS with these steps (in this STEP it is necessary EKSCTL CLI):
  - Configure your credentials in AWS => RUN the command:
  ```awscli
  aws configure
  ```
  - Create an EKS CLUSTER => RUN the command:
  ```
  eksctl create cluster -f EKS-Cluster.yaml
  ```
  - Wait for the Cloudformation Stack to be ready!

## STEP 2 - Github Repositories:
- Create an repository with the name: hyundai-project
  - Our CodePipeline use this repository to check periodiacally updates
  - Our CodeBuild use this repository where contains the buildspec.yaml
  - Create other branch named "homolog"
- Create an repository with the name: hyundai-project-hml:
  - OBS: Our buildspec.yaml script check this repository with the "-hml" for the upload of deployment

## STEP 3 - AWS CodeBuild: 
- Create and AWS CodeBuild project:
  - Go to the AWS CodeBuild
  - Click on Create Project:
    - Project Name: hyundai-project
    - Source:
      - Select Github
      - Connect using OAuth
      - Copy the Repository URL, in the homolog branch, which is named: hyundai-project
    - Environment:
      - Managed Image: Ubuntu
      - Runtime: Standard
      - Image: aws/codebuild/standard:4.0
      - Mark the box: "Privileged"
      - Service Role: New Service Role
    - Buildspec:
      - Select: "Use a buildspec file"
      - Buildspec name: buildspec.yaml
    - Logs:
      - Mark the box: "Cloudwatch logs - optional"
      - Group name: hyundai-project
      - Stream name: project-
  - Create the CodeBuild Project
## STEP 4 - ECR:
- Create and AWS Elastic Container Registry
  - Go to the AWS ECR
  - Click on Get Started or create a new repository
    - Visibility:
      - Private
    - Repository Name: hyundai-project
    - Enable "Scan On Push"
  - Create Repository
## STEP 5 - AWS Secrets Manager:
- Create TWO Secrets in AWS Secrets Manager:
  - Secret ONE (1):
    - Select Other type of Secret, and put the keys and values below (replace the field "VALUE" with yours):
      - KEY: DOCKER_PASSWD
      - VALUE: your_docker_hub_password

      - KEY: DOCKER_USR
      - VALUE: your_docker_hub_user
      
    - Secret Name: DOCKER_CREDENTIALS
    - Disable the Secret Rotation and Store the new secret
   
  - Secret TWO (2):
    - Select Other type of Secret, and put the keys and values below (replace the field "VALUE" with yours):
      - KEY: GITHUB_CREDENTIAL
      - VALUE: your_github_token
      
      * E.g: ghp_exampletoken12345...
    - Secret Name: GHP_TOKEN
    - Disable the Secret Rotation and Store the new secret
## STEP 6 - AWS Identity and Access Management (IAM):
- Go to the IAM
- Search for the CodeBuild iam role where the codebuild uses
- Add the following permissions:
  - AWSCodePipelineReadOnlyAccess
  - AmazonEC2ContainerRegistryFullAccess
  - SecretsManagerReadWrite
## STEP 7 - AWS CodePipeline:
- Create and AWS CodePipeline project:
  - Go to the AWS CodePipeline
  - Click on Create pipeline
  - Choose pipeline settings:
    - Pipeline Name: hyundai-project
    - Service Role: New Service Role
  - Add source stage:
    - Source Provider: Github (Version 1)
    - Connect to the Github
    - Repository: hyundai-project
    - Branch: homolog
    - Detection Options: AWS CodePipeline
  - Add build stage:
    - Build Provider: AWS CodeBuild
    - Project Name: hyundai-project
  - Skip the Deploy provider
  - Create pipeline and test de trigger of Pipeline.
  - Wait a few minutes to start the STAGE 2 of Pipeline, and after start, click on 'Details' to check the Pipeline.
  - An Error will occur, because the repository "hyundai-project-hml" are empty.
  - Add the yaml manifests of kubernetes to "hyundai-project-hml" repository
  - Edit the image line:
    - Put the value of a new image builded and pushed to ECR Repository in AWS
    - Save the file
  - Run the pipeline again, click on release changes to look for a new errors, or, not =)
  - If pipeline run ok, commit a empty change to repository "hyundai-project" and wait for the magic happens
## STEP 8 - APPLY the newest manifests:
- Clone the "hyundai-project-hml"
- Go to the this repository and apply the manifests running: (Replace file.yaml to the files of project)
```
kubectl apply -f file.yaml
```
## STEP 9 - APPLY the HPA:
- RUN this command to apply HPA in Project:
```
kubectl autoscale deployment -n hyundai-project hyundai-project-dp --cpu-percent=50 --min=1 --max=10
```
- Wait a few minutes and RUN this command:
```
kubectl get hpa
```

---
## Thanks for seeing my project, this project it is for self-study and for my Professional Portfolio. But, for now connect to me in my LinkedIn profile to see more DevOps projects made by me and Follow-me on the Github! Thanks
<p align="center">
  <a href="https://www.linkedin.com/in/jgsiqueiraa/"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" width="120" hspace="0" vspace="15"></a>
  <a href="https://github.com/cl0uD-C1SC0"><img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white" hspace="0" vspace="15" width="100"></p>
