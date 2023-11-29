### Publish project on github

****

- Create repository
  
  Add below secrets before pushing if **CI  type** is **Github actions**:
  
  - SONAR_HOST_URL
  
  - SONAR_TOKEN
  
  - REGISTRY_USERNAME
  
  - REGISTRY_PASSWORD
  
  - AZURE_CREDENTIALS (The entire JSON output from the `az ad sp create-for-rbac` command.)

          Secrets & variableslocation: 

            Repo level: select repository -> settings -> Secrets and variables -> Actions

            Org level: select orgnization ->s settings -> Secrets and variables -> Actions


### AKS Deployment with Github actions

****

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

  ```
  kubectl apply -f cluster-issuer.yaml
  ```

### AKS Deployment with Azure Pipelines

****

- Create AKS and Azure container registery(ACR)

- Attach ACR with AKS

- Create Azure pipeline envirnoment with Kubernetes resource.

- Update your Envirnoment name and AKS namespace which you selected or created at time of envirnoment creation in azure-pipelines.yaml file.

- Create sonarqube/snoarcloud service connection for sonar scan and update service connection name in Sonar Scan task.

- Create variable group with name `registry-credentials` and add two variable in that variable group with REGISTERY_USERNAME, REGISTERY_PASSWORD

- Create variable group to store DOMAIN variables for Ingress host.
  
  In my case I created `Dev` for development, `QA` for testing and `Prod` for production.  

Reference links:

- [Build, test, and deploy containers to Azure Kubernetes Service (AKS) using GitHub Actions - Azure Kubernetes Service | Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/kubernetes-action)

- [GitHub - Azure/k8s-deploy: GitHub Action for deploying to Kubernetes clusters](https://github.com/Azure/k8s-deploy)

- [Deploy to Azure Kubernetes Service with Azure Pipelines - Azure Kubernetes Service | Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/devops-pipeline?tabs=cli&pivots=pipelines-yaml)

- [KubernetesManifest@0 - Deploy to Kubernetes v0 task | Microsoft Learn](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/kubernetes-manifest-v0?view=azure-pipelines)
