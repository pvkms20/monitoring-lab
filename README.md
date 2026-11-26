# Monitoring Lab

A DevOps monitoring project using Docker Compose, Prometheus,
Node Exporter, and Grafana.

## Tools
- Node Exporter: Collects Linux system metrics.
- Prometheus: Collects and stores metrics every 15 seconds.
- Grafana: Displays monitoring dashboards.

## Start the Project

docker compose up -d

## Access

- Prometheus: http://localhost:9090
- Node Exporter: http://localhost:9100/metrics
- Grafana: http://localhost:3000

## Monitoring Flow

Linux System → Node Exporter → Prometheus → Grafana
