# /linux-monitoring-setup

Set up a complete monitoring stack for any Linux server, Docker host, or application — writes all configs to disk.

## Usage

```
/linux-monitoring-setup <what to monitor>
```

## Examples

```
/linux-monitoring-setup my Ubuntu VPS — CPU, RAM, disk, and services
/linux-monitoring-setup Docker host with per-container CPU and memory metrics
/linux-monitoring-setup full stack: metrics + logs + alerting on Slack
/linux-monitoring-setup lightweight uptime monitoring for 5 websites
/linux-monitoring-setup Prometheus + Grafana for my PostgreSQL server
/linux-monitoring-setup alert me when disk is above 80% or any service is down
/linux-monitoring-setup self-hosted status page for my services
```

## Generates

```
monitoring/
  docker-compose.yml        ← full monitoring stack
  prometheus/
    prometheus.yml          ← scrape configs
    rules/alerts.yml        ← alert rules (disk, CPU, service down, etc.)
  grafana/
    provisioning/           ← auto-provisioned datasources and dashboards
  loki/
    loki-config.yml         ← log aggregation (if chosen)
  promtail/
    promtail-config.yml     ← log shipping (if chosen)
```

## Behavior

If the target and scope are clear, present stack options immediately.
If unclear, ask at most two questions: what to monitor, and how much infra to run.

Stack options (user picks a number):
1. **Prometheus + Grafana + Node Exporter** — full server metrics
2. **Full stack** — metrics + Loki + Promtail + Alertmanager (logs and alerts)
3. **Netdata** — lightweight, zero-config, real-time
4. **Uptime Kuma** — uptime monitoring + status page
5. **Docker + cAdvisor + Grafana** — container metrics focus

After selection:
1. Write all config files silently to `./monitoring/`
2. Print: `docker compose up -d` command, dashboard URLs with default logins, Grafana dashboard IDs to import
3. Suggest: set up `/linux-backup-restore` to back up Grafana volumes and Prometheus data

$ARGUMENTS
