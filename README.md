# Azure Kubernetes Service (AKS) Deployment using Terraform

## Overview
This guide provides step-by-step instructions to set up an Azure Kubernetes Service (AKS) cluster using Terraform and deploy a simple Nginx application.

## Prerequisites
Ensure you have the following installed:
Azure CLI (Install Guide)
Terraform (Install Guide)
kubectl (Install Guide)
Git (Install Guide)
Docker (Install Guide)
VS Code (or any code editor) (Download VS Code)

## Step 1: Setting Up the AKS Cluster using Terraform

### 1.1 Login to Azure CLI
    az login

### 1.2 Set up a Terraform project directory
    mkdir aks-terraform && cd aks-terraform

### 1.3 Create a Terraform configuration file (main.tf)
    Create the main.tf file using the following command:

    notepad main.tf  # Windows
    nano main.tf  # Linux/Mac

Add the following content:


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


### 1.4 Initialize and Apply Terraform

terraform init
terraform apply -auto-approve

### 1.5 Configure kubectl to access the AKS cluster

az aks get-credentials --resource-group aks-resource-group --name myAKSCluster

## Step 2: Deploy a Simple Nginx Application

### 2.1 Create Kubernetes Deployment YAML (deployment.yaml)

Create the deployment.yaml file:

notepad deployment.yaml  # Windows
nano deployment.yaml  # Linux/Mac

Add the following content:

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

### 2.2 Create Kubernetes Service YAML (service.yaml)

Create the service.yaml file:

notepad service.yaml  # Windows
nano service.yaml  # Linux/Mac

Add the following content:

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

### 2.3 Apply the Kubernetes YAML files

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

## Step 3: Verify Deployment

### 3.1 Check pod status

kubectl get pods

### 3.2 Get the external IP address of the service

kubectl get service nginx-service

### 3.3 Test the application

curl http://<EXTERNAL_IP>

Or open it in a browser.

## Step 4: Upload Project to GitHub

### 4.1 Initialize Git and Commit Files

git init
git add .
git commit -m "Initial commit"

### 4.2 Create a GitHub Repository and Push the Code

git remote add origin <your-repo-url>
git branch -M main
git push -u origin main

## Troubleshooting

Issue: subscription_id error

Fix: Add your Azure Subscription ID in main.tf under the provider block.

Issue: Terraform apply hangs indefinitely

Fix:

set TF_LOG=TRACE  # Windows
export TF_LOG=TRACE  # Linux/Mac
terraform apply

Issue: Service external IP is pending

Fix:

kubectl get events --sort-by='.lastTimestamp'
kubectl describe service nginx-service

Learning Resources

📌 Terraform on Azure
📌 Kubernetes Basics
📌 Azure Kubernetes Service (AKS) Quickstart

Conclusion

Following this guide, you have successfully:
✅ Created an AKS cluster using Terraform
✅ Deployed a simple Nginx application
✅ Verified that the application is running
✅ Uploaded your project to GitHub

If you encounter any issues, refer to the troubleshooting section or check the provided learning resources. 🚀

