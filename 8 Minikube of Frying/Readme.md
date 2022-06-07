# Problem:
<div align="center">
  <div align="center">
  Gabriel works on an big enterprise, where have a big variety of clients around the globe. Your boss did a request where your objective is creates an web app which    print an environment variable in web using tools like Docker Images and Kubernetes. But, this is not the objective principale, the architecture is a local machine using an tool to create a k8s cluster, like minikube, this architecture it has to be resilient, scalable, and the code has to be confiable and securely, the app goes to web port 80 and you has to make the use of an tool makes easy understand the app and view (Like ArgoCD) or others.
    <div>
      <h1>Gabriel creates the "Minikube of Frying"</h1>
      <img src="minikube/Minikube (1).png" width="200">
    </div>
    </div>
</div>

---
# Steps in Local Machine:
## REQUIREMENTS:

* minikube
* minikube ingress
* Sonnar-Scanner CLI
* kubectl
* helm
* docker
  
# MINIKUBE Installation (LINUX):
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# MINIKUBE Ingress:
minikube addons enable ingress

# Kubectl Install (Linux):
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

chmod +x kubectl
  
mkdir -p ~/.local/bin
  
mv ./kubectl ~/.local/bin/kubectl

# HELM Installation (Linux):
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

chmod 700 get_helm.sh

./get_helm.sh
  
# DOCKER Installation (Linux):
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Apply Manifests:

kubectl apply -f challenge-ns.yml

kubectl apply -f challenge-cfg.yml

kubectl apply -f challenge-dp.yml

kubectl apply -f minikube/minikube-svc.yml

kubectl apply -f minikube/minikube-in.yml
  
 Access the > http://localhost:80

# SonarQube Installation with HELM:
helm repo add sonarqube https://SonarSource.github.io/helm-chart-sonarqube
  
helm repo update
  
kubectl create namespace sonarqube
  
helm upgrade --install -n sonarqube sonarqube sonarqube/sonarqube

Access the > http://localhost:9000
  
LINK: https://docs.sonarqube.org/latest/setup/sonarqube-on-kubernetes/

# ArgoCD with HELM:

helm repo add argo https://argoproj.github.io/argo-helm

helm install my-release argo/argo-cd

Access the > http://localhost:8080

LINK: https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd

# Docker Commands to Docker Hub
1 - sudo docker tag web:latest josegabriel/web:latest
  
2 - sudo docker login
  
3 - sudo docker push josegabriel/web:latest
    
# Helpful commands:

In Cloud Step, install SonarQube in Cluster k8s and edit the file of svc to make accessible from internet with this command:
> kubectl edit svc -n sonarqube sonarqube-sonarqube (Put type to LoadBalancer)

To gain more efficience in local machine, add a Docker Container with SonarQube with these commands:
> docker run -d --name sonarqube -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true -p 9000:9000 sonarqube:latest
> Access the > http://localhost:9000/

To make a Scan with sonar-scanner cli, copy this command (This command is to other progamming langugages like PHP)
```shell
sonar-scanner \
-Dsonar.projectKey=$SONAR_PROJECT \
-Dsonar.sources=. \
-Dsonar.host.url=http://localhost:9000 \
-Dsonar.login=$SONAR_TOKEN
```
> OBS: These commands above, $SONAR_PROJECT and $SONAR_TOKEN are variables created by me with values of my project name and my sonar token ID
  
To put HPA (Horizontal Pod Autoscalling) in LocalMachine with Minikube, copy and paste these commands:
> minikube addons enable metrics-server
  
> kubectl autoscale deployment -n challenge-projeto challenge-deploy --cpu-percent=20 --min=1 --max=10

OBS: Frying is because the computer that was used was frying with the use of minikube and other applications
