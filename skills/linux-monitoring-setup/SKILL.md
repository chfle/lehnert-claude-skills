---
name: linux-monitoring-setup
description: Use when user wants to set up monitoring, observability, alerting, dashboards, uptime checks, metrics collection, or log aggregation for a Linux server, Docker stack, application, or infrastructure — including Prometheus, Grafana, Node Exporter, Loki, Alertmanager, Uptime Kuma, Netdata, or custom bash-based monitoring scripts.
version: 1.2.0
author: Lehnert
---

# Linux Monitoring Setup

## Overview

Designs and generates a complete monitoring stack tailored to the user's infrastructure and needs. Presents real stack options, lets the user choose, then writes all config files and a ready-to-run docker-compose.yml to disk. Covers metrics, logs, alerts, dashboards, and uptime monitoring.

**Language:** Respond in the user's language. All config files and comments in English.

---

## When to Use

- User wants to monitor a server, VPS, or Docker stack
- User asks about Prometheus, Grafana, Node Exporter, Loki, Alertmanager
- User wants uptime monitoring or a status page
- User wants log aggregation and search (ELK, Loki)
- User wants alerts for CPU, disk, memory, service down, HTTP errors
- User wants lightweight monitoring without heavy tools

## When NOT to Use

- User wants to analyze existing logs → `linux-log-analyzer`
- User wants to harden the server → `linux-security-hardener`
- User wants a general Docker stack → `docker-compose-writer`

---

## Step 1 — Understand the Context

Ask at most **two** questions if the answers aren't clear from the request.

**Question 1**: "What do you want to monitor?" — offer examples:
- A single Linux server (CPU, RAM, disk, network)
- A Docker host and its containers
- A web application (HTTP response time, error rates, uptime)
- Databases (PostgreSQL, MySQL, Redis)
- Everything — full-stack observability

**Question 2**: "How much infrastructure do you want to run for monitoring?"

| Tier | RAM | Containers | Best for |
|------|-----|------------|---------|
| **Lightweight** | ~100–150MB | 1–2 | Low-resource VPS, quick visibility |
| **Standard** | ~400–600MB | 3–4 | Most servers — Prometheus + Grafana |
| **Full** | ~1–2GB | 6–8 | Prod infra — metrics + logs + alerts |

- **Lightweight** — Netdata or Uptime Kuma or a bash monitoring script
- **Standard** — Prometheus + Grafana + Node Exporter (industry standard)
- **Full** — metrics + logs + traces + alerts (Prometheus + Grafana + Loki + Alertmanager)

If the user just says "set up monitoring for my server", default to **Standard — Prometheus + Grafana** for a single Linux server.

---

## Step 2 — Present Stack Options

Show options based on the user's context. Always recommend the best fit first.

### For a single Linux server (Standard)

> Here are the best monitoring stacks for a Linux server. Which fits your needs?
>
> **1. Prometheus + Grafana + Node Exporter** *(Recommended)*
> Industry-standard metrics stack. Node Exporter collects system metrics, Prometheus stores them, Grafana visualizes with pre-built dashboards. ~400MB RAM.
>
> **2. Netdata**
> Zero-config, real-time monitoring with a beautiful built-in UI. 1-minute install, very low overhead. Best for quick visibility without configuration. ~150MB RAM.
>
> **3. Uptime Kuma**
> Lightweight uptime and status page monitor. Checks HTTP/TCP/ping endpoints and sends alerts (Telegram, Slack, email). ~100MB RAM. Best for service availability.
>
> **4. Bash-based monitoring script**
> No containers, no dependencies. A cron-driven bash script that checks CPU, disk, memory, and services — emails or logs alerts. Zero overhead.

### For a Docker host

Present: **Prometheus + cAdvisor + Grafana** (container metrics) or **Dozzle** (log viewer) or **Portainer** (full management).

### For full-stack observability (Full)

Present: **Prometheus + Grafana + Loki + Promtail + Alertmanager** (the canonical full stack).

### For web application uptime only

Present: **Uptime Kuma** or **Gatus** (config-file-driven, more powerful).

---

## Stack Specifications

### Stack A — Prometheus + Grafana + Node Exporter (Standard)

**Components:**
- `prometheus` — scrapes and stores time-series metrics
- `node_exporter` — exposes Linux system metrics on port 9100
- `grafana` — visualization with pre-built Linux dashboards

**docker-compose.yml pattern:**
```yaml
services:
  prometheus:
    image: prom/prometheus:v2.51.0
    restart: unless-stopped
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention.time=30d'
      - '--web.enable-lifecycle'
    networks:
      - monitoring
    healthcheck:
      test: ["CMD", "wget", "-q", "--spider", "http://localhost:9090/-/healthy"]
      interval: 30s
      timeout: 10s
      retries: 3

  node_exporter:
    image: prom/node-exporter:v1.8.0
    restart: unless-stopped
    pid: host
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    networks:
      - monitoring

  grafana:
    image: grafana/grafana:10.4.0
    restart: unless-stopped
    environment:
      GF_SECURITY_ADMIN_USER: ${GRAFANA_USER:-admin}
      GF_SECURITY_ADMIN_PASSWORD: ${GRAFANA_PASSWORD}
      GF_USERS_ALLOW_SIGN_UP: "false"
      GF_INSTALL_PLUGINS: grafana-clock-panel
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning:ro
    networks:
      - monitoring
    depends_on:
      prometheus:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "-q", "--spider", "http://localhost:3000/api/health"]
      interval: 30s
      timeout: 10s
      retries: 3

networks:
  monitoring:
    driver: bridge

volumes:
  prometheus_data:
  grafana_data:
```

**prometheus.yml:**
```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node'
    static_configs:
      - targets: ['node_exporter:9100']

  # Add application-specific scrape targets below:
  # - job_name: 'myapp'
  #   static_configs:
  #     - targets: ['myapp:8080']   # must expose /metrics endpoint
```

**Grafana dashboard IDs to import:**
- `1860` — Node Exporter Full (most popular Linux dashboard)
- `893` — Docker and system monitoring
- `11074` — Node Exporter for Prometheus Dashboard

---

### Stack B — Full Stack (Prometheus + Grafana + Loki + Promtail + Alertmanager)

Add to Stack A:

**Loki** — log aggregation, stores logs from Promtail
**Promtail** — log shipper, reads from `/var/log` and Docker containers
**Alertmanager** — routes Prometheus alerts to Slack, email, PagerDuty

```yaml
  loki:
    image: grafana/loki:2.9.5
    restart: unless-stopped
    command: -config.file=/etc/loki/local-config.yaml
    volumes:
      - ./loki/loki-config.yaml:/etc/loki/local-config.yaml:ro
      - loki_data:/loki
    networks:
      - monitoring

  promtail:
    image: grafana/promtail:2.9.5
    restart: unless-stopped
    volumes:
      - /var/log:/var/log:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - ./promtail/promtail-config.yaml:/etc/promtail/config.yaml:ro
    command: -config.file=/etc/promtail/config.yaml
    networks:
      - monitoring

  alertmanager:
    image: prom/alertmanager:v0.27.0
    restart: unless-stopped
    volumes:
      - ./alertmanager/alertmanager.yml:/etc/alertmanager/alertmanager.yml:ro
      - alertmanager_data:/alertmanager
    networks:
      - monitoring
```

---

### Stack C — Uptime Kuma (Lightweight uptime monitoring)

```yaml
services:
  uptime-kuma:
    image: louislam/uptime-kuma:1
    restart: unless-stopped
    volumes:
      - uptime_kuma_data:/app/data
    ports:
      - "3001:3001"
    healthcheck:
      test: ["CMD", "node", "extra/healthcheck.js"]
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  uptime_kuma_data:
```

---

### Stack D — Netdata (Zero-config real-time monitoring)

```yaml
services:
  netdata:
    image: netdata/netdata:stable
    restart: unless-stopped
    pid: host
    cap_add:
      - SYS_PTRACE
      - SYS_ADMIN
    security_opt:
      - apparmor:unconfined
    volumes:
      - netdata_config:/etc/netdata
      - netdata_lib:/var/lib/netdata
      - netdata_cache:/var/cache/netdata
      - /etc/passwd:/host/etc/passwd:ro
      - /etc/group:/host/etc/group:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /etc/os-release:/host/etc/os-release:ro
    environment:
      NETDATA_CLAIM_TOKEN: ${NETDATA_CLAIM_TOKEN:-}
      NETDATA_CLAIM_URL: https://app.netdata.cloud
    ports:
      - "19999:19999"

volumes:
  netdata_config:
  netdata_lib:
  netdata_cache:
```

---

### Stack E — Docker host monitoring (Prometheus + cAdvisor + Node Exporter + Grafana)

Add cAdvisor to Stack A for container metrics:

```yaml
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:v0.49.1
    restart: unless-stopped
    privileged: true
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
    networks:
      - monitoring
```

Add to prometheus.yml:
```yaml
  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']
```

---

## Alert Rules

When generating full stack, always include a `prometheus/alert.rules.yml` with these critical alerts:

```yaml
groups:
  - name: critical
    rules:
      - alert: HostHighCpuLoad
        expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High CPU load on {{ $labels.instance }}"

      - alert: HostLowDisk
        expr: (node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) * 100 < 20
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Low disk space on {{ $labels.instance }}: {{ $value | printf \"%.1f\" }}% free"

      - alert: HostOutOfMemory
        expr: (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100 < 10
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Out of memory on {{ $labels.instance }}"

      - alert: HostDown
        expr: up == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Host {{ $labels.instance }} is down"

      - alert: HostHighLoad
        expr: node_load15 > 4
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High system load on {{ $labels.instance }}"
```

---

## Output Format

Write all files silently to `./monitoring/` in the current working directory.

Structure:
```
monitoring/
  docker-compose.yml
  .env.example
  prometheus/
    prometheus.yml
    alert.rules.yml          (if alerting configured)
  grafana/
    provisioning/
      datasources/
        prometheus.yml
      dashboards/
        dashboard.yml
  loki/
    loki-config.yaml         (if Loki included)
  promtail/
    promtail-config.yaml     (if Promtail included)
  alertmanager/
    alertmanager.yml         (if Alertmanager included)
```

Then print ONLY:

```
✅ Monitoring stack created in ./monitoring/

▶ To start:
  cd monitoring
  cp .env.example .env && nano .env
  docker compose up -d

▶ Verify everything is running:
  docker compose ps
  docker compose logs -f

🌐 Grafana:      http://localhost:3000  (admin / your-password)
🌐 Prometheus:   http://localhost:9090
🌐 Alertmanager: http://localhost:9093  (if configured)

📊 Import dashboards in Grafana:
   Dashboards → Import → Enter ID: 1860 (Node Exporter Full)
   Dashboards → Import → Enter ID: 893  (Docker monitoring)

💡 Next: /linux-security-hardener to lock down the monitoring ports before exposing to internet
```

Adjust URLs to match the actual services generated.
