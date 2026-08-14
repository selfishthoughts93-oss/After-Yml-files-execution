# ☸️ 04-Kubernetes-Deployment.md

# Kubernetes Deployment Runbook for SecureBank Application

> Production-ready Kubernetes deployment guide for deploying the SecureBank application on Google Kubernetes Engine (GKE).

---

# 📌 1. Objective

This document covers the deployment of the SecureBank application into the GKE cluster using Kubernetes manifests.

At the end of this phase:

- Namespace will be created
- Application Deployment will be created
- Kubernetes Service will be exposed
- LoadBalancer will be provisioned
- Application will be accessible through External IP

---

# 🏗️ 2. Architecture

```text
                    GKE Cluster

 ┌──────────────────────────────────────┐
 │                                      │
 │      Namespace : securebank          │
 │                                      │
 │   ┌────────────────────────────┐     │
 │   │      Deployment            │     │
 │   │                            │     │
 │   │ SecureBank Pod-1           │     │
 │   │ SecureBank Pod-2           │     │
 │   └────────────────────────────┘     │
 │                                      │
 │   ┌────────────────────────────┐     │
 │   │      LoadBalancer Service  │     │
 │   └────────────────────────────┘     │
 │                                      │
 └──────────────────────────────────────┘

                    |
                    |
                    ▼

              External IP

                    |
                    ▼

             End Users
```

---

# 🖥️ 3. Execution Location

| Activity | Execute On |
|-----------|------------|
| Create YAML Files | VS Code |
| Deploy Resources | VS Code |
| Verify Pods | VS Code |
| Verify Services | VS Code |
| Access Application | Browser |

---

# 📋 4. Prerequisites

Before proceeding ensure:

- GKE Cluster Running
- Node Pool Running
- Kubectl Configured
- Docker Image Available
- Cluster Accessible

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

# 📁 5. Kubernetes Folder Structure

Create:

```text
k8s/

├── namespace.yaml
├── deployment.yaml
└── service.yaml
```

---

# 🔍 6. Verify Docker Image

## Run On

```text
Browser
```

Verify image exists:

```text
https://hub.docker.com/r/devopsbyrushi/securebank
```

Image:

```text
devopsbyrushi/securebank:latest
```

---

# 📂 7. Create Namespace

## Run On

```text
VS Code
```

File:

```text
k8s/namespace.yaml
```

Content:

```yaml
apiVersion: v1
kind: Namespace

metadata:
  name: securebank
```

Apply:

```bash
kubectl apply -f k8s/namespace.yaml
```

Verify:

```bash
kubectl get ns
```

Expected:

```text
securebank
```

---

# 🚀 8. Create Deployment

## Run On

```text
VS Code
```

File:

```text
k8s/deployment.yaml
```

Content:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: securebank
  namespace: securebank

spec:
  replicas: 2

  selector:
    matchLabels:
      app: securebank

  template:
    metadata:
      labels:
        app: securebank

    spec:
      containers:
      - name: securebank

        image: devopsbyrushi/securebank:latest

        imagePullPolicy: Always

        ports:
        - containerPort: 8080

        resources:
          requests:
            cpu: "250m"
            memory: "256Mi"

          limits:
            cpu: "500m"
            memory: "512Mi"
```

Apply:

```bash
kubectl apply -f k8s/deployment.yaml
```

Expected:

```text
deployment.apps/securebank created
```

---

# 🔍 9. Verify Deployment

## Run On

```text
VS Code Terminal
```

```bash
kubectl get deployment -n securebank
```

Expected:

```text
NAME         READY   UP-TO-DATE   AVAILABLE
securebank   2/2     2            2
```

---

# 🔍 10. Verify ReplicaSets

```bash
kubectl get rs -n securebank
```

Expected:

```text
NAME
securebank-xxxxxxxxx
```

---

# 🔍 11. Verify Pods

```bash
kubectl get pods -n securebank
```

Expected:

```text
NAME                              STATUS
securebank-xxxxxx-xxxxx           Running
securebank-yyyyyy-yyyyy           Running
```

---

# 🔍 12. Pod Details

```bash
kubectl describe pod POD_NAME -n securebank
```

Example:

```bash
kubectl describe pod securebank-6d9c57fdf6-vx2jr -n securebank
```

---

# 📡 13. Create Service

## Run On

```text
VS Code
```

File:

```text
k8s/service.yaml
```

Content:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: securebank-service
  namespace: securebank

spec:
  selector:
    app: securebank

  type: LoadBalancer

  ports:
  - port: 80
    targetPort: 8080
```

Apply:

```bash
kubectl apply -f k8s/service.yaml
```

Expected:

```text
service/securebank-service created
```

---

# 🔍 14. Verify Service

## Run On

```text
VS Code Terminal
```

```bash
kubectl get svc -n securebank
```

Expected:

```text
NAME                  TYPE           EXTERNAL-IP
securebank-service    LoadBalancer   PENDING
```

Wait 2-5 minutes.

Check again:

```bash
kubectl get svc -n securebank
```

Expected:

```text
NAME                  TYPE           EXTERNAL-IP
securebank-service    LoadBalancer   34.xx.xx.xx
```

---

# 🌐 15. Access Application

## Run On

```text
Browser
```

Open:

```text
http://EXTERNAL-IP
```

Example:

```text
http://34.170.xxx.xxx
```

---

# 🔍 16. Application Verification

Verify the following URLs:

```text
/
```

```text
/health
```

```text
/login
```

```text
/register
```

```text
/dashboard
```

Expected:

```text
HTTP 200 OK
```

---

# 📋 17. Verify All Resources

```bash
kubectl get all -n securebank
```

Expected:

```text
pods
services
deployments
replicasets
```

---

# 📊 18. Kubernetes Monitoring Commands

Pods:

```bash
kubectl get pods -n securebank -o wide
```

---

Services:

```bash
kubectl get svc -n securebank
```

---

Deployments:

```bash
kubectl get deployment -n securebank
```

---

ReplicaSets:

```bash
kubectl get rs -n securebank
```

---

Namespaces:

```bash
kubectl get ns
```

---

# 🔄 19. Rolling Restart

If new Docker image is pushed:

```bash
kubectl rollout restart deployment securebank -n securebank
```

Verify:

```bash
kubectl rollout status deployment securebank -n securebank
```

Expected:

```text
successfully rolled out
```

---

# 🔍 20. View Application Logs

```bash
kubectl logs POD_NAME -n securebank
```

Example:

```bash
kubectl logs securebank-xxxxx-xxxxx -n securebank
```

Live Logs:

```bash
kubectl logs -f securebank-xxxxx-xxxxx -n securebank
```

---

# 🚨 Common Troubleshooting

## Pod Not Running

Check:

```bash
kubectl describe pod POD_NAME -n securebank
```

---

## Image Pull Error

Verify:

```bash
docker pull devopsbyrushi/securebank:latest
```

---

## Service Not Getting External IP

Verify:

```bash
kubectl get svc -n securebank
```

Wait:

```text
2-5 Minutes
```

---

## CrashLoopBackOff

Check:

```bash
kubectl logs POD_NAME -n securebank
```

---

## Deployment Failed

Verify:

```bash
kubectl describe deployment securebank -n securebank
```

---

# 🧹 21. Delete Resources (Optional)

Delete Service:

```bash
kubectl delete -f k8s/service.yaml
```

Delete Deployment:

```bash
kubectl delete -f k8s/deployment.yaml
```

Delete Namespace:

```bash
kubectl delete -f k8s/namespace.yaml
```

---

# ✅ Validation Checklist

## Namespace

- [ ] Namespace Created

## Deployment

- [ ] Deployment Running
- [ ] Replicas Running

## Service

- [ ] Service Created
- [ ] External IP Assigned

## Application

- [ ] Home Page Accessible
- [ ] Login Page Accessible
- [ ] Health Endpoint Working

## Kubernetes

- [ ] Pods Running
- [ ] Services Running
- [ ] Deployment Healthy

---

# 📌 Expected Outcome

At the end of this phase:

✅ Namespace Created

✅ Deployment Running

✅ Pods Running

✅ Service Exposed

✅ External IP Assigned

✅ SecureBank Application Accessible

✅ Ready for Jenkins CI/CD Integration

---

# Next Document

```text
05-Jenkins-SonarQube-Integration.md
```
