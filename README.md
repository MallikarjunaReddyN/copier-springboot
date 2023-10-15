# Spring Boot Template

A [Copier](https://github.com/copier-org/copier) template for a Spring Boot projects.

This template aims at making it easier to configure spring boot projects with **Java 17, checkstyle, databases (MySQL and PostgreSQL), docker, continous integration (Github Actions and Azure Pipelines), sonarqube, Helm chart and ArgoCD (Supports with Github Actions only)**. 

### Prerequisites

****

##### For generating template:

- **Python** - [Download Python | Python.org](https://www.python.org/downloads/)

- **copier** - [copier · PyPI](https://pypi.org/project/copier/3.1.0/)

##### For running generated project:

- Java - [Java Archive Downloads - Java SE 17](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html)

- Docker - [Docker Desktop](https://www.docker.com/products/docker-desktop/)

- Intellij (Optional) - [IntelliJ IDEA – the Leading Java and Kotlin IDE](https://www.jetbrains.com/idea/)

### Usage

****

- Create a new project folder, for instance `example-service`.

- Open your favourite terminal at newly created folder location.

- Run below command:
  
  for windows:
  
  ```
  copier copy --vcs-ref main https://github.com/MallikarjunaReddyN/copier-springboot.git .
  (or)
  py -m copier copy --vcs-ref main https://github.com/MallikarjunaReddyN/copier-springboot.git .
  ```
  
  for macOS and linux:
  
  ```
  copier copy --vcs-ref main https://github.com/MallikarjunaReddyN/copier-springboot.git .
  ```

- And answer the questions.

### Publish project on github

****

- Create repository
  
  Add below secrets before pushing if **CI  type** is **Github actions**:
  
  - SONAR_HOST_URL
  
  - SONAR_TOKEN
  
  - DOCKER_USERNAME
  
  - DOCKER_PASSWORD
  
  - AZURE_CREDENTIALS (The entire JSON output from the `az ad sp create-for-rbac` command.)

          Secrets & variableslocation: 

            Repo level: select repository -> settings -> Secrets and variables -> Actions

            Org level: select orgnization ->s settings -> Secrets and variables -> Actions

- Configure below variables if **CI type** is **Azure Pipelines**:
  
  - registery.username
  
  - registery.password
  
  and create new service connection for sonarqube with name "sonarcloud"

### AKS Deployment with Github actions

##### Prerequisites

- Azure account

- AKS cluster

- Azure CLI

- Kubectl

Steps:

- Login into azure by running below command:
  
  ```
  az login
  ```

- Create a service principal to access your resource group with the `Contributor` role using the [`az ad sp create-for-rbac`](https://learn.microsoft.com/en-us/cli/azure/ad/sp#az-ad-sp-create-for-rbac) command.
  
  ```
  az ad sp create-for-rbac \
      --name "<SERVICE_PRICIPAL_NAME" \
      --scope /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> \
      --role Contributor \
      --sdk-auth
  ```

- Get cluster details by running below command:
  
  ```
  az aks get-credentials --resource-group <YOUR_RESOURCEGROUP> --name <YOUR_AKS_CLUSTER_NAME>
  ```

- Install Ingress in cluster using below kubectl command:
  
  ```
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/cloud/deploy.yaml
  ```

- Install Let's Encrypt Cert Manager with below command:
  
  ```
  kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.9.1/cert-manager.yaml
  ```

- Create ClusterIssuer resource using below snippet:
  
  ```yaml
  apiVersion: cert-manager.io/v1
  kind: ClusterIssuer
  metadata:
    name: letsencrypt
    namespace: cert-manager
  spec:
    acme:
      server: https://acme-v02.api.letsencrypt.org/directory
      email: <YOUR_EMAIL_ADDRESS>
      privateKeySecretRef:
        name: letsencrypt
      solvers:
      - http01:
          ingress:
            class: nginx
  ```

Reference links:

- [Build, test, and deploy containers to Azure Kubernetes Service (AKS) using GitHub Actions - Azure Kubernetes Service | Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/kubernetes-action)
