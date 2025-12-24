# 📊 Prometheus and Grafana (Beginner Friendly)

---

## 🔹 Prometheus

### What is Prometheus?

Prometheus is a **monitoring tool**.

* It **collects (scrapes)** performance metrics from:

  * CPU
  * Memory
  * Disk
  * Network
  * VM, Cloud VM, Servers, Routers, etc.
* Prometheus **stores metrics with timestamp**.
* We can query data using **PromQL (Prometheus Query Language)**.
* It has a **powerful API**, so it can integrate with almost any system.
* It is **highly scalable** and supports **third-party exporters**.
* Monitoring and alerting are very important to **measure application health in production**.

👉 To check whether target machines are up or not:

```
http://localhost:9090/targets
```

---

## ✅ Advantages of Prometheus

1. Prometheus is **independent** and does not affect cloud or on-prem infrastructure
2. It can be **customized** based on infrastructure needs
3. Only required **exporters** are installed → less hardware overhead
4. Exporters can be **custom-built**
5. If an exporter is not available, we can **create our own exporter**
6. Comes with **PromQL + TSDB**, very useful for troubleshooting
7. Has **built-in database**, no need for extra DB
8. Uses **pull mechanism**, very flexible scrape intervals
9. **Open source** with strong community support

---

## 🔹 Node Exporter

### What is Node Exporter?

* Node Exporter **collects hardware and OS metrics**
* It exposes metrics in a format **Prometheus understands**
* Runs on **port 9100**
* Common metrics:

  * CPU usage
  * Memory usage
  * Disk usage
  * Network usage
* Collects **kernel-level and system-level metrics**

🧠 **Simple analogy:**
Node Exporter is like a **sensor** installed on a machine.
Prometheus comes and **reads data from the sensor**.

---

## 🔹 Grafana

### What is Grafana?

* Grafana is a **visualization tool**
* It **displays metrics** collected by Prometheus
* Grafana **does not store data**
* It uses Prometheus as a **data source**
* Used for:

  * Dashboards
  * Alerts
  * Email notifications
* Grafana Labs provides **ready-made dashboards**

🧠 **Analogy:**
Prometheus = Data collector
Grafana = TV screen to view that data

---

## ✅ Advantages of Grafana

1. Supports **multiple data sources** (Prometheus, Loki, InfluxDB, etc.)
2. Dashboards are **fully customizable**
3. Multiple data sources in **one dashboard**
4. Community dashboards → **no need to reinvent the wheel**
5. Dashboards look **clean and professional**
6. Strong **open-source community**
7. Supports images, alerts, and many plugins

---

# 🛠️ Prometheus Installation

## 🔹 Step 1: Install Prometheus

Official website:
👉 [https://prometheus.io/download/](https://prometheus.io/download/)

### Download binary

```
wget https://github.com/prometheus/prometheus/releases/download/v2.45.1/prometheus-2.45.1.linux-amd64.tar.gz
```

### Extract

```
tar -xvzf prometheus-2.45.1.linux-amd64.tar.gz
cd prometheus-2.45.1.linux-amd64
```

### Start Prometheus

```
./prometheus
```

👉 Access Prometheus:

```
http://localhost:9090
```

---

## 🔹 Step 2: Install Node Exporter

Official site:
👉 [https://prometheus.io/download/#node_exporter](https://prometheus.io/download/#node_exporter)

### Download

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz
```

### Extract

```
tar -xvzf node_exporter-1.7.0.linux-amd64.tar.gz
cd node_exporter-1.7.0.linux-amd64
```

### Start Node Exporter

```
./node_exporter
```

👉 Access Node Exporter:

```
http://localhost:9100
```

---

## 🔹 Step 3: Configure Prometheus to Scrape Node Exporter

Create config file:

```
vim prometheus.yml
```

### Configuration

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "node"
    static_configs:
      - targets: ['localhost:9100', '100.0.0.3:9100']
```

### Start Prometheus with config

```
./prometheus --config.file=prometheus.yml
```

👉 Now Prometheus scrapes metrics from both machines

---

## 🔹 Step 4: Install Grafana (Ubuntu)

Official docs:
👉 [https://grafana.com/docs/grafana/latest/setup-grafana/installation/](https://grafana.com/docs/grafana/latest/setup-grafana/installation/)

### Commands

```
sudo apt-get install -y apt-transport-https software-properties-common wget
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get install grafana
```

### Start Grafana

```
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

👉 Access Grafana:

```
http://localhost:3000/login
```

Default login:

* Username: **admin**
* Password: **admin**

---

## 🔹 Step 5: Configure Grafana Dashboard

### Add Prometheus Data Source

1. Grafana → Home
2. Connections → Data Sources
3. Add data source → Prometheus
4. URL: `http://localhost:9090`
5. Save & Test

---

### Import Dashboard from Grafana Labs

1. Go to Grafana → Create → Import
2. Enter Dashboard ID: **2747**
3. Click Load → Import
4. Linux memory dashboard will appear

---

## 🔹 Run Node Exporter in Background

```
nohup ./node_exporter > node_exporter.log 2>&1 &
```

