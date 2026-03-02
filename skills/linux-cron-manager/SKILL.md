---
name: linux-cron-manager
description: Use when user wants to schedule a task with cron, write a crontab entry, create a /etc/cron.d/ file, set up a cron job with locking and logging, convert a cron job to a systemd timer, debug a cron job that isn't running, or understand cron syntax and environment behavior.
version: 1.3.0
author: Lehnert
---

# Linux Cron Manager

## Overview

Generates production-ready cron entries and /etc/cron.d/ files with proper environment setup, flock-based locking to prevent overlapping runs, structured logging, and failure alerting. Includes a full cron syntax reference and a conversion path to systemd timers for complex scheduling needs.

**Language:** Respond in the user's language. Cron files and scripts in English.

---

## When to Use

- User wants to schedule any recurring task
- User wants a cron job that logs and alerts on failure
- User wants to prevent a cron job from running twice at once
- User wants to convert a cron job to a systemd timer
- User asks "my cron job isn't running" — debugging
- User wants to understand cron syntax or cron environment behavior

## When NOT to Use

- User needs complex dependencies between jobs, socket activation, or resource limits → `linux-systemd-manager`
- User wants to write the script that cron will run → `linux-shell-scriptor`

---

## Step 1 — Understand the Request

| User says | Output |
|-----------|--------|
| "run script X every Y" | `/etc/cron.d/` entry + wrapper with locking + logging |
| "crontab entry for..." | Direct crontab line with explanation |
| "my cron job isn't running" | Debugging checklist |
| "convert cron to systemd timer" | Equivalent `.service` + `.timer` pair |
| "cron syntax for..." | Explanation + validated expression |

---

## Cron Syntax Reference

```
┌──────────── minute       (0–59)
│ ┌────────── hour         (0–23)
│ │ ┌──────── day of month (1–31)
│ │ │ ┌────── month        (1–12 or JAN–DEC)
│ │ │ │ ┌──── day of week  (0–7, 0 and 7 = Sunday, or SUN–SAT)
│ │ │ │ │
* * * * *  user  command
```

**Common expressions:**

| Schedule | Expression |
|----------|-----------|
| Every minute | `* * * * *` |
| Every 5 minutes | `*/5 * * * *` |
| Every hour at :00 | `0 * * * *` |
| Every hour at :30 | `30 * * * *` |
| Daily at 2 AM | `0 2 * * *` |
| Daily at 2:30 AM | `30 2 * * *` |
| Every Monday at 3 AM | `0 3 * * 1` |
| Weekdays at 9 AM | `0 9 * * 1-5` |
| 1st of every month | `0 0 1 * *` |
| Every 15 minutes | `*/15 * * * *` |
| Twice daily (6 AM + 6 PM) | `0 6,18 * * *` |
| Every Sunday at midnight | `0 0 * * 0` |

Validate any expression at: `https://crontab.guru`

---

## Production Cron Entry Template

**Never write a bare cron entry.** Always wrap with locking, logging, and error handling.

### /etc/cron.d/ file format (preferred — survives user crontab edits)

`/etc/cron.d/myapp-task`:
```
# myapp-task — describe what this does
# Runs: daily at 2 AM
# Logs: /var/log/cron/myapp-task.log
# Owner: appuser

SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=""
HOME=/home/appuser   # cron doesn't source profile — set HOME explicitly to avoid "~" surprises

0 2 * * *  appuser  /usr/bin/flock -n /var/lock/myapp-task.lock /opt/myapp/tasks/run-task.sh
```

### Wrapper script (`/opt/myapp/tasks/run-task.sh`):

Always generate the wrapper script alongside the cron entry:

```bash
#!/usr/bin/env bash
set -euo pipefail

# ── Config ──────────────────────────────────────────────────
SCRIPT_NAME="myapp-task"
LOG_DIR="/var/log/cron"
LOG_FILE="${LOG_DIR}/${SCRIPT_NAME}.log"
LOCK_FILE="/var/lock/${SCRIPT_NAME}.lock"
MAX_LOG_SIZE_MB=50

# ── Logging ──────────────────────────────────────────────────
mkdir -p "$LOG_DIR"
log()   { echo "[$(date '+%Y-%m-%d %H:%M:%S')] [INFO]  $*" | tee -a "$LOG_FILE"; }
warn()  { echo "[$(date '+%Y-%m-%d %H:%M:%S')] [WARN]  $*" | tee -a "$LOG_FILE" >&2; }
error() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] [ERROR] $*" | tee -a "$LOG_FILE" >&2; }

# ── Log rotation ─────────────────────────────────────────────
if [[ -f "$LOG_FILE" ]]; then
  LOG_SIZE_MB=$(du -m "$LOG_FILE" | cut -f1)
  if [[ "$LOG_SIZE_MB" -ge "$MAX_LOG_SIZE_MB" ]]; then
    ARCHIVED_LOG="${LOG_FILE}.$(date +%Y%m%d_%H%M%S)"
    mv "$LOG_FILE" "$ARCHIVED_LOG"
    gzip "$ARCHIVED_LOG"
  fi
fi

# ── Lock — prevent concurrent runs ───────────────────────────
exec 9>"$LOCK_FILE"
if ! flock -n 9; then
  warn "Another instance is already running. Exiting."
  exit 0
fi

# ── Trap — cleanup on exit ───────────────────────────────────
cleanup() {
  local exit_code=$?
  if [[ $exit_code -ne 0 ]]; then
    error "Task failed with exit code $exit_code"
    # Optional: send alert
    # echo "Cron job $SCRIPT_NAME failed" | mail -s "CRON FAILURE: $SCRIPT_NAME" admin@example.com
  fi
  flock -u 9
}
trap cleanup EXIT

# ── Main ─────────────────────────────────────────────────────
log "=== Starting $SCRIPT_NAME ==="

# --- YOUR TASK CODE HERE ---
# Example:
# log "Running backup..."
# /opt/backup/backup.sh
# log "Backup complete"
# ---------------------------

log "=== $SCRIPT_NAME complete ==="
```

---

## Email / Alert on Failure

Add to the cron file to capture output and email on non-zero exit:

```bash
# Option 1: Send all output to email (requires working MTA)
MAILTO="admin@example.com"
0 2 * * *  appuser  /opt/myapp/tasks/run-task.sh

# Option 2: Only email on failure using a wrapper
0 2 * * *  appuser  /opt/myapp/tasks/run-task.sh || echo "myapp-task failed" | mail -s "CRON FAILURE" admin@example.com

# Option 3: Log to file and send to a webhook (Slack/Discord) on failure
0 2 * * *  appuser  /opt/myapp/tasks/run-task.sh 2>&1 | tee -a /var/log/cron/myapp.log | \
  grep -i "error\|fail" | while read line; do \
  curl -s -X POST -H 'Content-type: application/json' \
  --data "{\"text\":\"Cron failure: $line\"}" "${SLACK_WEBHOOK_URL}"; done
```

---

## Environment Rules

Cron runs in a **minimal environment** — this is the #1 cause of cron jobs that "work manually but not in cron."

Always include in `/etc/cron.d/` files:
```
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
```

Common environment pitfalls:

| Problem | Cause | Fix |
|---------|-------|-----|
| Command not found | PATH is minimal in cron | Use full path: `/usr/bin/python3` |
| Script works manually, not in cron | Missing env vars | Add `EnvironmentFile` or `source /etc/profile` at top of script |
| Python/Node virtualenv not active | No shell init | Source virtualenv explicitly: `source /opt/venv/bin/activate` |
| HOME not set | Cron doesn't source profile | Add `HOME=/home/appuser` to cron file |
| No display/dbus | X11/dbus not available | Not suitable for GUI apps; use headless mode |

---

## Debugging Checklist

When a cron job isn't running or is failing silently:

```bash
# 1. Verify cron daemon is running
systemctl status cron    # Debian/Ubuntu
systemctl status crond   # RHEL/Rocky

# 2. Check cron logs
grep CRON /var/log/syslog | tail -30          # Debian/Ubuntu
journalctl -u cron --since "1 hour ago"       # systemd systems
grep cron /var/log/messages | tail -30        # RHEL/Rocky

# 3. Check the cron entry is valid
crontab -l -u appuser                         # list user crontab
ls -la /etc/cron.d/                           # list system cron jobs

# 4. Test the command manually as the cron user
sudo -u appuser /path/to/script.sh

# 5. Test with same minimal environment as cron
sudo -u appuser env -i HOME=/home/appuser \
  PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin \
  /path/to/script.sh

# 6. Verify file permissions
ls -la /path/to/script.sh        # must be executable
stat /etc/cron.d/myfile          # must be owned by root, mode 644

# 7. Check for syntax errors in /etc/cron.d/ files
# File must NOT have a .sh extension — cron ignores them
# File must NOT be world-writable
```

**The #1 reason cron jobs "silently do nothing":** output (stdout + stderr) is discarded unless captured. Always write logs inside the wrapper script using the `log()` function above, or add `>> /var/log/cron/myapp.log 2>&1` to the cron entry.

**Common reasons cron jobs silently do nothing:**

| Issue | Check |
|-------|-------|
| Script not executable | `chmod +x script.sh` |
| `/etc/cron.d/` file has `.sh` extension | Rename to no extension |
| `/etc/cron.d/` file is world-writable | `chmod 644` |
| Wrong user in cron entry | Verify user exists: `id appuser` |
| Script path wrong | Use absolute paths only in cron |
| Cron daemon not reloaded | `systemctl reload cron` after adding new file |
| Lock file stuck from previous crash | Delete `/var/lock/scriptname.lock` |

---

## Cron → Systemd Timer Conversion

When to switch to systemd timers instead of cron:

| Use cron when | Use systemd timer when | Use Anacron when |
|---------------|----------------------|-----------------|
| Simple schedule, no dependencies | Need `After=` dependencies | System isn't always on (laptop, desktop) |
| Script already works standalone | Need resource limits (MemoryMax, CPUQuota) | Daily/weekly tasks that must run even after downtime |
| Quick setup needed | Need detailed logging via journalctl | No systemd available and `Persistent=true` is needed |
| No systemd available (Alpine, containers) | Need `Persistent=true` for missed runs | — |

**Anacron** (`/etc/anacrontab`) — runs jobs that were missed while the system was off:
```
# period  delay  job-id         command
1         5      daily-backup   /opt/backup/backup.sh
7         10     weekly-report  /opt/reports/weekly.sh
```
Install: `apt install anacron` / `dnf install cronie-anacron`

**Conversion example:**

Cron entry:
```
0 2 * * *  appuser  /opt/myapp/backup.sh
```

Equivalent systemd timer — see `/linux-systemd-manager` for full output.

---

## Output Format

Write files to `./cron/` in the current working directory:

```
cron/
  myapp-task          ← drop into /etc/cron.d/ (no extension!)
  run-task.sh         ← wrapper script with locking + logging
```

Then print ONLY:

```
✅ Cron job written to ./cron/

▶ Install:
  sudo cp cron/myapp-task /etc/cron.d/myapp-task
  sudo chmod 644 /etc/cron.d/myapp-task
  sudo chown root:root /etc/cron.d/myapp-task
  sudo cp cron/run-task.sh /opt/myapp/tasks/run-task.sh
  sudo chmod 755 /opt/myapp/tasks/run-task.sh
  sudo chown appuser:appuser /opt/myapp/tasks/run-task.sh
  sudo systemctl reload cron          # tell cron to pick up the new /etc/cron.d/ file

▶ Verify cron picked it up:
  grep myapp-task /var/log/syslog
  # or: journalctl -u cron | grep myapp-task

▶ Test manually as cron user:
  sudo -u appuser /opt/myapp/tasks/run-task.sh

💡 For complex scheduling with dependencies or resource limits → /linux-systemd-manager
```
