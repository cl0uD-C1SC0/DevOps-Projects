# Challenge 02 - Print an environment variable in Web APP

# Objective:
Resumo: Deverá ser feito uma pagina web que exiba no corpo somente a mensagem Olá <nome>, este nome será um valor
que será consumido via variavel de ambiente pela aplicação. Poderá ser utilizado qualquer linguagem de programação. Após a página pronta, conteinerizar esta aplicação com docker e montar manifestos kubernetes (Service, Configmap[A variavel vai aqui], Deployment[com resources], Namespace); Montar escalonamento horizontal para o container, que, quando chegar a 20% de uso do CPU, escalar outro container, com um minimo de containers 1 e maximo 10. O kubernetes deve ser utilizado na PROPRIA maquina, ou seja, é necessário instanciar um kubernetes com minikube ou K3D ou ferramenta parecida.
Colocar os manifestos em um repositório git.
Instanciar um ArgoCD via Docker na própria maquina, adicionar o cluster e o repositório no ArgoCD e então criar um APP no ArgoCD para a aplicação. (A Ideia aqui, é que quando houver uma alteração no repositório, o ArgoCD altere o cluster local com as alterações feitas no repositório git).

---

# STEPS in Cloud:

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
