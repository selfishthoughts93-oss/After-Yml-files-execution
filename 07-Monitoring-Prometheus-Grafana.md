# 📊 07-Monitoring-Prometheus-Grafana.md

# Prometheus & Grafana Monitoring Runbook

> Production-ready monitoring setup for SecureBank Application deployed on Google Kubernetes Engine (GKE).

---

# 📌 1. Objective

This document covers the complete monitoring implementation using:

- Prometheus
- Grafana
- Node Exporter
- Kubernetes Monitoring
- Jenkins Monitoring
- SonarQube Monitoring

At the end of this phase:

✅ Prometheus will collect metrics

✅ Grafana will visualize metrics

✅ Node Exporter will expose server metrics

✅ Kubernetes cluster monitoring will be enabled

✅ Dashboards will be available for infrastructure monitoring

---

# 🏗️ 2. Monitoring Architecture

```text
                   Monitoring VM

    +---------------------------------------+
    |                                       |
    |            Prometheus                 |
    |                 |                     |
    |                 |                     |
    |          Collect Metrics              |
    |                 |                     |
    +-----------------+---------------------+
                      |
                      |
      -----------------------------------------
      |                 |                    |
      v                 v                    v

 Node Exporter     Jenkins VM         SonarQube VM
      |                 |                    |
      -----------------------------------------
                      |
                      v

                  Grafana
                      |
                      v

               Monitoring Dashboard
```

---

# 🖥️ 3. Execution Location

| Activity | Execute On |
|-----------|------------|
| Prometheus Setup | Monitoring VM |
| Grafana Setup | Monitoring VM |
| Node Exporter Setup | All Servers |
| Dashboard Import | Grafana UI |
| Validation | Browser |

---

# 📋 4. Prerequisites

Ensure the following are completed:

- Jenkins Installed
- SonarQube Installed
- Monitoring VM Running
- Node Exporter Installed
- Prometheus Installed
- Grafana Installed

---

# 🔍 5. Verify Services

## Run On

```text
Monitoring VM
```

Check Docker Containers:

```bash
docker ps
```

Expected:

```text
prometheus

grafana
```

---

# 📂 6. Monitoring Components

```text
Monitoring VM

├── Prometheus
├── Grafana
└── Prometheus Data

Jenkins VM

└── Node Exporter

SonarQube VM

└── Node Exporter

Monitoring VM

└── Node Exporter
```

---

# 📄 7. Verify Prometheus Configuration

## Run On

```text
Monitoring VM
```

Open:

```bash
sudo nano prometheus.yml
```

Example Configuration:

```yaml
global:
  scrape_interval: 15s

scrape_configs:

  - job_name: "prometheus"

    static_configs:
      - targets:
          - localhost:9090

  - job_name: "jenkins"

    static_configs:
      - targets:
          - JENKINS-IP:9100

  - job_name: "sonarqube"

    static_configs:
      - targets:
          - SONAR-IP:9100

  - job_name: "monitoring"

    static_configs:
      - targets:
          - MONITORING-IP:9100
```

---

# 🔄 8. Restart Prometheus

## Run On

```text
Monitoring VM
```

If Running as Docker Container:

```bash
docker restart prometheus
```

Verify:

```bash
docker ps
```

---

# 🌐 9. Verify Prometheus UI

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
Prometheus Dashboard
```

---

# 🔍 10. Verify Targets

## Run On

```text
Browser
```

Navigation:

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

All Targets should be:

```text
State = UP
```

---

# 📈 11. Verify Prometheus Queries

CPU Usage:

```promql
100 - (avg by(instance)
(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

Memory Usage:

```promql
(node_memory_MemTotal_bytes
-
node_memory_MemAvailable_bytes)
/
node_memory_MemTotal_bytes
* 100
```

Disk Usage:

```promql
100 -
(
node_filesystem_avail_bytes
/
node_filesystem_size_bytes
*100
)
```

---

# 📊 12. Access Grafana

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
Grafana Home Page
```

---

# ⚙️ 13. Add Prometheus Data Source

## Run On

```text
Grafana UI
```

Navigation:

```text
Connections
→ Data Sources
→ Add Data Source
```

Select:

```text
Prometheus
```

URL:

```text
http://prometheus:9090
```

OR

```text
http://MONITORING-IP:9090
```

Click:

```text
Save & Test
```

Expected:

```text
Data source is working
```

---

# 📊 14. Import Node Exporter Dashboard

## Run On

```text
Grafana UI
```

Navigation:

```text
Dashboards
→ Import
```

Dashboard ID:

```text
1860
```

Click:

```text
Load
```

Select Data Source:

```text
Prometheus
```

Click:

```text
Import
```

Expected:

```text
Node Exporter Full Dashboard
```

---

# 📈 15. Dashboard Metrics

The dashboard should display:

```text
CPU Usage

Memory Usage

Disk Usage

Network Traffic

Load Average

Filesystem Usage

System Uptime
```

---

# 📊 16. Verify Jenkins Monitoring

Query:

```promql
up{job="jenkins"}
```

Expected:

```text
1
```

---

# 📊 17. Verify SonarQube Monitoring

Query:

```promql
up{job="sonarqube"}
```

Expected:

```text
1
```

---

# 📊 18. Verify Monitoring Server

Query:

```promql
up{job="monitoring"}
```

Expected:

```text
1
```

---

# 📋 19. Useful Prometheus Queries

CPU:

```promql
100 - (avg by(instance)
(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

Memory:

```promql
(node_memory_MemTotal_bytes
-
node_memory_MemAvailable_bytes)
/
node_memory_MemTotal_bytes
*100
```

Disk:

```promql
100 -
(
node_filesystem_avail_bytes
/
node_filesystem_size_bytes
*100
)
```

Node Status:

```promql
up
```

---

# 🔍 20. Validation Commands

## Run On

```text
Monitoring VM
```

Check Prometheus:

```bash
docker logs prometheus
```

Check Grafana:

```bash
docker logs grafana
```

Check Node Exporter:

```bash
systemctl status node_exporter
```

Check Port:

```bash
ss -tulpn | grep 9100
```

Expected:

```text
LISTEN 9100
```

---

# 🚨 Common Troubleshooting

## Target Down

Verify:

```bash
curl http://SERVER-IP:9100/metrics
```

Expected:

```text
node_cpu_seconds_total
```

---

## Node Exporter Not Running

Verify:

```bash
sudo systemctl status node_exporter
```

Restart:

```bash
sudo systemctl restart node_exporter
```

---

## Prometheus Not Scraping

Verify:

```bash
docker logs prometheus
```

Check:

```bash
prometheus.yml
```

---

## Grafana Dashboard Empty

Verify:

```text
Prometheus Datasource
```

Test:

```text
Save & Test
```

---

## Dashboard 1860 Not Showing Data

Verify:

```promql
up
```

Expected:

```text
1
```

Also confirm Dashboard Datasource:

```text
Prometheus
```

---

# ✅ Validation Checklist

## Prometheus

- [ ] Running
- [ ] Targets Up
- [ ] Metrics Collected

## Grafana

- [ ] Running
- [ ] Datasource Added
- [ ] Dashboard Imported

## Node Exporter

- [ ] Installed
- [ ] Running
- [ ] Port 9100 Reachable

## Monitoring

- [ ] Jenkins Monitored
- [ ] SonarQube Monitored
- [ ] Monitoring VM Monitored

---

# 📌 Expected Outcome

At the end of this phase:

✅ Prometheus Running

✅ Grafana Running

✅ Node Exporter Running

✅ Monitoring Dashboard Available

✅ Jenkins Metrics Available

✅ SonarQube Metrics Available

✅ Infrastructure Monitoring Enabled

---

# Next Document

```text
08-Troubleshooting-Guide.md
```
