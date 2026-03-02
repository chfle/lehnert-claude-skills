---
name: linux-systemd-manager
description: Use when user wants to create, edit, debug, or optimize a systemd service, timer, socket, mount, or path unit — or asks how to run something as a service, auto-restart a process, schedule tasks with systemd timers, understand journalctl output, troubleshoot a failed unit, or manage systemd targets and dependencies.
version: 1.2.0
author: Lehnert
---

# Linux Systemd Manager

## Overview

Writes, debugs, and optimizes systemd units of all types. Covers service sandboxing, security hardening, resource limits, restart policies, timer scheduling, socket activation, and dependency management. Writes all unit files to disk with the exact install commands to activate them.

**Language:** Respond in the user's language. Unit files and comments in English.

---

## When to Use

- User wants to run any process as a systemd service
- User wants to schedule tasks with a systemd timer (cron replacement)
- User has a failed or misbehaving service and wants to debug it
- User wants to harden an existing service unit
- User asks "how do I auto-start X on boot?"
- User wants socket activation, path units, or mount units
- User wants to understand or use `journalctl`

## When NOT to Use

- User only wants simple cron scheduling → /linux-cron-manager
- User wants to audit a service config file → `linux-config-auditor`
- User wants to write the application being run → `linux-shell-scriptor`

---

## Step 1 — Detect Unit Type

Map the user's request to the correct unit type:

| User says | Unit type |
|-----------|-----------|
| "run as a service", "auto-start on boot", "keep process running" | `.service` |
| "run on a schedule", "every hour", "cron replacement" | `.timer` + `.service` |
| "start when file changes", "watch a directory" | `.path` + `.service` |
| "socket activation", "start on connection" | `.socket` + `.service` |
| "mount a filesystem", "auto-mount" | `.mount` or `.automount` |
| "debug a failed service", "why did it crash?" | Debugging workflow |
| "harden my service unit" | Security hardening audit |

---

## Step 2 — Gather Context

Ask at most **one** question if critical info is missing:
- What command/binary does the service run?
- Which user should it run as?
- Does it need to start after a specific service (database, network)?

If the user gives enough context (command, user, schedule), generate immediately.

---

## Service Unit — Full Template

Always generate a complete, well-commented unit. Never produce a bare-minimum unit.

```ini
[Unit]
Description=My Application Service
Documentation=https://example.com/docs
After=network-online.target
Wants=network-online.target
# After=postgresql.service   # uncomment if app needs a database

[Service]
Type=simple                  # simple | forking | oneshot | notify | idle
User=appuser
Group=appuser
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/bin/myapp --config /etc/myapp/config.yaml
ExecReload=/bin/kill -HUP $MAINPID

# Restart policy
Restart=on-failure
RestartSec=5s
StartLimitIntervalSec=60s
StartLimitBurst=3

# Environment
EnvironmentFile=-/etc/myapp/myapp.env
Environment="NODE_ENV=production"

# Logging
StandardOutput=journal
StandardError=journal
SyslogIdentifier=myapp

# Resource limits
LimitNOFILE=65536
LimitNPROC=4096
# MemoryMax=512M              # uncomment to cap memory usage
# CPUQuota=50%                # uncomment to cap CPU usage

# Security hardening
NoNewPrivileges=yes
PrivateTmp=yes
ProtectSystem=strict
ProtectHome=yes
ReadWritePaths=/var/lib/myapp /var/log/myapp
StateDirectory=myapp          # creates /var/lib/myapp with correct ownership automatically
LogsDirectory=myapp           # creates /var/log/myapp with correct ownership automatically
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes
RestrictAddressFamilies=AF_INET AF_INET6 AF_UNIX
RestrictNamespaces=yes
LockPersonality=yes
MemoryDenyWriteExecute=yes
RestrictRealtime=yes
CapabilityBoundingSet=       # Drop ALL capabilities (recommended)
# Exception: if app must bind port < 1024:
# CapabilityBoundingSet=CAP_NET_BIND_SERVICE
# AmbientCapabilities=CAP_NET_BIND_SERVICE
AmbientCapabilities=
SystemCallFilter=@system-service
SystemCallErrorNumber=EPERM

[Install]
WantedBy=multi-user.target
```

---

## Service Types Reference

| Type | When to use | ExecStart behavior |
|------|-------------|-------------------|
| `simple` | Most apps — process runs in foreground | PID of ExecStart is the main process |
| `forking` | Old-style daemons that fork to background | Parent exits after fork; use `PIDFile=` |
| `oneshot` | Scripts that run and exit (batch jobs) | systemd waits for exit before `active` |
| `notify` | Apps using `sd_notify()` to signal ready | Process sends `READY=1` when ready |
| `exec` | Like simple but waits for exec to succeed | More precise readiness tracking |
| `idle` | Delay start until job queue is empty | Avoids boot output clutter |

---

## Timer Unit (Cron Replacement)

**Always generate a timer + its companion service as a pair.**

> **Naming rule:** The timer and service must share the same base name — `myapp-task.timer` activates `myapp-task.service` automatically. No `Requires=` needed in the timer.

`/etc/systemd/system/myapp-task.service`:
```ini
[Unit]
Description=My App Scheduled Task
After=network-online.target

[Service]
Type=oneshot
User=appuser
ExecStart=/opt/myapp/bin/myapp --task run
StandardOutput=journal
StandardError=journal
SyslogIdentifier=myapp-task

# Security
NoNewPrivileges=yes
PrivateTmp=yes
ProtectSystem=strict
ProtectHome=yes
```

`/etc/systemd/system/myapp-task.timer`:
```ini
[Unit]
Description=Run My App Task daily at 2 AM

[Timer]
OnCalendar=*-*-* 02:00:00   # daily at 2 AM
# OnCalendar=hourly          # every hour
# OnCalendar=weekly          # every Monday at 00:00
# OnBootSec=5min             # 5 minutes after boot
# OnUnitActiveSec=1h         # 1 hour after last run
AccuracySec=1min
RandomizedDelaySec=30s       # spread load — avoid thundering herd
Persistent=true              # run missed timers after downtime (use for backups, reports)
# Persistent=false           # skip missed runs (use for metrics, health checks)

[Install]
WantedBy=timers.target
```

**OnCalendar quick reference:**

| Schedule | Expression |
|----------|-----------|
| Every minute | `*:0/1` |
| Every 5 minutes | `*:0/5` |
| Every hour at :00 | `hourly` |
| Daily at 2 AM | `*-*-* 02:00:00` |
| Every Monday 3 AM | `Mon *-*-* 03:00:00` |
| 1st of month at midnight | `*-*-01 00:00:00` |
| Weekdays at 9 AM | `Mon..Fri *-*-* 09:00:00` |

Verify expression: `systemd-analyze calendar "Mon *-*-* 03:00:00"`

---

## Socket Activation Unit

Allows systemd to listen on a port or Unix socket and start the service only when a connection arrives.

**`Accept=no` vs `Accept=yes`:**

| Value | Behavior | Use case |
|-------|----------|----------|
| `Accept=no` | One service instance handles all connections | Long-lived servers (web apps, databases) |
| `Accept=yes` | Spawn one service instance per connection | Simple per-connection handlers (inetd style) |

Most modern apps use `Accept=no`.

**TCP socket (`/etc/systemd/system/myapp.socket`):**
```ini
[Unit]
Description=My App Socket

[Socket]
ListenStream=8080
Accept=no
SocketUser=appuser

[Install]
WantedBy=sockets.target
```

**Unix domain socket (for local IPC — nginx, PHP-FPM, gunicorn):**
```ini
[Unit]
Description=My App Unix Socket

[Socket]
ListenStream=/run/myapp/myapp.sock
SocketMode=0660
SocketUser=appuser
SocketGroup=www-data

[Install]
WantedBy=sockets.target
```

`/etc/systemd/system/myapp.service` (socket-activated):
```ini
[Unit]
Description=My App (socket-activated)
Requires=myapp.socket
After=myapp.socket

[Service]
Type=simple
User=appuser
ExecStart=/opt/myapp/bin/myapp
StandardInput=socket
NoNewPrivileges=yes
PrivateTmp=yes
RuntimeDirectory=myapp       # creates /run/myapp with correct ownership
```

---

## Path Unit (Watch Files or Directories)

Triggers a service when a file or directory changes.

`/etc/systemd/system/watch-uploads.path`:
```ini
[Unit]
Description=Watch /var/uploads for new files

[Path]
PathExistsGlob=/var/uploads/*.pending
DirectoryNotEmpty=/var/uploads
Unit=process-uploads.service

[Install]
WantedBy=multi-user.target
```

---

## Security Hardening Options

When auditing or hardening an existing unit, check and add these options:

| Option | Effect | Recommended |
|--------|--------|-------------|
| `NoNewPrivileges=yes` | Blocks privilege escalation via setuid/capabilities | Always |
| `PrivateTmp=yes` | Gives service its own /tmp — isolates temp files | Always |
| `ProtectSystem=strict` | Mounts /, /usr, /boot read-only | Always for non-system services |
| `ProtectHome=yes` | Blocks access to /home, /root, /run/user | Always unless needed |
| `ReadWritePaths=` | Whitelist specific writable paths | Required with ProtectSystem=strict |
| `PrivateDevices=yes` | No access to device nodes | For most services |
| `ProtectKernelTunables=yes` | Blocks /proc/sys writes | Always |
| `ProtectKernelModules=yes` | Blocks module loading | Always |
| `ProtectControlGroups=yes` | Blocks cgroup changes | Always |
| `RestrictAddressFamilies=` | Limit network families | Set to AF_INET AF_INET6 for web services |
| `CapabilityBoundingSet=` | Drop all Linux capabilities | Set unless specific cap needed |
| `SystemCallFilter=@system-service` | Block dangerous syscalls | Always for app services |
| `MemoryDenyWriteExecute=yes` | Block JIT/self-modifying code | Unless JVM/JS runtime |
| `User=` | Run as non-root | Always |
| `LimitNOFILE=` | Set open file descriptor limit | For high-connection services |
| `MemoryMax=` | Cap memory usage | For resource-controlled services |
| `CPUQuota=` | Cap CPU percentage | For background services |

Check current security score: `systemd-analyze security myservice.service`

---

## Debugging Workflow

When a service fails, run these in order:

```bash
# 1. Check current status and last error
systemctl status myservice.service

# 2. View full logs (no line limit)
journalctl -u myservice.service -n 100 --no-pager

# 3. Follow logs in real time while starting
journalctl -u myservice.service -f &
systemctl start myservice.service

# 4. Check for dependency failures
systemctl list-dependencies myservice.service
systemctl --failed

# 5. Validate unit file syntax
systemd-analyze verify /etc/systemd/system/myservice.service

# 6. Check what the service environment looks like
systemctl show myservice.service
systemctl cat myservice.service   # show effective unit (including overrides)

# 7. Run the ExecStart command manually as the service user to see raw errors
sudo -u appuser /opt/myapp/bin/myapp --config /etc/myapp/config.yaml

# 8. Check security restrictions blocking the service
systemd-analyze security myservice.service

# 9. See what slowed boot (identify long-starting services)
systemd-analyze blame
systemd-analyze critical-chain myservice.service

# 10. Inspect cgroup resource usage (live)
systemd-cgls
systemctl status myservice.service   # shows CPU + memory under cgroup
```

**Common failure causes:**

| Error | Likely cause | Fix |
|-------|-------------|-----|
| `code=exited, status=1` | App crashed — check logs | `journalctl -u service -n 50` |
| `code=exited, status=200/CHDIR` | WorkingDirectory doesn't exist | Create the directory |
| `code=exited, status=203/EXEC` | ExecStart binary not found or not executable | Check path and permissions |
| `start request repeated too quickly` | Crash loop — app failing immediately | Fix app error, set `StartLimitBurst` |
| `Permission denied` | User lacks access to file/socket | Check ReadWritePaths, user permissions |
| `Failed to set up namespace` | Sandboxing conflicts with app | Disable ProtectSystem or add ReadWritePaths |

---

## Output Format

Write all unit files to `./systemd/` in the current working directory.

```
systemd/
  myservice.service
  myservice.timer       (if timer requested)
  myservice.socket      (if socket activation requested)
  myservice.path        (if path unit requested)
```

Then print ONLY:

```
✅ Unit files written to ./systemd/

▶ Install and enable:
  sudo cp systemd/*.service systemd/*.timer /etc/systemd/system/
  sudo systemctl daemon-reload
  sudo systemctl enable --now myservice.service
  [sudo systemctl enable --now myservice.timer]

▶ Verify:
  systemctl status myservice.service
  journalctl -u myservice.service -n 20

▶ Security score:
  systemd-analyze security myservice.service

💡 Next: /linux-config-auditor to review the service config file · /linux-security-hardener for host-level hardening
```
