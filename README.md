# Azure Kubernetes Service (AKS) Deployment using Terraform

## Overview

This guide provides step-by-step instructions to set up an Azure Kubernetes Service (AKS) cluster using Terraform and deploy a simple Nginx application.

## Prerequisites

Before starting, make sure you have the following tools installed:

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
