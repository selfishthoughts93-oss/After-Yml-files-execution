# 🛠️ 08-Troubleshooting-Guide.md

# Complete Troubleshooting Guide

> Production-ready troubleshooting guide for the SecureBank DevSecOps Project running on Google Cloud Platform, GKE, Jenkins, SonarQube, Docker, Kubernetes, Prometheus, and Grafana.

---

# 📌 1. Objective

This guide provides solutions for the most common issues encountered during:

- GCP Infrastructure Setup
- Ansible Automation
- Terraform Deployment
- GKE Cluster Creation
- Kubernetes Deployment
- Jenkins Configuration
- SonarQube Integration
- Docker Build & Push
- CI/CD Pipeline Execution
- Prometheus Monitoring
- Grafana Dashboard Configuration

---

# 🖥️ 2. Infrastructure Troubleshooting

---

## Issue: Cannot SSH into VM

### Error

```text
Permission denied (publickey)
```

### Verify

Run:

```bash
ssh USER@SERVER-IP
```

Check:

```bash
ls -la ~/.ssh
```

### Fix

Generate Key:

```bash
ssh-keygen -t ed25519
```

Copy Key:

```bash
ssh-copy-id USER@SERVER-IP
```

Verify:

```bash
ssh USER@SERVER-IP
```

---

## Issue: VM Not Reachable

### Verify Firewall

```text
GCP Console
→ VPC Network
→ Firewall
```

Required Port:

```text
22
```

### Verify VM

```text
Compute Engine
→ VM Instances
```

Status:

```text
Running
```

---

# ⚙️ 3. Ansible Troubleshooting

---

## Issue: Host Unreachable

### Error

```text
UNREACHABLE
```

### Verify

```bash
ansible all -i hosts -m ping
```

### Fix

Check Inventory:

```bash
cat hosts
```

Verify SSH:

```bash
ssh USER@SERVER-IP
```

---

## Issue: Python Not Found

### Error

```text
Python Interpreter Discovery Failed
```

### Fix

Install Python:

```bash
sudo apt update

sudo apt install python3 -y
```

Verify:

```bash
python3 --version
```

---

# ☸️ 4. Terraform Troubleshooting

---

## Issue: Terraform Not Found

### Error

```text
terraform: command not found
```

### Fix

Verify:

```bash
terraform version
```

Reinstall Terraform.

---

## Issue: Authentication Failed

### Error

```text
googleapi: Error 403
```

### Verify

```bash
gcloud auth list
```

### Fix

```bash
gcloud auth activate-service-account \
--key-file=terraform-key.json
```

---

## Issue: API Not Enabled

### Error

```text
Kubernetes Engine API has not been used
```

### Fix

Enable APIs:

```text
Kubernetes Engine API

Compute Engine API

IAM API
```

---

# ☸️ 5. GKE Troubleshooting

---

## Issue: kubectl Not Working

### Error

```text
Unable to connect to server
```

### Verify

```bash
kubectl config current-context
```

### Fix

```bash
gcloud container clusters get-credentials securebank-gke \
--zone us-central1-a \
--project bankingproject2027
```

Verify:

```bash
kubectl get nodes
```

---

## Issue: Nodes Not Ready

### Verify

```bash
kubectl get nodes
```

### Check Details

```bash
kubectl describe node NODE_NAME
```

---

# 🚀 6. Kubernetes Troubleshooting

---

## Issue: Pod Pending

### Verify

```bash
kubectl get pods -n securebank
```

### Describe

```bash
kubectl describe pod POD_NAME -n securebank
```

### Common Causes

```text
Insufficient CPU

Insufficient Memory

Image Pull Failure
```

---

## Issue: ImagePullBackOff

### Verify

```bash
kubectl describe pod POD_NAME -n securebank
```

### Fix

Check Image:

```bash
docker pull devopsbyrushi/securebank:latest
```

Verify DockerHub Repository.

---

## Issue: CrashLoopBackOff

### Verify Logs

```bash
kubectl logs POD_NAME -n securebank
```

### Check Events

```bash
kubectl describe pod POD_NAME -n securebank
```

---

## Issue: Service Not Accessible

### Verify

```bash
kubectl get svc -n securebank
```

Expected:

```text
EXTERNAL-IP Assigned
```

---

# 🔍 7. Jenkins Troubleshooting

---

## Issue: Jenkins Not Opening

### Verify

```bash
sudo systemctl status jenkins
```

### Restart

```bash
sudo systemctl restart jenkins
```

### Check Port

```bash
ss -tulpn | grep 8080
```

---

## Issue: Jenkins Initial Password Missing

### Verify

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## Issue: Plugin Installation Failed

### Verify Internet

```bash
ping google.com
```

### Restart Jenkins

```bash
sudo systemctl restart jenkins
```

---

# 🔍 8. SonarQube Troubleshooting

---

## Issue: SonarQube Not Opening

### Verify

```bash
docker ps
```

### Logs

```bash
docker logs sonarqube
```

### Check Port

```bash
ss -tulpn | grep 9000
```

---

## Issue: Jenkins Cannot Connect to SonarQube

### Verify

```bash
curl http://SONAR-IP:9000
```

Expected:

```text
HTTP 200
```

---

## Issue: Invalid Sonar Token

### Fix

Create New Token:

```text
My Account
→ Security
→ Generate Token
```

Update Jenkins Credential.

---

# 🐳 9. Docker Troubleshooting

---

## Issue: Docker Service Down

### Verify

```bash
sudo systemctl status docker
```

### Restart

```bash
sudo systemctl restart docker
```

---

## Issue: Docker Login Failed

### Verify

```bash
docker login
```

### Use DockerHub Token

```text
Personal Access Token
```

Not Docker Password.

---

## Issue: Docker Push Failed

### Verify

```bash
docker images
```

Repository Exists:

```text
DockerHub Repository
```

---

# 🚀 10. CI/CD Pipeline Troubleshooting

---

## Issue: Git Checkout Failed

### Verify Credentials

```text
github-creds
```

### Verify Repository

```bash
git ls-remote REPOSITORY_URL
```

---

## Issue: Maven Build Failed

### Verify

```bash
mvn clean package
```

### Check

```text
pom.xml
```

---

## Issue: Sonar Stage Failed

### Verify

```text
SonarQube Server
```

### Verify Token

```text
sonar-token
```

---

## Issue: Docker Stage Failed

### Verify

```bash
docker build .
```

Check:

```text
Dockerfile
```

---

## Issue: Kubernetes Deploy Stage Failed

### Verify

```bash
kubectl get nodes
```

Verify:

```bash
kubectl get deployment -n securebank
```

---

# 📊 11. Prometheus Troubleshooting

---

## Issue: Prometheus Not Opening

### Verify

```bash
docker ps
```

### Logs

```bash
docker logs prometheus
```

### Port

```bash
ss -tulpn | grep 9090
```

---

## Issue: Targets Down

### Verify

```bash
curl http://SERVER-IP:9100/metrics
```

Expected:

```text
node_cpu_seconds_total
```

---

## Issue: Metrics Missing

### Verify

```bash
docker logs prometheus
```

Check:

```text
prometheus.yml
```

---

# 📈 12. Grafana Troubleshooting

---

## Issue: Grafana Not Opening

### Verify

```bash
docker ps
```

### Logs

```bash
docker logs grafana
```

### Port

```bash
ss -tulpn | grep 3000
```

---

## Issue: Datasource Not Working

### Verify

```text
Connections
→ Data Sources
```

Test:

```text
Save & Test
```

Expected:

```text
Data source is working
```

---

## Issue: Dashboard ID 1860 Not Showing Data

### Verify Datasource

```text
Prometheus
```

### Verify Query

```promql
up
```

Expected:

```text
1
```

---

# 📋 13. Useful Validation Commands

## Kubernetes

```bash
kubectl get all -A
```

---

## Nodes

```bash
kubectl get nodes
```

---

## Pods

```bash
kubectl get pods -A
```

---

## Services

```bash
kubectl get svc -A
```

---

## Docker

```bash
docker ps -a
```

---

## Jenkins

```bash
sudo systemctl status jenkins
```

---

## Node Exporter

```bash
sudo systemctl status node_exporter
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

# 🚨 Emergency Recovery Commands

## Restart Jenkins

```bash
sudo systemctl restart jenkins
```

---

## Restart Docker

```bash
sudo systemctl restart docker
```

---

## Restart Node Exporter

```bash
sudo systemctl restart node_exporter
```

---

## Restart Prometheus

```bash
docker restart prometheus
```

---

## Restart Grafana

```bash
docker restart grafana
```

---

# ✅ Final Validation Checklist

## Infrastructure

- [ ] VMs Running
- [ ] Firewall Rules Configured

## Kubernetes

- [ ] Cluster Healthy
- [ ] Nodes Ready
- [ ] Pods Running

## Jenkins

- [ ] Jenkins Accessible
- [ ] Pipeline Successful

## SonarQube

- [ ] Analysis Successful
- [ ] Quality Gate Passed

## Docker

- [ ] Image Built
- [ ] Image Pushed

## Monitoring

- [ ] Prometheus Running
- [ ] Grafana Running
- [ ] Node Exporter Running

---

# 📌 Expected Outcome

At the end of this troubleshooting guide:

✅ Infrastructure Issues Resolved

✅ Kubernetes Issues Resolved

✅ Jenkins Issues Resolved

✅ SonarQube Issues Resolved

✅ Docker Issues Resolved

✅ Monitoring Issues Resolved

✅ Production Environment Stable

---

# Next Document

```text
09-Project-Execution-Runbook.md
```
