# 🖥️ Monitoring Lab — Prometheus, Node Exporter & Grafana

## 📌 Project Overview

This project demonstrates how to monitor Linux system performance using **Prometheus, Node Exporter, Grafana, and Docker Compose**.

The monitoring stack collects CPU, memory, disk, and network metrics and displays them through a Grafana dashboard.

### 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| Docker | Runs applications inside containers |
| Docker Compose | Starts and manages multiple containers |
| Node Exporter | Collects Linux system metrics |
| Prometheus | Collects and stores monitoring metrics |
| Grafana | Displays monitoring dashboards |

## 🔄 Monitoring Architecture

```text
        ┌─────────────────────┐
        │    Ubuntu Linux     │
        │  CPU, RAM, Disk     │
        │      Network        │
        └──────────┬──────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │    Node Exporter    │
        │     Port 9100       │
        └──────────▲──────────┘
                   │
             Scrapes every
              15 seconds
                   │
        ┌──────────┴──────────┐
        │     Prometheus      │
        │      Port 9090      │
        │  Stores metrics     │
        └──────────▲──────────┘
                   │
              Queries data
                   │
        ┌──────────┴──────────┐
        │       Grafana       │
        │      Port 3000      │
        │ Displays dashboards │
        └─────────────────────┘
```

**How it works:**

1. Node Exporter reads Linux system metrics.
2. Prometheus collects those metrics every 15 seconds.
3. Prometheus stores the metrics as time-series data.
4. Grafana queries Prometheus.
5. Grafana displays the results as graphs.

Prometheus also monitors itself.

## 📚 Basic Monitoring Concepts

| Term | Meaning |
|---|---|
| Metric | A numerical measurement, such as CPU usage |
| Target | A service from which Prometheus collects metrics |
| Scrape | The process of collecting metrics |
| Time series | Metric values recorded over time |
| Label | Additional information identifying a metric |
| Pull-based monitoring | Prometheus requests metrics from targets |

## 🚀 Installation and Setup

### Requirements

- Ubuntu Linux
- Docker Engine
- Docker Compose
- Web browser

### Step 1: Clone the Repository

```bash
git clone https://github.com/pvkms20/monitoring-lab.git
cd monitoring-lab
```

### Step 2: Start the Monitoring Stack

```bash
docker compose up -d
```

This starts three containers:

- Node Exporter
- Prometheus
- Grafana

### Step 3: Verify Containers

```bash
docker ps
```

All three containers should show a running status.

### Step 4: Access the Services

| Service | Address |
|---|---|
| Node Exporter | http://localhost:9100/metrics |
| Prometheus | http://localhost:9090 |
| Prometheus Targets | http://localhost:9090/targets |
| Grafana | http://localhost:3000 |

For a fresh Grafana installation, the initial username and password are normally `admin` and `admin`. Change the default password when prompted.

### Step 5: Connect Grafana to Prometheus

1. Open Grafana.
2. Navigate to Connections → Data sources.
3. Select Prometheus.
4. Enter the server URL:

```text
http://prometheus:9090
```

5. Click Save & test.

A successful test confirms Grafana can communicate with Prometheus.

## 📊 Five Important Metrics

| Metric | Meaning |
|---|---|
| `node_cpu_seconds_total` | CPU time spent in different operating modes |
| `node_memory_MemTotal_bytes` | Total system memory |
| `node_memory_MemAvailable_bytes` | Memory available for applications |
| `node_filesystem_avail_bytes` | Available filesystem storage |
| `node_network_receive_bytes_total` | Total network bytes received |

## 📈 PromQL Queries

### 1. CPU Usage %

```promql
100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

Shows the percentage of CPU time spent doing work.

### 2. Memory Usage %

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

Shows RAM usage percentage.

### 3. Disk Usage %

```promql
(1 - node_filesystem_avail_bytes{mountpoint="/",fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{mountpoint="/",fstype!~"tmpfs|overlay"}) * 100
```

Shows disk usage percentage for the root filesystem.

### 4. Network Incoming Traffic

```promql
sum(rate(node_network_receive_bytes_total{device!="lo"}[5m]))
```

Shows incoming network traffic in bytes per second.

### 5. Network Outgoing Traffic

```promql
sum(rate(node_network_transmit_bytes_total{device!="lo"}[5m]))
```

Shows outgoing network traffic in bytes per second.

### 6. System Uptime

```promql
time() - node_boot_time_seconds
```

Shows how long the monitored Linux environment has been running.

### 7. Targets UP/DOWN

```promql
up
```

- `1` = Target is UP.
- `0` = Target is DOWN.

## 🖥️ Grafana Dashboard

The dashboard is named **System overview**.

It contains six panels:

1. CPU Usage %
2. Memory Usage %
3. Disk Usage %
4. Network In/Out
5. System Uptime
6. Targets UP/DOWN

### Import the Dashboard

The exported dashboard is stored at:

```text
dashboards/system-overview.json
```

To import it:

1. Open Grafana.
2. Navigate to Dashboards.
3. Select New → Import dashboard.
4. Upload `dashboards/system-overview.json`.
5. Select the Prometheus data source if prompted.
6. Complete the import.

## 🧪 Monitoring Tests

### Verify Prometheus Targets

Open:

```text
http://localhost:9090/targets
```

Both Prometheus and Node Exporter should show UP.

### Test Service Failure

Stop Node Exporter:

```bash
docker compose stop node-exporter
```

Wait for Prometheus to perform another scrape.

Node Exporter should show DOWN.

Restart it:

```bash
docker compose start node-exporter
```

The target should return to UP after a successful scrape.

### Test System Load

Run a CPU-intensive task on the monitored Linux environment and observe whether the Grafana CPU graph changes.

## 🛑 Stop the Monitoring Stack

```bash
docker compose down
```

This stops and removes the monitoring containers and Compose network while preserving the named data volumes.

Restart with:

```bash
docker compose up -d
```

## 🔐 Security

- Do not commit passwords or access tokens.
- Do not commit `.env` files containing secrets.
- Change Grafana's default administrator password.
- Keep monitoring services restricted to trusted environments.

## 📁 Project Structure

```text
monitoring-lab/
├── docker-compose.yml
├── prometheus.yml
├── README.md
└── dashboards/
    └── system-overview.json
```

## ✅ Conclusion

This project demonstrates a basic monitoring system using Docker Compose, Node Exporter, Prometheus, and Grafana.

It provides experience with container management, monitoring configuration, PromQL queries, dashboard creation, and troubleshooting.
