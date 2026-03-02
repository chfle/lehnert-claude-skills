---
name: linux-shell-scriptor
description: Use when user wants to write, generate, or create a bash or shell script for any Linux task — backups, monitoring, deployment, automation, cron jobs, health checks, system maintenance, log rotation, or any described sysadmin use case.
version: 1.2.0
author: Lehnert
---

# Linux Shell Scriptor

## Overview

Generates production-ready bash scripts from plain English descriptions. Every script follows Linux best practices: strict mode, structured error handling, timestamped logging, secure defaults, dry-run support, and a built-in usage function.

**Language:** Respond in the user's language. Script comments and variable names default to English.

---

## When to Use

- User describes any task they want automated as a shell script
- User says "write a bash script", "create a shell script", "script to…"
- Backup, monitoring, deployment, cleanup, health check, cron job, report, alerting
- User wants to automate any repetitive sysadmin or DevOps task

## When NOT to Use

- User wants a Python / Perl / Ruby script → general coding
- User wants to audit an existing config → /linux-config-auditor
- User wants to configure a service → /linux-config-auditor
- User wants to set up cron scheduling only → /linux-cron-manager

---

## Clarification Protocol

If the user's description is missing critical details, ask **one question at a time**. Never ask more than two clarifying questions total before generating. Start generating and list assumptions if the use case is reasonably clear.

| Missing info | Question to ask |
|-------------|----------------|
| Target distro | "Which Linux distro does this run on (Ubuntu, RHEL, Alpine…)?" |
| Run as which user | "Should the script run as root or a regular user?" |
| Triggered how | "Is this run manually, by cron, or triggered by another process?" |
| On failure behavior | "On failure, should it exit silently, log, or send a notification?" |
| Destructive actions | "Should there be a `--dry-run` mode to preview changes before applying?" |

---

## Script Quality Standards

Every script MUST contain all of the following:

| Element | Implementation |
|---------|----------------|
| Shebang + strict mode | `#!/usr/bin/env bash` and `set -euo pipefail` |
| Script header | Name, version, purpose, date in a comment block |
| `usage()` function | Flags, arguments, examples, exit codes |
| Logging helpers | `log()`, `warn()`, `error()` functions writing to stderr with timestamps |
| Argument parsing | `getopts` or manual flag loop with validation and `usage` on bad args |
| Dependency check | `command -v <tool> >/dev/null` for every external command used |
| Trap + cleanup | `trap cleanup EXIT` to remove temp files and restore state |
| Idempotent logic | Safe to run multiple times wherever possible |
| Dry-run mode | `--dry-run` / `-n` flag for any script that modifies files or services |
| No hardcoded secrets | Credentials via env vars or config files only; never inline |
| Quoted variables | All variable expansions in double quotes: `"$var"` |

---

## Templates by Use Case

### Backup Script
- Defines SOURCE_DIR, DEST_DIR, RETENTION_DAYS
- Uses `rsync -az --delete` or `tar -czf` based on size
- Verifies backup integrity (checksum or restore test)
- Prunes old backups with `find … -mtime +N -delete`
- Lock file to prevent concurrent runs (`flock`)
- Logs start/end/size, emails or writes to syslog on failure

### Monitoring / Health Check
- Checks disk usage (`df`), memory (`free`), CPU load (`uptime`), and key services (`systemctl is-active`)
- User-configurable thresholds (DISK_WARN=80, DISK_CRIT=90)
- Outputs OK / WARN / CRIT per check
- Optional Nagios-compatible exit codes (0/1/2)
- Can write to a status file for polling

### Deployment Script
- Pull latest code (`git pull` or artifact fetch)
- Run build step if needed
- Run DB migrations
- Swap symlink or restart service
- On failure: rollback and log exactly what failed
- Appends to a deployment log with timestamp and git SHA

### System Maintenance
- Package updates (`apt`/`yum`/`dnf`) with pre-check for reboot requirement
- Old file cleanup (`find /tmp -mtime +7 -delete`)
- Journal/log rotation trigger
- Creates a maintenance report in `/var/log/maintenance.log`

### Log Management / Report
- Parses a log file with `grep`/`awk`/`sed` patterns provided by user
- Counts occurrences, extracts fields, detects anomalies
- Outputs a summary report to stdout or a file
- Optionally archives processed logs

### Database Maintenance
- Connects using env vars (never hardcoded credentials)
- PostgreSQL: `pg_dump`, `VACUUM`, `ANALYZE`, `pg_stat_activity` monitoring
- MySQL: `mysqldump`, optimize tables, check binary log size
- Redis: `redis-cli INFO`, `BGSAVE`, key count / memory stats
- Sends result summary to log file; alerts on non-zero exit

---

## Security Rules

These are non-negotiable and must appear in every generated script:

- **No `eval` with user input** — ever
- **Quote every variable expansion:** `"$var"`, `"${array[@]}"`
- **Temp files via `mktemp`** — never predictable paths like `/tmp/script.tmp`
- **Validate file path inputs** — check for `..` traversal before using
- **Restrictive umask for sensitive output:** `umask 077` before writing credentials or keys
- **Avoid unnecessary `sudo`** — document when privilege escalation is needed and why
- **`set -euo pipefail`** — always; prevents silent failures from masked errors

---

## Output Format

1. Write the script to `./scripts/<task-name>.sh`
2. **Also print** the script in a fenced `bash` code block so the user can review it before running
3. Below the script, list any **assumptions made** (distro, user, paths, thresholds)
4. Show:

```bash
# Syntax check before first run
bash -n scripts/your-script.sh

# Make executable
chmod +x scripts/your-script.sh

# Test with dry-run (if script supports it)
./scripts/your-script.sh --dry-run
```

5. Suggest next steps based on script type

---

## Next Steps (always include after output)

> **Syntax check:** `bash -n scripts/your-script.sh`
> **ShellCheck:** `shellcheck scripts/your-script.sh` (install: `apt install shellcheck`)
> **If recurring:** Add to cron with `/linux-cron-manager` or a systemd timer with `/linux-systemd-manager`
> **If security-sensitive:** Review the host with `/linux-security-hardener`
> **If it reads config files:** Audit those configs with `/linux-config-auditor`
