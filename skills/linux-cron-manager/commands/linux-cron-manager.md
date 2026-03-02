# /linux-cron-manager

Schedule any Linux task with cron — generates a `/etc/cron.d/` entry and a production-ready wrapper script with locking, logging, and failure alerting.

## Usage

```
/linux-cron-manager <schedule and task description>
```

## Examples

```
/linux-cron-manager run /opt/backup.sh every day at 2 AM as root
/linux-cron-manager run /opt/myapp/cleanup.py every 15 minutes as appuser
/linux-cron-manager send a disk usage report by email every Monday at 8 AM
/linux-cron-manager my cron job isn't running — debug it
/linux-cron-manager convert to systemd timer: 0 3 * * * appuser /opt/sync.sh
/linux-cron-manager what cron syntax means "every 10 minutes, Mon–Fri, 9–18h"?
/linux-cron-manager run database backup every 6 hours, skip if previous run is still active
```

## Generates

```
cron/
  <name>.cron        ← /etc/cron.d/ entry with SHELL, PATH, MAILTO
  <name>-wrapper.sh  ← wrapper: flock locking, timestamped logging, failure trap
```

## Behavior

If the schedule, command, and user are clear, generate immediately.
If any of those are missing, ask one question.

**Schedule mode** — new cron job:
1. Generate the `/etc/cron.d/` file (with `SHELL`, `PATH`, `MAILTO` headers)
2. Generate the wrapper script: `flock` locking to prevent overlapping runs, timestamped `log_info`/`log_error` helpers, `logrotate`-compatible log path, `trap` on failure
3. Write both files to `./cron/`
4. Print: install commands (`cp` + `chmod`), verify command (`grep -l` in cron.d), manual test command

**Debug mode** — `not running` / `isn't working` / `debug` in the description:
Output the step-by-step cron debugging checklist: check cron daemon, `/var/log/cron`, PATH issues, environment differences, file permissions.

**Convert mode** — `convert to systemd timer` in the description:
Output the equivalent `.service` + `.timer` pair — or suggest `/linux-systemd-manager` for a full timer unit with sandboxing.

Suggests: `/linux-systemd-manager` when the job needs resource limits, restart policies, or dependency ordering.

$ARGUMENTS
