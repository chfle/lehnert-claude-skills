# /linux-systemd-manager

Create, debug, or harden any systemd unit — service, timer, socket, path, or mount.

## Usage

```
/linux-systemd-manager <describe what you need>
```

## Examples

```
/linux-systemd-manager run /opt/myapp/start.sh on boot as user appuser, restart on failure
/linux-systemd-manager timer: run /opt/backup.sh every day at 2:30 AM, skip if already running
/linux-systemd-manager my nginx.service keeps crashing after 5 minutes — debug it
/linux-systemd-manager harden my existing /etc/systemd/system/app.service — here: [paste unit]
/linux-systemd-manager socket activation for my Go app on port 8080
/linux-systemd-manager path unit: watch /var/uploads and trigger /opt/process.sh on new files
/linux-systemd-manager run a Python script every 15 minutes with resource limits (CPU + RAM)
/linux-systemd-manager show me systemd-analyze and security score for my service
```

## Generates

```
systemd/
  <name>.service    ← service unit (hardened with sandboxing)
  <name>.timer      ← timer unit (if scheduling was requested)
  install.sh        ← copy, reload, enable, start commands in order
```

## Behavior

If the command, user, and schedule (if applicable) are provided, generate immediately.
If critical info is missing (e.g. which user to run as), ask one question.

**Create mode** — new unit:
1. Generate complete, security-hardened unit file(s) — `PrivateTmp`, `NoNewPrivileges`, `ProtectSystem`, resource limits
2. Write unit file(s) and `install.sh` to `./systemd/`
3. Print: install commands, enable/start commands, verify with `systemctl status` and `journalctl -u <name>`
4. Print `systemd-analyze security <name>.service` command to see the hardening score

**Debug mode** — `failing` / `crashing` / `not starting` in the description:
1. Output the exact diagnostic sequence: `systemctl status`, `journalctl -u`, `systemd-analyze verify`
2. List the most common failure causes and how to identify them

**Harden mode** — paste an existing unit:
1. Audit it for missing security directives
2. Output an improved version with each change explained

Suggests: `/linux-cron-manager` if the user only needs a simple cron schedule without service features.

$ARGUMENTS
