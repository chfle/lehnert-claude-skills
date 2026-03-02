Generate a production-ready bash script using the linux-shell-scriptor skill.

If the user provided a description as an argument, use it directly as the task description.
If no argument was given, ask: "What should the script do? Describe the task in plain language (e.g. 'backup /var/www to /mnt/backups, keep 7 days, email on failure')."

Then run the full linux-shell-scriptor workflow:
1. Identify the script category (backup, monitoring, deployment, maintenance, health check, log management, or other)
2. Check if any critical information is missing — if so, ask ONE clarifying question before generating
3. Generate the complete bash script following all quality standards (strict mode, logging, dry-run, dependency checks, trap/cleanup, no hardcoded secrets)
4. Print the script in a fenced bash code block — copy-paste ready
5. List any assumptions made below the script
6. Show the save + chmod + syntax-check commands
7. Suggest next steps (cron, systemd timer, security review)

$ARGUMENTS
