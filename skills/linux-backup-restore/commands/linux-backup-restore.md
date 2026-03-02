# /linux-backup-restore

Design and generate a complete backup solution — scripts, configs, retention, encryption, remote storage, and restore procedures.

## Usage

```
/linux-backup-restore <what to back up>
```

## Examples

```
/linux-backup-restore my Ubuntu VPS — /etc, /home, /var/www
/linux-backup-restore PostgreSQL database with daily dumps
/linux-backup-restore Docker volumes to Backblaze B2
/linux-backup-restore full server backup with restic to S3
/linux-backup-restore restore guide for my borg backup
/linux-backup-restore test if my backups are working
```

## Behavior

If the backup target is clear, generate immediately.
If the tool is unspecified, present 4 options (Restic, BorgBackup, rsync, tar+gpg) — user picks one.

Generates: backup script, test-restore script, .env.example, README.
Writes all files silently to `./backup/`.
Prints only: first-time setup commands, run command, cron line to automate, test command.
Always enforces the 3-2-1 backup rule and includes a mandatory test-restore script.

$ARGUMENTS
