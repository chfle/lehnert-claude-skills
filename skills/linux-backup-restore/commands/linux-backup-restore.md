# /linux-backup-restore

Design and generate a complete backup solution — scripts, encryption, remote storage, retention, and restore procedures.

## Usage

```
/linux-backup-restore <what to back up>
```

## Examples

```
/linux-backup-restore /etc, /home, /var/www to local NAS and Backblaze B2
/linux-backup-restore PostgreSQL database — daily full, hourly incremental
/linux-backup-restore all Docker named volumes to S3
/linux-backup-restore full server backup with restic, encrypted, to SFTP remote
/linux-backup-restore MongoDB + Redis + /data/uploads with 30-day retention
/linux-backup-restore show me how to restore from my BorgBackup repository
/linux-backup-restore verify and test my restic backups are actually working
/linux-backup-restore disaster recovery guide for my server
```

## Generates

```
backup/
  backup.sh           ← main backup script with logging and error handling
  test-restore.sh     ← mandatory restore test script
  .env.example        ← repo URL, passwords, remote credentials
  README.md           ← setup, usage, restore procedure, retention policy
```

## Behavior

If the backup target is clear, start generating.
If the tool is not specified, present 4 options and let the user pick:
1. **Restic** — deduplicated, encrypted, supports S3/B2/SFTP/local
2. **BorgBackup** — deduplicated, compressed, SSH-native, fast
3. **rsync** — simple, fast, no deduplication, great for local/NFS
4. **tar + gpg** — portable, no dependencies, manual but reliable

For **restore** or **disaster recovery** requests: output the step-by-step restore procedure without generating new scripts.

After tool selection:
1. Write `backup.sh`, `test-restore.sh`, `.env.example`, `README.md` to `./backup/`
2. Enforce the **3-2-1 rule**: 3 copies, 2 different media, 1 offsite
3. Print: first-run setup commands, run command, cron line to automate, test restore command
4. Suggest: automate with `/linux-cron-manager` or `/linux-systemd-manager`

$ARGUMENTS
