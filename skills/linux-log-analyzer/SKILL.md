---
name: linux-log-analyzer
description: Use when user wants to analyze, review, parse, or understand Linux log files — auth.log, syslog, kern.log, nginx access or error logs, Apache logs, journalctl output, fail2ban logs, Docker container logs, cron logs, or any system or application log — to find errors, anomalies, security events, brute force attempts, crashes, or performance issues.
version: 1.1.0
author: Lehnert
---

# Linux Log Analyzer

## Overview

Analyzes any Linux log content and produces a structured report: log type detection, event timeline, error/warning summary, security threat detection, anomaly flagging, and prioritized recommendations. Writes the full report to disk and prints only the key findings summary.

**Language:** Respond in the user's language. Report content in English.

---

## When to Use

- User pastes log content and wants it analyzed
- User asks "what's wrong with my server?" and provides logs
- User wants to find brute force attacks, failed logins, unusual IPs
- User wants to understand why a service crashed or behaved unexpectedly
- User wants a summary of a long log file
- User asks about errors in nginx, sshd, Docker, cron, kernel logs

## When NOT to Use

- User wants to set up centralized log management → use `linux-monitoring-setup`
- User wants to write a script that parses logs → use `linux-shell-scriptor`
- User wants to audit a config file → use `linux-config-auditor`

---

## Step 1 — Accept Input

Three input methods:

| Method | What user provides |
|--------|-------------------|
| **Paste** | Copies log lines directly into the chat |
| **Path** | Gives a file path like `/var/log/auth.log` — read it with the Read tool |
| **Description** | Describes the log and problem — ask them to paste relevant lines |

If the user describes a problem without pasting logs, ask:
> "Paste the relevant log lines (or the full log file content) and I'll analyze them."

---

## Step 2 — Detect Log Type

Auto-detect from content patterns. Never ask the user what log type it is unless completely ambiguous.

| Log type | Detection pattern |
|----------|------------------|
| `auth.log` / `secure` | `sshd`, `pam_unix`, `sudo`, `useradd`, `passwd`, `Failed password`, `Accepted publickey` |
| `syslog` / `messages` | Generic `kernel:`, `systemd`, `cron`, mixed daemon messages |
| `kern.log` | `kernel:`, `oom-killer`, `EXT4-fs`, `NVMe`, hardware errors |
| `nginx access` | `GET /`, `POST /`, `HTTP/1.1`, status codes, IP + user-agent pattern |
| `nginx error` | `[error]`, `[warn]`, `[crit]`, `upstream`, `connect() failed` |
| `apache access` | Same as nginx access with Apache-style combined log format |
| `apache error` | `[Mon`, `[error]`, `AH0` error codes |
| `journalctl` | `-- Boot`, `systemd[1]`, `kernel:` mixed with unit names |
| `fail2ban` | `fail2ban.actions`, `Ban`, `Unban`, `Found` |
| `docker` | Container name prefix, `level=`, JSON log format |
| `cron` | `CRON[`, `CMD`, `crond` |
| `mysql/postgres` | `ERROR`, `FATAL`, `PANIC`, query patterns, `slow query` |
| `mongodb` | `SEVERE`, `ERROR`, `WARNING`, `WiredTiger`, `getLastError`, `connection refused` |
| `redis` | `WARNING`, `NOTICE`, `#`, `ERR`, `maxmemory-policy`, `RDB/AOF` |
| `ufw/iptables` | `UFW BLOCK`, `IN=`, `OUT=`, `SRC=`, `DST=` |

---

## Step 3 — Analysis Framework

Run all analysis passes. Adjust depth to the log volume.

### Pass 1 — Error & Warning Summary

Count and categorize by severity:

| Severity | Keywords to scan for |
|----------|---------------------|
| 🔴 FATAL / CRITICAL | `FATAL`, `PANIC`, `critical`, `oom-killer`, `kernel panic`, `segfault` |
| 🟠 ERROR | `ERROR`, `error`, `[error]`, `failed`, `Failed`, `refused`, `denied` |
| 🟡 WARNING | `warn`, `WARNING`, `[warn]`, `deprecated`, `timeout`, `retrying` |
| 🔵 INFO | Informational events worth noting (restarts, connections, auth successes) |

### Pass 2 — Security Threat Detection

Always run for auth.log, syslog, nginx, ufw logs:

| Threat | Signal |
|--------|--------|
| **Brute force SSH** | >5 `Failed password` from same IP within short window |
| **Successful breach after failures** | `Accepted` login from IP that previously had failures |
| **Root login attempt** | `Failed password for root` or `Accepted publickey for root` |
| **Invalid user attempts** | `Invalid user` — enumerate usernames being tried |
| **Port scanning** | Many different ports hit from same IP in ufw/iptables logs |
| **Web scanning / probing** | Many 404s, `/wp-admin`, `/.env`, `/phpMyAdmin`, shell upload patterns |
| **Privilege escalation** | Unexpected `sudo` usage, `su` for unusual accounts |
| **Account changes** | `useradd`, `usermod`, `passwd` outside expected windows |
| **Cron injection** | Unexpected new cron entries in syslog |

### Pass 3 — Performance & Stability Issues

| Issue | Signal |
|-------|--------|
| OOM kills | `oom-killer`, `Out of memory` — note which process and how much RAM |
| Service crashes / restarts | `start request repeated too quickly`, `failed` + `restarting` |
| Disk errors | `I/O error`, `EXT4-fs error`, `Buffer I/O error` — flag as urgent |
| High load / slow queries | DB `slow query`, `lock wait timeout`, nginx `upstream timed out` |
| Network issues | `connection refused`, `No route to host`, `name resolution failure` |
| Certificate expiry | `certificate has expired`, `SSL_ERROR`, `certificate verify failed` |

### Pass 4 — Timeline of Notable Events

Extract the 10–20 most significant events in chronological order with timestamps.

### Pass 5 — IP Intelligence

For any log containing IP addresses:
- List top 10 IPs by request/event count
- Flag IPs with >10 failed auth attempts
- Flag IPs that appear in both failures and successes (potential breach)
- Note any internal vs external IP patterns
- Classify IPs: private RFC1918 (`10.x`, `172.16–31.x`, `192.168.x`) vs public
- Flag known bad ranges (Tor exit nodes, common scanner ASNs like Shodan, Censys)

### Pass 6 — Extended Analysis

Run these deeper passes when the log has enough data (>50 lines or contains meaningful time range):

#### Frequency & Spike Detection
- Bucket events by hour or minute and calculate the baseline rate
- Flag any window where the event rate is >3× the average — these are anomalous spikes
- Example output: "Error rate spiked 8× at 03:14 — 47 errors in 60 seconds vs. average of 6/min"

#### Cross-Event Correlation
Link related events that occurred close in time (within 60 seconds):
- SSH failure burst → followed by successful login = **credential stuffing / breach attempt**
- Service restart → followed by error spike = **crash loop with side effects**
- OOM kill → followed by slow query or timeout = **resource starvation cascade**
- UFW block spike → followed by auth failures = **coordinated attack from same actor**

#### Trend Analysis
If the log spans more than 1 hour:
- Is the error rate increasing, decreasing, or stable?
- Are any IPs showing escalating activity (first scans, then auth attempts, then exploits)?
- Any recurring patterns (same error every hour = likely a broken cron job)?

#### Filesystem & Kernel Extended Analysis (ext2/ext3/ext4, hardware)
Specifically for kern.log and syslog:

| Signal | Meaning | Urgency |
|--------|---------|---------|
| `EXT4-fs error` / `EXT3-fs error` | Filesystem corruption | 🔴 Immediate — backup and run `fsck` |
| `Buffer I/O error on dev` | Disk read/write failure | 🔴 Disk may be dying — run `smartctl` |
| `SCSI error` / `ata exception` | Storage controller error | 🔴 Hardware issue |
| `MCE hardware error` | CPU/memory hardware fault | 🔴 Critical hardware failure |
| `EDAC MC` errors | ECC memory errors | 🟠 RAM degrading |
| `soft lockup` / `hard lockup` | Kernel deadlock or hung task | 🟠 System instability |
| `RCU stall` | Real-Time Clock stall — kernel hang | 🟠 Investigate running processes |
| `segfault` at address | Process memory violation | 🟠 App crash or exploit attempt |
| `oom-killer` | Out-of-memory process kill | 🟠 RAM exhaustion |
| `ip_tables: ... table is full` | Firewall rule table overflow | 🟡 Tune conntrack limits |

For each filesystem/hardware finding, include the exact recommended command to diagnose further.

#### HTTP / Web Log Pattern Analysis
For nginx and Apache access logs only:
- **Status code distribution**: count 2xx / 3xx / 4xx / 5xx — high 5xx = app problem, high 4xx = scanners/bad clients
- **Top requested paths**: list top 20 URLs — flag any that look like vulnerability scanning (`/.env`, `/wp-login.php`, `/admin`, `/shell`, `/phpinfo.php`)
- **Response size anomalies**: unusually large responses could indicate data exfiltration
- **User-agent analysis**: flag empty user-agents, known scanner agents (Nikto, sqlmap, masscan, zgrab), and headless browser signatures
- **Request rate per IP per minute**: flag IPs exceeding 60 req/min as potential DDoS or scrapers
- **Slow requests**: flag any `request_time` > 5s if log format includes it

#### Database Log Extended Analysis
For MySQL, PostgreSQL, MongoDB logs:
- List slow queries (>1s) with count and average duration
- Flag `lock wait timeout` — identifies transaction conflicts
- Flag `too many connections` — indicates connection pool exhaustion
- Flag `deadlock found` — requires query/index review
- Replication errors: `Slave_IO_Running: No`, `replication lag`

---

## Step 4 — Write Report

Write the full report to `./log-analysis/report-<log-type>-<YYYYMMDD>.md` in the current working directory.

Report structure:
```markdown
# Log Analysis Report
**Log type:** [detected type]
**Lines analyzed:** [count]
**Time range:** [first timestamp] → [last timestamp]
**Generated:** [date]

## Executive Summary
[2–3 sentences: what was found, severity, most important action]

## Findings by Severity
### 🔴 Critical / Fatal ([count])
[table: timestamp | message | count]

### 🟠 Errors ([count])
[table: timestamp | message | count]

### 🟡 Warnings ([count])
[table: top entries]

## Security Events
[table: threat type | source IP | count | first seen | last seen | verdict]

## Top IPs
[table: IP | requests | failed auths | successful auths | verdict]

## Service / Stability Events
[list of crashes, restarts, OOM events with timestamps]

## Filesystem & Hardware Events
[ext4 errors, disk I/O errors, kernel panics, MCE errors — with recommended diagnostic commands]

## Extended Analysis
### Anomalous Spikes
[hourly event rate table — flag windows >3× average]

### Correlated Event Chains
[linked event sequences with timestamps and interpretation]

### Trend
[is the situation stable, improving, or worsening?]

### HTTP Pattern Analysis (if web log)
[status code distribution, top paths, scanner activity, suspicious user-agents]

### Database Analysis (if DB log)
[slow queries, lock waits, connection issues]

## Notable Events Timeline
[chronological list of the 10–20 most significant events]

## Recommendations
[numbered, prioritized list — most urgent first]
```

---

## Step 5 — Chat Output

Print ONLY this after writing the report:

```
✅ Log analysis complete → ./log-analysis/report-[type]-[date].md

📊 Summary ([N] lines · [time range]):
   🔴 Critical:  [count]
   🟠 Errors:    [count]
   🟡 Warnings:  [count]
   🔵 Notable:   [count]

🔒 Security: [one line — e.g. "47 brute force attempts from 3.4.5.6 — IP should be blocked"]
⚡ Top issue: [one line — e.g. "nginx upstream timeout 23 times between 02:00–03:00"]

▶ Recommended actions:
  1. [most urgent action]
  2. [second action]
  3. [third action]

💡 Next: /linux-security-hardener to block attack vectors · /linux-config-auditor to fix misconfigs
```

If no issues were found:
```
✅ Log analysis complete → ./log-analysis/report-[type]-[date].md
   No critical issues found. [N] lines analyzed, [time range].
```

---

## Quick Reference — Common Findings & Actions

| Finding | Recommended action |
|---------|-------------------|
| SSH brute force from IP X | `ufw deny from X` or add to fail2ban blocklist |
| Root login accepted | Disable `PermitRootLogin` in sshd_config immediately |
| OOM kill of process X | Check RAM usage, add swap, tune process memory limits |
| Disk I/O errors | Run `smartctl -a /dev/sdX` — disk may be failing |
| Nginx upstream timeout | Check app server health, tune `proxy_read_timeout` |
| SSL certificate expired | Run `certbot renew` or check renewal cron job |
| Service restart loop | Check service logs: `journalctl -u service-name -n 100` |
| DB slow queries | Enable slow query log, add indexes, check connection pool |
