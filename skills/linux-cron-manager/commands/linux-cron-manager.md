# /linux-cron-manager

Schedule any Linux task with cron — generates a /etc/cron.d/ entry and a production-ready wrapper script with locking, logging, and failure alerting.

## Usage

```
/linux-cron-manager <schedule and task description>
```

## Examples

```
/linux-cron-manager run /opt/backup.sh every day at 2 AM as root
/linux-cron-manager run /opt/myapp/cleanup.py every 15 minutes as appuser
/linux-cron-manager my cron job isn't running — debug it
/linux-cron-manager convert this cron job to a systemd timer: 0 3 * * * appuser /opt/sync.sh
/linux-cron-manager cron syntax for every weekday at 9 AM
```

## Behavior

If the schedule, command, and user are clear, generate immediately.
If missing, ask one question.

Writes two files to `./cron/`: the `/etc/cron.d/` entry (with SHELL, PATH, MAILTO) and a wrapper script with flock locking, timestamped logging, log rotation, and a failure trap.
For debugging: outputs the step-by-step diagnostic checklist.
For conversion: outputs the equivalent systemd `.service` + `.timer` pair.

Prints only: install commands, verify command, manual test command.

$ARGUMENTS
