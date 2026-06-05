# 🚀 Production-Ready Automated Monitoring Stack

[![Docker Compose](https://img.shields.io/badge/Docker_Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=Prometheus&logoColor=white)](https://prometheus.io/)
[![Grafana](https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=Grafana&logoColor=white)](https://grafana.com/)

A fully automated, GitOps-compliant monitoring and observability stack featuring **Prometheus**, **Grafana**, **Alertmanager**, and **Node Exporter**. Designed with zero manual intervention in mind, this stack auto-provisions its own datasources, dashboards, and alerting rules out of the box.

---

## 🔥 Key Features

* **Zero-Touch Configuration (Auto-Provisioning):** Grafana automatically maps and connects to Prometheus via native YAML configurations upon boot. No clicking around the UI to manually add datasources.
* **Pre-baked Dashboards:** Includes comprehensive, production-ready Grafana dashboards (e.g., Node Exporter Full) pre-loaded directly through the container lifecycle.
* **Isolated Network Architecture:** Secure container networking setup via Docker Compose, isolating internal metrics scraping while exposing only the essential visualization layer.

---

## 🏗️ Architecture & Component Flow
[ Target: Node Exporter ] ---> ( Scraped by Prometheus ) ---> [ Alertmanager (Alerts) ]
|
v
[ Grafana Dashboards ]

* **Prometheus:** Handles metric collection, time-series data storage, and evaluation of alerting expressions.
* **Grafana:** Provides rich, automated visualizations powered by pre-built provisioning configurations.
* **Alertmanager:** Deduplicates, groups, and routes alerts triggered by Prometheus to target notification channels.
* **Node Exporter:** Collects hardware and OS-level metrics from the host machine.

---

## 📂 Repository Structure

```text
.
├── docker-compose.yml          # Container orchestration definition
├── prometheus/
│   ├── prometheus.yml          # Scrape configurations and global settings
│   └── alert.rules.yml         # Custom alerting logic (CPU, RAM, Disk)
├── alertmanager/
│   └── alertmanager.yml        # Notification routing configuration
└── grafana/
    └── provisioning/
        ├── datasources/
        │   └── datasource.yml  # Automated Prometheus connection
        └── dashboards/
            ├── dashboards.yml  # Dashboard provider definitions
            └── node-exporter.json # Pre-baked Node Exporter dashboard
```
## 🚀 Quick Start

### Prerequisites

Ensure you have **Docker** and **Docker Compose** installed on your target machine.

### Deployment

Spin up the entire telemetry stack with a single command:

```bash
docker compose up -d
```

Verify that all containers are healthy and running:

```bash
docker compose ps
```

Access your analytics platform at `http://your-server-ip:3000`

> **Default credentials:** `admin` / `admin`

---

## ⚙️ Technical Deep Dive (The DevOps Approach)

### Infrastructure as Code (IaC) for Grafana

Instead of treating Grafana as a stateful application requiring manual UI setup, this project treats dashboards and datasources as **immutable code**.

The `datasource.yml` ensures that the Prometheus binding is established seamlessly on startup:

```yaml
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
```

### Smart Alerting Rules

Prometheus continuously evaluates the host metrics. For instance, the critical disk space alert is calculated dynamically based on real-time filesystems:

```yaml
expr: (node_filesystem_free_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) * 100 < 10
for: 5m
labels:
  severity: critical
annotations:
  summary: "Low disk space on {{ $labels.instance }}"
```

> **My custom-built dashboard:** 

<img width="1365" height="607" alt="image" src="https://github.com/user-attachments/assets/a2901df7-2a89-47a6-842f-7fd4dc85bf98" />



