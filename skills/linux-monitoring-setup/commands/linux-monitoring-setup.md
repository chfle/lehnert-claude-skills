# /linux-monitoring-setup

Set up a complete monitoring stack for any Linux server, Docker host, or application.

## Usage

```
/linux-monitoring-setup <what to monitor>
```

## Examples

```
/linux-monitoring-setup my Linux VPS
/linux-monitoring-setup Docker host with container metrics
/linux-monitoring-setup full stack with logs and alerts
/linux-monitoring-setup lightweight uptime monitoring for my websites
/linux-monitoring-setup Prometheus Grafana for my server
```

## Behavior

If the target and scope are provided, use them directly.
If not, ask at most two questions: what to monitor, and how much infrastructure to run.

Presents stack options (Prometheus+Grafana, Netdata, Uptime Kuma, full Loki stack, or bash-based) — user picks a number.

After selection:
1. Write all config files silently to `./monitoring/` (docker-compose.yml, prometheus.yml, alert rules, Grafana provisioning, Loki/Promtail if chosen)
2. Print only: confirmation, start commands, dashboard URLs with login, Grafana dashboard IDs to import

$ARGUMENTS
