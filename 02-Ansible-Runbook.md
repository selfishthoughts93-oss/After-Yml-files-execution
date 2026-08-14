# ⚙️ 02-Ansible-Runbook.md

# GCP DevOps Professional Ansible Configuration Runbook

> Production-ready Ansible automation guide for configuring Jenkins, SonarQube, Docker, Prometheus, Grafana, and Node Exporter servers in the SecureBank DevSecOps Project.

---

# 📌 1. Objective

This document covers the complete Ansible setup and server configuration process.

At the end of this phase, the following components will be installed and configured automatically:

- Common Packages
- Java 17
- Docker
- Jenkins
- SonarQube
- Prometheus
- Grafana
- Node Exporter

---

# 🏗️ 2. Architecture

```text
Ansible Control Node
        |
        |
        +------------------------+
        |                        |
        v                        v
 Jenkins VM              SonarQube VM
        |
        |
        v
 Monitoring VM
   |         |
   v         v
Prometheus Grafana
```

---

# 🖥️ 3. Execution Location

| Activity | Execute On |
|-----------|------------|
| Install Ansible | Control Node |
| Generate SSH Keys | Control Node |
| Configure Inventory | Control Node |
| Run Playbooks | Control Node |
| Verify Jenkins | Browser |
| Verify SonarQube | Browser |
| Verify Prometheus | Browser |
| Verify Grafana | Browser |

---

# 📋 4. Prerequisites

Before proceeding ensure:

- Jenkins VM Created
- SonarQube VM Created
- Monitoring VM Created
- SSH Access Working
- Ubuntu 24.04 LTS Installed

---

# 🔑 5. Configure Passwordless Authentication

## Run On

```text
Ansible Control Node
```

Generate SSH Key

```bash
ssh-keygen -t ed25519
```

Press Enter for defaults.

---

# Set Permissions

```bash
chmod 700 ~/.ssh

chmod 600 ~/.ssh/id_ed25519

chmod 644 ~/.ssh/id_ed25519.pub
```

---

# Copy Public Key to Servers

```bash
ssh-copy-id master@JENKINS-IP

ssh-copy-id sonar@SONAR-IP

ssh-copy-id monitoring@MONITORING-IP
```

---

# Verify Access

```bash
ssh master@JENKINS-IP

ssh sonar@SONAR-IP

ssh monitoring@MONITORING-IP
```

Expected:

```text
Welcome to Ubuntu 24.04 LTS
```

---

# 📦 6. Install Ansible

## Run On

```text
Ansible Control Node
```

```bash
sudo apt update

sudo apt install ansible -y
```

Verify:

```bash
ansible --version
```

Expected:

```text
ansible [core]
```

---

# 📁 7. Project Structure

```text
ansible/

├── hosts
├── 02.Basic-Packages-install.yaml
├── 03-dockerinstall.yaml
├── 04-monitoring.yaml
├── 05.sonarqube-install.yaml
├── 06.Jenkins-install.yml
└── monitoring-node-exporter-installation.yml
```

---

# 📝 8. Inventory File

## hosts

```ini
[jenkins]
JENKINS-IP ansible_user=master

[sonarqube]
SONAR-IP ansible_user=sonar

[monitoring]
MONITORING-IP ansible_user=monitoring

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

Verify:

```bash
ansible all -i hosts -m ping
```

Expected:

```text
SUCCESS
SUCCESS
SUCCESS
```

---

# 📦 9. Install Common Packages

## Run On

```text
Ansible Control Node
```

Execute:

```bash
ansible-playbook -i hosts 02.Basic-Packages-install.yaml
```

Verify:

```bash
ansible all -i hosts -m shell -a "java -version"
```

---

# 🐳 10. Install Docker

## Run On

```text
Ansible Control Node
```

Execute:

```bash
ansible-playbook -i hosts 03-dockerinstall.yaml
```

Verify:

```bash
ansible all -i hosts -m shell -a "docker --version"
```

Expected:

```text
Docker version 28.x
```

---

# 🔍 11. Install SonarQube

## Run On

```text
Ansible Control Node
```

Execute:

```bash
ansible-playbook -i hosts 05.sonarqube-install.yaml
```

---

# Verify SonarQube

## Run On

```text
Browser
```

Open:

```text
http://SONAR-IP:9000
```

Default Login:

```text
Username : admin

Password : admin
```

Expected:

```text
SonarQube Dashboard
```

---

# 🏗️ 12. Install Jenkins

## Run On

```text
Ansible Control Node
```

Execute:

```bash
ansible-playbook -i hosts 06.Jenkins-install.yml
```

---

# Verify Jenkins

## Run On

```text
Browser
```

Open:

```text
http://JENKINS-IP:8080
```

Get Password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Expected:

```text
Unlock Jenkins Screen
```

---

# 📊 13. Install Prometheus & Grafana

## Run On

```text
Ansible Control Node
```

Execute:

```bash
ansible-playbook -i hosts 04-monitoring.yaml
```

---

# Verify Prometheus

## Run On

```text
Browser
```

Open:

```text
http://MONITORING-IP:9090
```

Expected:

```text
Prometheus UI
```

---

# Verify Grafana

## Run On

```text
Browser
```

Open:

```text
http://MONITORING-IP:3000
```

Default Login:

```text
Username : admin

Password : admin
```

Expected:

```text
Grafana Dashboard
```

---

# 📈 14. Install Node Exporter

## Run On

```text
Ansible Control Node
```

Execute:

```bash
ansible-playbook -i hosts monitoring-node-exporter-installation.yml
```

---

# Verify Node Exporter

```bash
ansible all -i hosts -m shell -a "systemctl status node_exporter --no-pager"
```

Check Port:

```bash
ansible all -i hosts -m shell -a "ss -tulpn | grep 9100"
```

Expected:

```text
LISTEN 0.0.0.0:9100
```

---

# 📋 15. Validation Commands

## Docker

```bash
ansible all -i hosts -m shell -a "docker --version"
```

---

## Java

```bash
ansible all -i hosts -m shell -a "java -version"
```

---

## Jenkins

```bash
ansible jenkins -i hosts -m shell -a "systemctl status jenkins --no-pager"
```

---

## SonarQube

```bash
ansible sonarqube -i hosts -m shell -a "docker ps"
```

---

## Prometheus

```bash
curl http://MONITORING-IP:9090/-/healthy
```

---

## Grafana

```bash
curl http://MONITORING-IP:3000/api/health
```

---

# 🚨 Common Troubleshooting

## SSH Failed

Verify:

```bash
ssh master@JENKINS-IP
```

Check:

```bash
ls -la ~/.ssh
```

---

## Ansible Ping Failed

Verify:

```bash
ansible all -i hosts -m ping
```

Check:

```bash
cat hosts
```

---

## Jenkins Not Opening

Verify:

```bash
sudo systemctl status jenkins
```

Port Check:

```bash
sudo ss -tulpn | grep 8080
```

---

## SonarQube Not Opening

Verify:

```bash
docker ps
```

Check:

```bash
docker logs sonarqube
```

---

## Grafana Not Opening

Verify:

```bash
docker ps
```

Check:

```bash
docker logs grafana
```

---

# ✅ Validation Checklist

## Infrastructure

- [ ] Jenkins VM Reachable
- [ ] SonarQube VM Reachable
- [ ] Monitoring VM Reachable

## Ansible

- [ ] Inventory Configured
- [ ] SSH Working
- [ ] Ping Successful

## Jenkins

- [ ] Jenkins Installed
- [ ] Jenkins UI Accessible

## SonarQube

- [ ] SonarQube Installed
- [ ] SonarQube UI Accessible

## Monitoring

- [ ] Prometheus Running
- [ ] Grafana Running
- [ ] Node Exporter Running

---

# 📌 Expected Outcome

At the end of this phase:

✅ Ansible Installed

✅ Passwordless SSH Configured

✅ Jenkins Installed

✅ SonarQube Installed

✅ Docker Installed

✅ Prometheus Installed

✅ Grafana Installed

✅ Node Exporter Installed

✅ Ready for Terraform & GKE Deployment

---

# Next Document

```text
03-GKE-Terraform-Runbook.md
```
