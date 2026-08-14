# 🔍 05-Jenkins-SonarQube-Integration.md

# Jenkins and SonarQube Integration Runbook

> Production-ready Jenkins and SonarQube integration guide for SecureBank DevSecOps Project.

---

# 📌 1. Objective

This document covers:

- Jenkins Configuration
- SonarQube Configuration
- Jenkins ↔ SonarQube Integration
- SonarQube Token Creation
- Jenkins Credential Configuration
- SonarQube Scanner Installation
- Quality Gate Verification

At the end of this phase:

✅ Jenkins will communicate with SonarQube

✅ Source code quality analysis will run automatically

✅ Quality Gate validation will be enabled

---

# 🏗️ 2. Architecture

```text
Developer
    |
    v
GitHub Repository
    |
    v
Jenkins Pipeline
    |
    +----------------+
    |                |
    v                v
Maven Build     Sonar Scanner
                     |
                     v
               SonarQube Server
                     |
                     v
               Quality Gate
                     |
                     v
              Jenkins Result
```

---

# 🖥️ 3. Execution Location

| Activity | Execute On |
|-----------|------------|
| Jenkins Configuration | Jenkins UI |
| SonarQube Configuration | SonarQube UI |
| Plugin Installation | Jenkins UI |
| Token Creation | SonarQube UI |
| Pipeline Testing | Jenkins UI |
| Validation | Browser |

---

# 📋 4. Prerequisites

Ensure the following are completed:

- Jenkins Installed
- SonarQube Installed
- Java Installed
- Maven Installed
- Git Installed
- GitHub Repository Available

Verify:

```bash
java -version
```

```bash
mvn -version
```

```bash
git --version
```

---

# 🚀 5. Access Jenkins

## Run On

```text
Browser
```

Open:

```text
http://JENKINS-IP:8080
```

Login using:

```text
Admin User
Password
```

Expected:

```text
Jenkins Dashboard
```

---

# 🚀 6. Access SonarQube

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

Change Password if prompted.

Expected:

```text
SonarQube Dashboard
```

---

# 🔌 7. Install Required Jenkins Plugins

## Run On

```text
Jenkins UI
```

Navigation:

```text
Manage Jenkins
→ Plugins
→ Available Plugins
```

Install:

```text
SonarQube Scanner

Pipeline

Pipeline Stage View

Docker Pipeline

Git

GitHub

Maven Integration

Kubernetes

Blue Ocean
```

Restart Jenkins after installation.

---

# ⚙️ 8. Configure SonarQube Server

## Run On

```text
SonarQube UI
```

Navigation:

```text
Administration
→ Configuration
```

Verify server is healthy.

---

# 🔑 9. Create SonarQube Token

## Run On

```text
SonarQube UI
```

Navigation:

```text
My Account
→ Security
```

Create Token:

```text
Name :
jenkins-sonar-token
```

Click:

```text
Generate
```

Copy token immediately.

Example:

```text
sqp_xxxxxxxxxxxxxxxxxxxxxxxxx
```

Save securely.

---

# ⚙️ 10. Configure SonarQube in Jenkins

## Run On

```text
Jenkins UI
```

Navigation:

```text
Manage Jenkins
→ System
```

Find:

```text
SonarQube Servers
```

Click:

```text
Add SonarQube
```

Configuration:

```text
Name :
SonarQube

Server URL :
http://SONAR-IP:9000
```

Authentication Token:

```text
Add Credentials
```

Select:

```text
Secret Text
```

Paste:

```text
jenkins-sonar-token
```

ID:

```text
sonar-token
```

Save.

---

# ⚙️ 11. Configure Sonar Scanner

## Run On

```text
Jenkins UI
```

Navigation:

```text
Manage Jenkins
→ Tools
```

Locate:

```text
SonarQube Scanner
```

Add:

```text
Name :
SonarScanner
```

Check:

```text
Install Automatically
```

Save.

---

# ⚙️ 12. Configure Maven

## Run On

```text
Jenkins UI
```

Navigation:

```text
Manage Jenkins
→ Tools
```

Locate:

```text
Maven Installations
```

Configuration:

```text
Name :
Maven3
```

Check:

```text
Install Automatically
```

Version:

```text
Latest Stable
```

Save.

---

# ⚙️ 13. Configure JDK

## Run On

```text
Jenkins UI
```

Navigation:

```text
Manage Jenkins
→ Tools
```

Configuration:

```text
Name :
JDK17
```

Install Automatically:

```text
Enabled
```

Version:

```text
Java 17
```

Save.

---

# 📂 14. Create SonarQube Project

## Run On

```text
SonarQube UI
```

Navigation:

```text
Projects
→ Create Project
```

Configuration:

```text
Project Key :
securebank

Display Name :
securebank
```

Create Project.

---

# 🧪 15. Test SonarQube Connectivity

## Run On

```text
Jenkins UI
```

Navigation:

```text
Manage Jenkins
→ System
→ SonarQube
```

Click:

```text
Test Connection
```

Expected:

```text
Success
```

---

# 📄 16. Sample SonarQube Stage

Add inside Jenkins Pipeline:

```groovy
stage('SonarQube Analysis') {

    steps {

        withSonarQubeEnv('SonarQube') {

            sh '''
            mvn clean verify sonar:sonar \
            -Dsonar.projectKey=securebank \
            -Dsonar.projectName=securebank
            '''
        }
    }
}
```

---

# 📊 17. Verify Sonar Analysis

## Run On

```text
SonarQube UI
```

Navigation:

```text
Projects
→ securebank
```

Expected:

```text
Code Smells

Bugs

Vulnerabilities

Coverage

Duplications
```

---

# 🛡️ 18. Configure Quality Gate

## Run On

```text
SonarQube UI
```

Navigation:

```text
Quality Gates
```

Select:

```text
Sonar Way
```

Set as:

```text
Default
```

---

# ⚙️ 19. Configure Quality Gate in Jenkins

Pipeline Stage:

```groovy
stage('Quality Gate') {

    steps {

        timeout(time: 5, unit: 'MINUTES') {

            waitForQualityGate abortPipeline: true

        }
    }
}
```

---

# 🔍 20. Verify Integration

Trigger Build:

```text
Build Now
```

Expected:

```text
SUCCESS
```

Jenkins Console:

```text
SonarQube analysis completed
```

SonarQube:

```text
Quality Gate Passed
```

---

# 📊 21. Validation Commands

Check Jenkins:

```bash
sudo systemctl status jenkins
```

Check SonarQube:

```bash
docker ps
```

Check Port:

```bash
ss -tulpn | grep 9000
```

---

# 🚨 Common Troubleshooting

## SonarQube Connection Failed

Verify:

```bash
curl http://SONAR-IP:9000
```

Expected:

```text
HTTP 200
```

---

## Invalid Token

Create a new token:

```text
My Account
→ Security
→ Generate Token
```

Update Jenkins Credential.

---

## Scanner Not Found

Verify:

```text
Manage Jenkins
→ Tools
→ Sonar Scanner
```

Install again.

---

## Quality Gate Timeout

Verify webhook.

Navigation:

```text
SonarQube
→ Administration
→ Webhooks
```

URL:

```text
http://JENKINS-IP:8080/sonarqube-webhook/
```

---

## Jenkins Cannot Reach SonarQube

Verify:

```bash
ping SONAR-IP
```

Verify firewall:

```bash
ss -tulpn | grep 9000
```

---

# ✅ Validation Checklist

## Jenkins

- [ ] Jenkins Running
- [ ] Plugins Installed
- [ ] Maven Configured
- [ ] JDK Configured

## SonarQube

- [ ] SonarQube Running
- [ ] Token Generated
- [ ] Project Created

## Integration

- [ ] Sonar Server Added
- [ ] Scanner Configured
- [ ] Test Connection Successful

## Quality Gate

- [ ] Analysis Successful
- [ ] Quality Gate Passed

---

# 📌 Expected Outcome

At the end of this phase:

✅ Jenkins Connected to SonarQube

✅ Sonar Scanner Configured

✅ Code Analysis Enabled

✅ Quality Gate Enabled

✅ Pipeline Ready for Docker Build

✅ Ready for Complete CI/CD Pipeline

---

# Next Document

```text
06-Complete-CICD-Pipeline.md
```
