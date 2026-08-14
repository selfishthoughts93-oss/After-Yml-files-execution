# 🚀 06-Complete-CICD-Pipeline.md

# Complete CI/CD Pipeline Runbook

> Production-ready CI/CD Pipeline implementation for SecureBank Application using GitHub, Jenkins, SonarQube, DockerHub, GKE, Kubernetes, Prometheus, and Grafana.

---

# 📌 1. Objective

This document covers the complete CI/CD pipeline implementation.

At the end of this phase:

✅ Source Code will be fetched from GitHub

✅ Maven Build will be executed

✅ SonarQube Analysis will be performed

✅ Docker Image will be built

✅ Docker Image will be pushed to DockerHub

✅ Kubernetes Deployment will be updated automatically

✅ Application will be deployed into GKE

---

# 🏗️ 2. CI/CD Architecture

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
 Maven Build      SonarQube Scan
    |                   |
    +---------+---------+
              |
              v
        Docker Build
              |
              v
          DockerHub
              |
              v
      Kubernetes Deploy
              |
              v
          GKE Cluster
              |
              v
      SecureBank Application
```

---

# 🖥️ 3. Execution Location

| Activity | Execute On |
|-----------|------------|
| GitHub Configuration | GitHub |
| Jenkins Configuration | Jenkins UI |
| Docker Configuration | Jenkins VM |
| Kubernetes Deployment | Jenkins VM |
| Pipeline Execution | Jenkins UI |
| Validation | Browser |

---

# 📋 4. Prerequisites

Ensure the following are completed:

- Jenkins Installed
- SonarQube Configured
- Docker Installed
- GKE Cluster Running
- Kubectl Configured
- DockerHub Account Available
- GitHub Repository Available

---

# 📂 5. Project Structure

```text
securebank/

├── src/
├── pom.xml
├── Dockerfile
├── Jenkinsfile
└── k8s/
    ├── namespace.yaml
    ├── deployment.yaml
    └── service.yaml
```

---

# 🔐 6. Create DockerHub Access Token

## Run On

```text
DockerHub Website
```

Navigation:

```text
Account Settings
→ Personal Access Tokens
→ Generate New Token
```

Configuration:

```text
Token Name :
jenkins-dockerhub-token
```

Permission:

```text
Read, Write, Delete
```

Copy Token.

---

# 🔑 7. Add DockerHub Credentials in Jenkins

## Run On

```text
Jenkins UI
```

Navigation:

```text
Manage Jenkins
→ Credentials
→ Global
→ Add Credentials
```

Configuration:

```text
Kind :
Username with Password
```

```text
Username :
YOUR_DOCKERHUB_USERNAME
```

```text
Password :
DOCKERHUB_ACCESS_TOKEN
```

```text
ID :
dockerhub-creds
```

Save.

---

# 🔑 8. Add GitHub Credentials

## Run On

```text
Jenkins UI
```

Navigation:

```text
Manage Jenkins
→ Credentials
→ Global
→ Add Credentials
```

Configuration:

```text
Kind :
Username with Password
```

```text
Username :
GitHub Username
```

```text
Password :
GitHub Token
```

```text
ID :
github-creds
```

Save.

---

# ☸️ 9. Configure Kubernetes Access on Jenkins VM

## Run On

```text
Jenkins VM
```

Verify Cluster Access:

```bash
kubectl get nodes
```

Expected:

```text
Ready
Ready
```

If not configured:

```bash
gcloud auth login
```

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

# 🐳 10. Dockerfile

## Run On

```text
GitHub Repository
```

File:

```text
Dockerfile
```

Content:

```dockerfile
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

Commit:

```bash
git add .
git commit -m "Added Dockerfile"
git push
```

---

# ⚙️ 11. Jenkinsfile

## Run On

```text
GitHub Repository
```

File:

```text
Jenkinsfile
```

Content:

```groovy
pipeline {

    agent any

    tools {
        maven 'Maven3'
    }

    environment {

        DOCKER_IMAGE = "devopsbyrushi/securebank"

        DOCKER_CREDENTIALS_ID = "dockerhub-creds"
    }

    stages {

        stage('Git Checkout') {

            steps {

                git branch: 'main',
                credentialsId: 'github-creds',
                url: 'https://github.com/USERNAME/REPOSITORY.git'
            }
        }

        stage('Maven Build') {

            steps {

                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {

            steps {

                withSonarQubeEnv('SonarQube') {

                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.projectKey=securebank \
                    -Dsonar.projectName=securebank
                    '''
                }
            }
        }

        stage('Quality Gate') {

            steps {

                timeout(time: 5, unit: 'MINUTES') {

                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {

            steps {

                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
                sh 'docker tag $DOCKER_IMAGE:$BUILD_NUMBER $DOCKER_IMAGE:latest'
            }
        }

        stage('Docker Login') {

            steps {

                withCredentials([
                usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login \
                    -u $DOCKER_USER \
                    --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {

            steps {

                sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
                sh 'docker push $DOCKER_IMAGE:latest'
            }
        }

        stage('Kubernetes Deployment') {

            steps {

                sh '''
                kubectl set image deployment/securebank \
                securebank=$DOCKER_IMAGE:latest \
                -n securebank
                '''
            }
        }
    }

    post {

        success {

            echo 'Pipeline Completed Successfully'
        }

        failure {

            echo 'Pipeline Failed'
        }
    }
}
```

---

# 📂 12. Push Code to GitHub

## Run On

```text
Local Machine
```

```bash
git add .
```

```bash
git commit -m "Initial SecureBank Project"
```

```bash
git push origin main
```

---

# 🏗️ 13. Create Jenkins Pipeline Job

## Run On

```text
Jenkins UI
```

Navigation:

```text
New Item
→ Pipeline
```

Configuration:

```text
Name :
SecureBank-Pipeline
```

Pipeline:

```text
Pipeline Script from SCM
```

SCM:

```text
Git
```

Repository URL:

```text
https://github.com/USERNAME/REPOSITORY.git
```

Credentials:

```text
github-creds
```

Branch:

```text
*/main
```

Save.

---

# ▶️ 14. Trigger Build

## Run On

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

# 🔍 15. Verify DockerHub Image

## Run On

```text
DockerHub Website
```

Verify:

```text
devopsbyrushi/securebank
```

Tags:

```text
latest

BUILD_NUMBER
```

---

# ☸️ 16. Verify Kubernetes Deployment

## Run On

```text
Jenkins VM
```

```bash
kubectl get deployment -n securebank
```

Expected:

```text
READY 2/2
```

---

# 🔍 17. Verify Pods

```bash
kubectl get pods -n securebank
```

Expected:

```text
Running
Running
```

---

# 🌐 18. Verify Application

## Run On

```text
Browser
```

```bash
kubectl get svc -n securebank
```

Copy External IP.

Open:

```text
http://EXTERNAL-IP
```

Expected:

```text
SecureBank Home Page
```

---

# 📊 19. Validation Commands

Deployment:

```bash
kubectl get deployment -n securebank
```

Pods:

```bash
kubectl get pods -n securebank
```

Service:

```bash
kubectl get svc -n securebank
```

Logs:

```bash
kubectl logs POD_NAME -n securebank
```

---

# 🚨 Common Troubleshooting

## Git Checkout Failed

Verify:

```text
github-creds
```

Check:

```bash
git ls-remote REPOSITORY_URL
```

---

## SonarQube Failed

Verify:

```text
SonarQube Token
```

Check:

```bash
curl http://SONAR-IP:9000
```

---

## Docker Login Failed

Verify:

```text
dockerhub-creds
```

Check:

```bash
docker login
```

---

## Docker Push Failed

Verify:

```bash
docker images
```

Verify Repository:

```text
DockerHub Repository Exists
```

---

## Kubernetes Deployment Failed

Verify:

```bash
kubectl get nodes
```

Verify:

```bash
kubectl get deployment -n securebank
```

---

# ✅ Validation Checklist

## Jenkins

- [ ] Pipeline Created
- [ ] Build Successful

## SonarQube

- [ ] Analysis Completed
- [ ] Quality Gate Passed

## Docker

- [ ] Image Built
- [ ] Image Pushed

## Kubernetes

- [ ] Deployment Updated
- [ ] Pods Running
- [ ] Service Running

## Application

- [ ] Accessible Through LoadBalancer
- [ ] No Runtime Errors

---

# 📌 Expected Outcome

At the end of this phase:

✅ GitHub Integrated

✅ Jenkins Pipeline Configured

✅ SonarQube Analysis Automated

✅ Docker Build Automated

✅ Docker Push Automated

✅ Kubernetes Deployment Automated

✅ End-to-End CI/CD Pipeline Completed

---

# Next Document

```text
07-Monitoring-Prometheus-Grafana.md
```
