# Azure Kubernetes Service (AKS) Deployment using Terraform

## Overview

This guide provides step-by-step instructions to set up an Azure Kubernetes Service (AKS) cluster using Terraform and deploy a simple Nginx application.

## Prerequisites

Ensure you have the following installed:

- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)  
- [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli)  
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)  
- [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)  
- [Docker](https://docs.docker.com/get-docker/)  
- [VS Code](https://code.visualstudio.com/Download) (or any code editor)

## Step 1: Setting Up the AKS Cluster using Terraform

### 1.1 Login to Azure CLI

```bash
az login
```

### 1.2 Set up a Terraform project directory

```bash
mkdir aks-terraform && cd aks-terraform
```

### 1.3 Create a Terraform configuration file (`main.tf`)

Create the `main.tf` file using the following command:

```bash
# Windows
notepad main.tf

# Linux/Mac
nano main.tf
```

Add the following content:

```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  subscription_id = "your-subscription-id"
  features {}
}

resource "azurerm_resource_group" "aks_rg" {
  name     = "aks-resource-group"
  location = "East US"
}

resource "azurerm_kubernetes_cluster" "aks_cluster" {
  name                = "myAKSCluster"
  location            = azurerm_resource_group.aks_rg.location
  resource_group_name = azurerm_resource_group.aks_rg.name
  dns_prefix          = "myaksdns"

  default_node_pool {
    name       = "default"
    node_count = 2
    vm_size    = "Standard_DS2_v2"
  }

  identity {
    type = "SystemAssigned"
  }
}
```

### 1.4 Initialize and Apply Terraform

```bash
terraform init
terraform apply -auto-approve
```

### 1.5 Configure `kubectl` to access the AKS cluster

```bash
az aks get-credentials --resource-group aks-resource-group --name myAKSCluster
```

## Step 2: Deploy a Simple Nginx Application

### 2.1 Create Kubernetes Deployment YAML (`deployment.yaml`)

Create the `deployment.yaml` file:

```bash
# Windows
notepad deployment.yaml

# Linux/Mac
nano deployment.yaml
```

Add the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

### 2.2 Create Kubernetes Service YAML (`service.yaml`)

Create the `service.yaml` file:

```bash
# Windows
notepad service.yaml

# Linux/Mac
nano service.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### 2.3 Apply the Kubernetes YAML files

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

## Step 3: Verify Deployment

### 3.1 Check pod status

```bash
kubectl get pods
```

### 3.2 Get the external IP address of the service

```bash
kubectl get service nginx-service
```

### 3.3 Test the application

```bash
curl http://<EXTERNAL_IP>
```

Or open it in a browser.


## Troubleshooting

### Issue: `Subscription ID error`

**Fix:** Add your Azure Subscription ID in `main.tf` under the provider block.

### Issue: Terraform apply hangs indefinitely

**Fix:**

```bash
# Windows
set TF_LOG=TRACE

# Linux/Mac
export TF_LOG=TRACE
terraform apply
```

### Issue: Service external IP is pending

**Fix:**

```bash
kubectl get events --sort-by='.lastTimestamp'
kubectl describe service nginx-service
```

## Learning Resources

- 📌 [Terraform on Azure](https://learn.hashicorp.com/collections/terraform/azure)
- 📌 [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- 📌 [Azure Kubernetes Service (AKS) Quickstart](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough-portal)



