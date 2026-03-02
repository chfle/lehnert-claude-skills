# /linux-shell-scriptor

Generate a production-ready bash script for any Linux task.

## Usage

```
/linux-shell-scriptor <task description>
```

## Examples

```
/linux-shell-scriptor backup /var/www to /mnt/backups, keep 7 days, email on failure
/linux-shell-scriptor monitor disk usage on all partitions and warn above 85%
/linux-shell-scriptor deploy a Node.js app from git with rollback on failure
/linux-shell-scriptor clean up log files older than 30 days in /var/log
```

## Behavior

If an argument is provided, use it directly as the task description.
If no argument is given, ask: "What should the script do? Describe the task in plain language."

Then run the full linux-shell-scriptor workflow:
1. Identify the script category and clarify any critical missing detail (at most one question)
2. Generate the complete bash script: strict mode, logging helpers, usage function, dependency checks, trap/cleanup, dry-run flag, no hardcoded secrets
3. Print the script in a fenced `bash` code block — copy-paste ready
4. List assumptions made (distro, user, paths, thresholds)
5. Show save + chmod + syntax-check commands
6. Suggest next steps (cron, systemd timer, security review)

$ARGUMENTS
