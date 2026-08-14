# ☸️ 03-GKE-Terraform-Runbook.md

# Google Kubernetes Engine (GKE) Deployment using Terraform

> Production-ready Terraform runbook for provisioning a Google Kubernetes Engine (GKE) cluster for the SecureBank DevSecOps Project.

---

# 📌 1. Objective

This document covers the complete Terraform deployment process for creating a production-ready Kubernetes cluster in Google Cloud Platform.

At the end of this phase, the following resources will be provisioned:

- GKE Cluster
- GKE Node Pool
- GKE Subnet
- Secondary Pod Range
- Secondary Service Range
- Workload Identity
- Kubernetes Access Configuration

---

# 🏗️ 2. Architecture

```text
Google Cloud Platform
│
├── bankingproject2027-vpc
│
├── securebank-gke-subnet
│   ├── Pod Range
│   └── Service Range
│
└── GKE Cluster
    │
    ├── securebank-gke
    │
    └── securebank-nodepool
            │
            ├── Worker Node 1
            └── Worker Node 2
```

---

# 🖥️ 3. Execution Location

| Activity | Execute On |
|-----------|------------|
| Terraform Installation | VS Code |
| Terraform Development | VS Code |
| GCP Authentication | VS Code |
| Terraform Apply | VS Code |
| Cluster Verification | VS Code |
| Kubectl Verification | VS Code |

---

# 📋 4. Prerequisites

Ensure the following are completed:

- GCP Project Created
- Billing Enabled
- VPC Created
- Subnet Created
- Service Account Created
- terraform-key.json Downloaded
- Terraform Installed
- Google Cloud SDK Installed

---

# 🔍 5. Verify Prerequisites

## Run On

```text
VS Code Terminal
```

Verify Terraform:

```bash
terraform version
```

Expected:

```text
Terraform v1.x.x
```

---

Verify GCloud:

```bash
gcloud version
```

Expected:

```text
Google Cloud SDK
```

---

Verify Authentication:

```bash
gcloud auth list
```

---

Verify Project:

```bash
gcloud config list
```

Expected:

```text
bankingproject2027
```

---

# 📁 6. Project Structure

## Run On

```text
VS Code
```

Create:

```text
04/Gke-Terraform/

├── provider.tf
├── variables.tf
├── terraform.tfvars
├── main.tf
├── outputs.tf
└── terraform-key.json
```

---

# ⚙️ 7. Provider Configuration

## provider.tf

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 6.0"
    }
  }
}

provider "google" {
  credentials = file("terraform-key.json")

  project = var.project_id
  region  = var.region
  zone    = var.zone
}
```

---

# 📝 8. Variable Definitions

## variables.tf

```hcl
variable "project_id" {
  type = string
}

variable "region" {
  type = string
}

variable "zone" {
  type = string
}

variable "network_name" {
  type = string
}

variable "gke_subnet_name" {
  type = string
}

variable "gke_subnet_cidr" {
  type = string
}

variable "pods_secondary_range" {
  type = string
}

variable "services_secondary_range" {
  type = string
}

variable "cluster_name" {
  type = string
}

variable "node_count" {
  type = number
}

variable "machine_type" {
  type = string
}
```

---

# 📝 9. Terraform Variables

## terraform.tfvars

```hcl
project_id = "bankingproject2027"

region = "us-central1"

zone = "us-central1-a"

network_name = "bankingproject2027-vpc"

gke_subnet_name = "securebank-gke-subnet"

gke_subnet_cidr = "10.20.0.0/24"

pods_secondary_range = "10.30.0.0/16"

services_secondary_range = "10.40.0.0/20"

cluster_name = "securebank-gke"

node_count = 2

machine_type = "e2-standard-2"
```

---

# 🚀 10. Main Terraform Configuration

## main.tf

```hcl
data "google_compute_network" "existing_vpc" {
  name = var.network_name
}

resource "google_compute_subnetwork" "securebank_gke_subnet" {

  name          = var.gke_subnet_name
  ip_cidr_range = var.gke_subnet_cidr

  region  = var.region
  network = data.google_compute_network.existing_vpc.id

  secondary_ip_range {
    range_name    = "securebank-pods-range"
    ip_cidr_range = var.pods_secondary_range
  }

  secondary_ip_range {
    range_name    = "securebank-services-range"
    ip_cidr_range = var.services_secondary_range
  }
}

resource "google_container_cluster" "securebank_gke" {

  name     = var.cluster_name
  location = var.zone

  network    = data.google_compute_network.existing_vpc.name
  subnetwork = google_compute_subnetwork.securebank_gke_subnet.name

  remove_default_node_pool = true

  initial_node_count = 1

  networking_mode = "VPC_NATIVE"

  release_channel {
    channel = "REGULAR"
  }

  workload_identity_config {
    workload_pool = "${var.project_id}.svc.id.goog"
  }

  ip_allocation_policy {
    cluster_secondary_range_name  = "securebank-pods-range"
    services_secondary_range_name = "securebank-services-range"
  }

  deletion_protection = false
}

resource "google_container_node_pool" "securebank_nodepool" {

  name     = "securebank-nodepool"
  cluster  = google_container_cluster.securebank_gke.name
  location = var.zone

  node_count = var.node_count

  node_config {

    machine_type = var.machine_type

    disk_size_gb = 50

    disk_type = "pd-balanced"

    oauth_scopes = [
      "https://www.googleapis.com/auth/cloud-platform"
    ]

    labels = {
      application = "securebank"
      environment = "dev"
      managed_by  = "terraform"
    }

    tags = [
      "securebank",
      "gke-node"
    ]
  }

  management {
    auto_repair  = true
    auto_upgrade = true
  }
}
```

---

# 📤 11. Outputs

## outputs.tf

```hcl
output "cluster_name" {
  value = google_container_cluster.securebank_gke.name
}

output "cluster_endpoint" {
  value = google_container_cluster.securebank_gke.endpoint
}

output "cluster_location" {
  value = google_container_cluster.securebank_gke.location
}

output "node_pool_name" {
  value = google_container_node_pool.securebank_nodepool.name
}

output "gke_subnet_name" {
  value = google_compute_subnetwork.securebank_gke_subnet.name
}
```

---

# 🔐 12. Authenticate Terraform

## Run On

```text
VS Code Terminal
```

```bash
gcloud auth activate-service-account \
--key-file=terraform-key.json
```

Verify:

```bash
gcloud auth list
```

---

# 🚀 13. Deploy Infrastructure

## Run On

```text
VS Code Terminal
```

Navigate:

```bash
cd 04/Gke-Terraform
```

Format:

```bash
terraform fmt
```

Initialize:

```bash
terraform init
```

Validate:

```bash
terraform validate
```

Plan:

```bash
terraform plan
```

Deploy:

```bash
terraform apply
```

Type:

```text
yes
```

Expected:

```text
Apply complete!
```

---

# 🔍 14. Verify Resources

## Run On

```text
VS Code Terminal
```

```bash
terraform state list
```

Expected:

```text
google_compute_subnetwork.securebank_gke_subnet

google_container_cluster.securebank_gke

google_container_node_pool.securebank_nodepool
```

---

# ☸️ 15. Configure Kubectl

## Run On

```text
VS Code Terminal
```

```bash
gcloud container clusters get-credentials securebank-gke \
--zone us-central1-a \
--project bankingproject2027
```

---

# 🔍 16. Verify Kubernetes Cluster

```bash
kubectl get nodes
```

Expected:

```text
NAME                                   STATUS   ROLES
gke-securebank-nodepool-xxxxx          Ready
gke-securebank-nodepool-yyyyy          Ready
```

---

# 🔍 17. Verify Cluster Information

```bash
kubectl cluster-info
```

Expected:

```text
Kubernetes control plane is running
```

---

# 🔍 18. Verify Namespaces

```bash
kubectl get ns
```

Expected:

```text
default
kube-system
kube-public
```

---

# 📊 19. Verify GKE Resources

```bash
gcloud container clusters list
```

Expected:

```text
securebank-gke
```

---

# 🧹 20. Destroy Infrastructure (Optional)

## Run On

```text
VS Code Terminal
```

```bash
terraform destroy
```

Type:

```text
yes
```

---

# 🚨 Common Troubleshooting

## Authentication Error

Verify:

```bash
gcloud auth list
```

---

## Provider Error

Verify:

```bash
terraform init -upgrade
```

---

## Cluster Creation Failed

Verify APIs:

```bash
gcloud services list
```

Required:

```text
container.googleapis.com
compute.googleapis.com
iam.googleapis.com
```

---

## Kubectl Not Working

Verify:

```bash
kubectl version --client
```

Reconnect:

```bash
gcloud container clusters get-credentials securebank-gke \
--zone us-central1-a \
--project bankingproject2027
```

---

# ✅ Validation Checklist

## Terraform

- [ ] Terraform Installed
- [ ] Terraform Initialized
- [ ] Terraform Applied

## GKE

- [ ] Cluster Created
- [ ] Node Pool Created
- [ ] Subnet Created

## Kubernetes

- [ ] Kubectl Configured
- [ ] Nodes Available
- [ ] Cluster Accessible

---

# 📌 Expected Outcome

At the end of this phase:

✅ GKE Cluster Running

✅ Node Pool Running

✅ Kubernetes Access Configured

✅ Kubectl Connected

✅ Ready for Kubernetes Deployment

---

# Next Document

```text
04-Kubernetes-Deployment.md
```
