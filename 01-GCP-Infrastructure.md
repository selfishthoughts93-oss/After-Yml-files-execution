# 🌐 01-GCP-Infrastructure.md

# GCP Infrastructure Setup Runbook

> Production-ready infrastructure setup guide for the SecureBank DevSecOps Project on Google Cloud Platform (GCP).

---

# 📌 1. Objective

This document covers the complete Google Cloud infrastructure setup required before deploying the SecureBank application.

At the end of this phase, the following infrastructure will be available:

- Google Cloud Project
- Custom VPC
- Custom Subnet
- Firewall Rules
- Jenkins VM
- SonarQube VM
- Monitoring VM
- Service Account for Terraform
- SSH Connectivity Between Servers

---

# 🏗️ 2. Infrastructure Architecture

```text
Google Cloud Platform
│
├── VPC
│   └── bankingproject2027-vpc
│
├── Subnet
│   └── bankingproject2027-subnet
│
├── Jenkins VM
│   └── CI/CD Server
│
├── SonarQube VM
│   └── Code Quality Server
│
├── Monitoring VM
│   ├── Prometheus
│   └── Grafana
│
└── GKE Cluster
    └── Created in Terraform Phase
```

---

# 🖥️ 3. Execution Location

| Activity | Execute On |
|-----------|------------|
| Project Creation | GCP Console |
| VPC Creation | GCP Console |
| Firewall Creation | GCP Console |
| VM Creation | GCP Console |
| Service Account Creation | GCP Console |
| SSH Verification | VS Code Terminal |

---

# 📋 4. Prerequisites

Before starting ensure:

- Google Cloud Account Available
- Billing Enabled
- Owner Access to Project
- VS Code Installed
- Git Installed
- SSH Client Installed

Verify:

```bash
git --version

ssh -V
```

Expected:

```text
git version 2.x.x

OpenSSH_x.x
```

---

# 🚀 5. Create GCP Project

## Execute On

```text
Google Cloud Console
```

Navigation:

```text
Google Cloud Console
→ IAM & Admin
→ Manage Resources
→ Create Project
```

Project Details:

```text
Project Name : bankingproject2027

Project ID   : bankingproject2027
```

Click:

```text
Create
```

---

# 💳 6. Enable Billing

## Execute On

```text
Google Cloud Console
```

Navigation:

```text
Billing
→ Link Billing Account
```

Verify:

```text
Billing Status = Active
```

---

# 🌐 7. Create Custom VPC

## Execute On

```text
Google Cloud Console
```

Navigation:

```text
VPC Network
→ VPC Networks
→ Create VPC Network
```

Configuration:

```text
Name : bankingproject2027-vpc

Subnet Creation Mode : Custom
```

Click:

```text
Create
```

---

# 🌐 8. Create Subnet

## Execute On

```text
Google Cloud Console
```

Configuration:

```text
Name : bankingproject2027-subnet

Region : us-central1

IPv4 Range : 10.10.0.0/24
```

Save configuration.

---

# 🔥 9. Create Firewall Rules

## Execute On

```text
Google Cloud Console
```

Navigation:

```text
VPC Network
→ Firewall
→ Create Firewall Rule
```

---

## Rule 1 - SSH Access

```text
Name : allow-ssh

Network : bankingproject2027-vpc

Source IP :
0.0.0.0/0

Protocol :
TCP

Port :
22
```

---

## Rule 2 - Jenkins

```text
Name : allow-jenkins

Port : 8080
```

---

## Rule 3 - SonarQube

```text
Name : allow-sonarqube

Port : 9000
```

---

## Rule 4 - Grafana

```text
Name : allow-grafana

Port : 3000
```

---

## Rule 5 - Prometheus

```text
Name : allow-prometheus

Port : 9090
```

---

## Rule 6 - Node Exporter

```text
Name : allow-node-exporter

Port : 9100
```

---

# 🖥️ 10. Create Jenkins VM

## Execute On

```text
Google Cloud Console
```

Navigation:

```text
Compute Engine
→ VM Instances
→ Create Instance
```

Configuration:

```text
Name :
jenkins-server

Region :
us-central1

Zone :
us-central1-a

Machine Type :
e2-medium

Boot Disk :
Ubuntu 24.04 LTS

Network :
bankingproject2027-vpc

Subnet :
bankingproject2027-subnet
```

Create Instance.

---

# 🖥️ 11. Create SonarQube VM

## Execute On

```text
Google Cloud Console
```

Configuration:

```text
Name :
sonarqube-server

Machine Type :
e2-medium

OS :
Ubuntu 24.04 LTS

Network :
bankingproject2027-vpc
```

Create Instance.

---

# 🖥️ 12. Create Monitoring VM

## Execute On

```text
Google Cloud Console
```

Configuration:

```text
Name :
monitoring-server

Machine Type :
e2-medium

OS :
Ubuntu 24.04 LTS

Network :
bankingproject2027-vpc
```

Create Instance.

---

# 👤 13. Create Terraform Service Account

## Execute On

```text
Google Cloud Console
```

Navigation:

```text
IAM & Admin
→ Service Accounts
→ Create Service Account
```

Configuration:

```text
Name :
terraform-sa
```

Grant Roles:

```text
Compute Admin

Kubernetes Engine Admin

Service Account User

Network Admin

Viewer
```

Create.

---

# 🔑 14. Generate Service Account Key

Open:

```text
IAM & Admin
→ Service Accounts
→ terraform-sa
→ Keys
→ Add Key
→ Create New Key
```

Select:

```text
JSON
```

Download:

```text
terraform-key.json
```

Store securely.

---

# 🔐 15. Configure SSH Access

## Execute On

```text
VS Code Terminal
```

Generate SSH Key:

```bash
ssh-keygen -t ed25519
```

Press Enter for defaults.

Files Created:

```text
~/.ssh/id_ed25519

~/.ssh/id_ed25519.pub
```

---

# Recommended Permissions

```bash
chmod 700 ~/.ssh

chmod 600 ~/.ssh/id_ed25519

chmod 644 ~/.ssh/id_ed25519.pub
```

---

# Copy Public Key to Managed Servers

Example:

```bash
ssh-copy-id docker@SERVER-IP

ssh-copy-id monitoring@SERVER-IP

ssh-copy-id sonar@SERVER-IP

ssh-copy-id master@SERVER-IP
```

---

# Verify Connectivity

```bash
ssh master@SERVER-IP

ssh sonar@SERVER-IP

ssh monitoring@SERVER-IP
```

Expected:

```text
Welcome to Ubuntu 24.04 LTS
```

---

# ✅ Validation Checklist

## GCP

- [ ] Project Created
- [ ] Billing Enabled

## Networking

- [ ] VPC Created
- [ ] Subnet Created
- [ ] Firewall Rules Created

## Compute

- [ ] Jenkins VM Created
- [ ] SonarQube VM Created
- [ ] Monitoring VM Created

## Security

- [ ] Service Account Created
- [ ] JSON Key Downloaded
- [ ] SSH Keys Generated

## Connectivity

- [ ] SSH Working
- [ ] VM Reachability Verified

---

# 📌 Expected Outcome

At the end of this phase:

✅ GCP Project Ready

✅ Networking Configured

✅ VMs Running

✅ Firewall Rules Configured

✅ Terraform Service Account Created

✅ SSH Connectivity Verified

✅ Ready for Ansible Configuration Phase

---

# Next Document

```text
02-GCP-DevOps-Professional-Ansible-Runbook.md
```
