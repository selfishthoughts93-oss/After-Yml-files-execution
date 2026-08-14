# 🚀 09-Project-Execution-Runbook.md

# End-to-End DevSecOps Project Execution Runbook

> Production-ready execution guide for deploying the SecureBank Application using Google Cloud Platform, Terraform, Ansible, Jenkins, SonarQube, Docker, Kubernetes, Prometheus, and Grafana.

---

# 📌 1. Objective

This document provides the complete execution flow of the project from infrastructure provisioning to application monitoring.

This runbook should be followed sequentially.

At the end of this execution:

✅ Infrastructure Created

✅ Servers Configured

✅ GKE Cluster Running

✅ Application Deployed

✅ CI/CD Pipeline Working

✅ Monitoring Enabled

✅ Production Validation Completed

---

# 🏗️ 2. Complete Architecture

```text
Developer
    |
    v
GitHub Repository
    |
    v
Jenkins Pipeline
    |
    +-------------------+
    |                   |
    v                   v
 SonarQube         Docker Build
 Analysis               |
    |                   |
    +---------+---------+
              |
              v
         DockerHub
              |
              v
      Kubernetes Deployment
              |
              v
          GKE Cluster
              |
              v
     SecureBank Application
              |
              v
   Prometheus + Grafana
```

---

# 🖥️ 3. Environment Overview

| Server | Purpose |
|----------|----------|
| Jenkins VM | CI/CD Server |
| SonarQube VM | Code Quality Server |
| Monitoring VM | Prometheus & Grafana |
| GKE Cluster | Application Hosting |
| GitHub | Source Code Repository |
| DockerHub | Image Repository |

---

# 📋 4. Execution Sequence

Execute documents in the following order:

```text
01-GCP-Infrastructure.md

↓

02-Ansible-Runbook.md

↓

03-GKE-Terraform-Runbook.md

↓

04-Kubernetes-Deployment.md

↓

05-Jenkins-SonarQube-Integration.md

↓

06-Complete-CICD-Pipeline.md

↓

07-Monitoring-Prometheus-Grafana.md

↓

08-Troubleshooting-Guide.md

↓

09-Project-Execution-Runbook.md
```

---

# 🚀 Phase 1 – GCP Infrastructure Setup

## Execute On

```text
Google Cloud Console
```

Create:

```text
Project

VPC

Subnet

Firewall Rules

Jenkins VM

SonarQube VM

Monitoring VM

Service Account
```

Verify:

```text
Compute Engine
```

Expected:

```text
jenkins-server

sonarqube-server

monitoring-server
```

Status:

```text
Running
```

---

# 🚀 Phase 2 – Configure Servers Using Ansible

## Execute On

```text
Ansible Control Node / VS Code
```

Run:

```bash
ansible-playbook -i hosts 02.Basic-Packages-install.yaml

ansible-playbook -i hosts 03-dockerinstall.yaml

ansible-playbook -i hosts 05.sonarqube-install.yaml

ansible-playbook -i hosts 06.Jenkins-install.yml

ansible-playbook -i hosts 04-monitoring.yaml

ansible-playbook -i hosts monitoring-node-exporter-installation.yml
```

Verify:

```bash
ansible all -i hosts -m ping
```

Expected:

```text
SUCCESS
```

---

# 🚀 Phase 3 – Deploy GKE Using Terraform

## Execute On

```text
VS Code
```

Navigate:

```bash
cd terraform
```

Run:

```bash
terraform fmt

terraform init

terraform validate

terraform plan

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

# 🚀 Phase 4 – Configure Kubernetes Access

## Execute On

```text
VS Code
```

Configure:

```bash
gcloud container clusters get-credentials securebank-gke \
--zone us-central1-a \
--project bankingproject2027
```

Verify:

```bash
kubectl get nodes
```

Expected:

```text
Ready
Ready
```

---

# 🚀 Phase 5 – Deploy Kubernetes Resources

## Execute On

```text
VS Code
```

Deploy Namespace:

```bash
kubectl apply -f k8s/namespace.yaml
```

Deploy Application:

```bash
kubectl apply -f k8s/deployment.yaml
```

Deploy Service:

```bash
kubectl apply -f k8s/service.yaml
```

Verify:

```bash
kubectl get all -n securebank
```

Expected:

```text
Pods Running

Deployment Available

Service Available
```

---

# 🚀 Phase 6 – Configure Jenkins

## Execute On

```text
Browser
```

Open:

```text
http://JENKINS-IP:8080
```

Install:

```text
Suggested Plugins
```

Create:

```text
Admin User
```

Verify Dashboard Access.

---

# 🚀 Phase 7 – Configure SonarQube

## Execute On

```text
Browser
```

Open:

```text
http://SONAR-IP:9000
```

Create:

```text
Project : securebank
```

Generate:

```text
Token : jenkins-sonar-token
```

Save Token.

---

# 🚀 Phase 8 – Integrate Jenkins and SonarQube

## Execute On

```text
Jenkins UI
```

Configure:

```text
Manage Jenkins

→ System

→ SonarQube Servers
```

Add:

```text
SonarQube Server

Token
```

Verify:

```text
Test Connection
```

Expected:

```text
Success
```

---

# 🚀 Phase 9 – Configure DockerHub

## Execute On

```text
DockerHub Website
```

Create:

```text
Personal Access Token
```

Add to Jenkins:

```text
Manage Jenkins

→ Credentials

→ Global

→ dockerhub-creds
```

Verify:

```text
Credential Saved
```

---

# 🚀 Phase 10 – Configure GitHub Integration

## Execute On

```text
GitHub
```

Push:

```text
Source Code

Dockerfile

Jenkinsfile

Kubernetes YAML Files
```

Verify:

```bash
git push origin main
```

Success:

```text
Everything up-to-date
```

---

# 🚀 Phase 11 – Create Jenkins Pipeline

## Execute On

```text
Jenkins UI
```

Create:

```text
SecureBank-Pipeline
```

Pipeline Source:

```text
Pipeline Script from SCM
```

Repository:

```text
GitHub Repository URL
```

Branch:

```text
main
```

Save.

---

# 🚀 Phase 12 – Execute CI/CD Pipeline

## Execute On

```text
Jenkins UI
```

Click:

```text
Build Now
```

Pipeline Stages:

```text
Git Checkout

Maven Build

SonarQube Analysis

Quality Gate

Docker Build

Docker Login

Docker Push

Kubernetes Deployment
```

Expected:

```text
SUCCESS
```

---

# 🚀 Phase 13 – Verify Deployment

## Execute On

```text
Jenkins VM
```

Verify:

```bash
kubectl get deployment -n securebank

kubectl get pods -n securebank

kubectl get svc -n securebank
```

Expected:

```text
Running
```

---

# 🚀 Phase 14 – Access Application

## Execute On

```text
Browser
```

Get External IP:

```bash
kubectl get svc -n securebank
```

Example:

```text
34.xx.xx.xx
```

Open:

```text
http://34.xx.xx.xx
```

Expected:

```text
SecureBank Application Home Page
```

---

# 🚀 Phase 15 – Configure Monitoring

## Execute On

```text
Monitoring VM
```

Verify:

```bash
docker ps
```

Expected:

```text
prometheus

grafana
```

---

# 🚀 Phase 16 – Verify Prometheus

## Execute On

```text
Browser
```

Open:

```text
http://MONITORING-IP:9090
```

Navigate:

```text
Status

→ Targets
```

Expected:

```text
UP

UP

UP
```

---

# 🚀 Phase 17 – Verify Grafana

## Execute On

```text
Browser
```

Open:

```text
http://MONITORING-IP:3000
```

Default:

```text
admin/admin
```

Add Datasource:

```text
Prometheus
```

Import Dashboard:

```text
1860
```

Expected:

```text
Node Exporter Full Dashboard
```

---

# 📊 Final Validation Commands

## Kubernetes

```bash
kubectl get all -n securebank
```

---

## Nodes

```bash
kubectl get nodes
```

---

## Services

```bash
kubectl get svc -n securebank
```

---

## Jenkins

```bash
sudo systemctl status jenkins
```

---

## Docker

```bash
docker ps
```

---

## Prometheus

```bash
curl http://localhost:9090/-/healthy
```

---

## Grafana

```bash
curl http://localhost:3000/api/health
```

---

# 📋 Production Readiness Checklist

## Infrastructure

- [ ] Project Created
- [ ] VPC Created
- [ ] Firewall Rules Configured

## Compute

- [ ] Jenkins VM Running
- [ ] SonarQube VM Running
- [ ] Monitoring VM Running

## Terraform

- [ ] GKE Cluster Created
- [ ] Node Pool Created

## Kubernetes

- [ ] Namespace Created
- [ ] Deployment Running
- [ ] Service Exposed

## CI/CD

- [ ] GitHub Connected
- [ ] Jenkins Pipeline Successful
- [ ] Docker Image Pushed

## Quality

- [ ] SonarQube Analysis Successful
- [ ] Quality Gate Passed

## Monitoring

- [ ] Prometheus Running
- [ ] Grafana Running
- [ ] Dashboard Imported

## Application

- [ ] Application Accessible
- [ ] No Pod Errors
- [ ] No Deployment Errors

---

# 🎯 Project Outcome

Successfully implemented:

```text
Google Cloud Platform

Terraform

Ansible

Docker

Jenkins

SonarQube

Kubernetes

GKE

Prometheus

Grafana

CI/CD Automation

Monitoring & Observability
```

---

# 🏆 Final Deliverables

```text
01-GCP-Infrastructure.md

02-Ansible-Runbook.md

03-GKE-Terraform-Runbook.md

04-Kubernetes-Deployment.md

05-Jenkins-SonarQube-Integration.md

06-Complete-CICD-Pipeline.md

07-Monitoring-Prometheus-Grafana.md

08-Troubleshooting-Guide.md

09-Project-Execution-Runbook.md
```

---

# ✅ Project Status

```text
END-TO-END DEVSECOPS PROJECT COMPLETED SUCCESSFULLY
```
