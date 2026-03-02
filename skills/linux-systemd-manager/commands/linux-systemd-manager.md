# /linux-systemd-manager

Create, debug, or harden any systemd unit — service, timer, socket, path, or mount.

## Usage

```
/linux-systemd-manager <describe what you need>
```

## Examples

```
/linux-systemd-manager run /opt/myapp/start.sh as a service as user appuser
/linux-systemd-manager timer to run /opt/backup.sh every day at 2 AM
/linux-systemd-manager my nginx.service keeps failing — debug it
/linux-systemd-manager harden my existing postgresql.service unit
/linux-systemd-manager socket activation for my Go app on port 8080
/linux-systemd-manager watch /var/uploads and trigger a processing script
```

## Behavior

If the command, user, and schedule (if applicable) are provided, generate immediately.
If critical info is missing, ask one question.

For new units: writes complete, security-hardened unit file(s) to `./systemd/`.
For debugging: outputs the exact diagnostic command sequence to run.
For hardening: audits the existing unit and outputs an improved version.

Prints only: install commands, enable/start commands, verify commands, and the systemd-analyze security score command.

$ARGUMENTS
