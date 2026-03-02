# /linux-shell-scriptor

Generate a production-ready bash script for any Linux task from a plain description.

## Usage

```
/linux-shell-scriptor <task description>
```

## Examples

```
/linux-shell-scriptor backup /var/www and /etc to /mnt/backups, keep 7 days, email on failure
/linux-shell-scriptor monitor disk usage on all partitions, warn at 85%, alert at 95% via email
/linux-shell-scriptor deploy Node.js app from git — pull, npm ci, pm2 restart, rollback on failure
/linux-shell-scriptor delete log files older than 30 days in /var/log, dry-run mode by default
/linux-shell-scriptor health check for nginx, PostgreSQL and Redis — exit 1 if any service is down
/linux-shell-scriptor rotate SSL certs with certbot, reload nginx, and notify on failure
/linux-shell-scriptor sync /data/uploads to S3 bucket every hour, skip files under 1KB
/linux-shell-scriptor create new Linux user with sudo, home dir, and SSH key from argument
```

## Generates

```
scripts/
  <task-name>.sh    ← production-ready, chmod +x ready
```

## Behavior

If an argument is provided, use it directly as the task description.
If no argument is given, ask: "What should the script do? Describe the task in plain English."

1. Identify the script category (backup, deploy, monitor, cleanup, user-mgmt, etc.)
2. Clarify at most one critical missing detail before generating
3. Generate the complete bash script with:
   - `set -euo pipefail` and `IFS=$'\n\t'`
   - Logging helpers (`log_info`, `log_warn`, `log_error` with timestamps)
   - `--help` / `--dry-run` flags
   - Dependency checks (`command -v` for all tools used)
   - `trap` + cleanup handler on EXIT/ERR
   - No hardcoded secrets — read from env vars or arguments
4. Write the script to `./scripts/<task-name>.sh`
5. Print: file path, `chmod +x` command, `shellcheck` syntax check command, list of assumptions made
6. Suggest next step: cron line (`/linux-cron-manager`) or systemd timer (`/linux-systemd-manager`) to automate it

$ARGUMENTS
