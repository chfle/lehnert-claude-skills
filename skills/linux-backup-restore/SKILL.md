---
name: linux-backup-restore
description: Use when user wants to back up a Linux server, design a backup strategy, write a backup script, set up automated backups, configure restic or borgbackup, restore from a backup, test backup integrity, back up databases, Docker volumes, or asks about the 3-2-1 backup rule or disaster recovery.
version: 1.0.0
author: Lehnert
---

# Linux Backup & Restore

## Overview

Designs and generates a complete backup solution tailored to what the user needs to protect. Presents tool options, handles the full lifecycle: backup creation, encryption, remote transfer, retention, integrity testing, and restore procedures. Writes all scripts and configs to disk.

**Language:** Respond in the user's language. All scripts and configs in English.

---

## When to Use

- User wants to back up a server, directory, database, or Docker volumes
- User asks "how do I back up my server?"
- User wants automated backups on a schedule
- User needs to restore from a backup
- User wants to verify backup integrity
- User wants off-site or cloud backups (S3, Backblaze B2, SSH remote)
- User asks about backup strategies, retention policies, or disaster recovery

## When NOT to Use

- User wants to sync files in real time → use rsync in script via `linux-shell-scriptor`
- User wants to monitor backup jobs → use `linux-monitoring-setup`
- User wants to automate only the scheduling → use `linux-cron-manager`

---

## Step 1 — Understand the Backup Need

Map the user's request to a mode. If unclear, ask ONE question.

| User says | Mode |
|-----------|------|
| "back up my server", "back up my VPS" | Full server backup strategy |
| "back up my database", "PostgreSQL backup", "MySQL backup" | Database backup |
| "back up Docker volumes" | Docker volume backup |
| "back up my files / directory" | Directory backup |
| "set up automated backups" | Scheduled backup with cron/systemd |
| "restore from backup", "I lost my data" | Restore procedure |
| "test my backups", "verify integrity" | Backup testing |
| "off-site backup", "remote backup", "S3", "cloud" | Remote / cloud backup |

If the scope is vague, ask:
> "What do you want to back up? (e.g. specific directories, databases, Docker volumes, or the whole server)"

---

## Step 2 — Present Tool Options

When the user hasn't specified a tool, present options based on the use case:

### For general file/directory backups:

> Here are the best backup tools for your use case. Which fits your needs?
>
> **1. Restic** *(Recommended)*
> Modern, fast, encrypted-by-default backup with deduplication. Supports local, SSH, S3, Backblaze B2, and many other backends. Easy restore and snapshot management.
> Best for: most use cases, especially with remote/cloud storage.
>
> **2. BorgBackup**
> Excellent deduplication and compression. Encrypted. Slightly faster than restic for large datasets on the same machine. Requires borg on both ends for remote.
> Best for: large datasets, same-machine or SSH remote backup.
>
> **3. rsync**
> Simple, fast, widely available. No deduplication or encryption built-in. Best for simple directory mirroring.
> Best for: simple local or SSH mirror, no versioning needed.
>
> **4. tar + gpg**
> Basic but universal. Compressed archive with optional GPG encryption. No incremental, no deduplication.
> Best for: one-off archives, maximum compatibility.

### For database backups:

Present: `pg_dump` (PostgreSQL), `mysqldump` / `xtrabackup` (MySQL/MariaDB), `mongodump` (MongoDB), plus restic wrapping the dump file.

### For Docker volumes:

Present: `docker run --volumes-from` + tar, or restic with Docker volume path, or pre-stop dump approach.

---

## Backup Tool Specifications

### Restic

**Initialize repository:**
```bash
# Local
restic init --repo /mnt/backups/myserver

# S3 (AWS or compatible like Backblaze B2, MinIO)
export AWS_ACCESS_KEY_ID=your_key
export AWS_SECRET_ACCESS_KEY=your_secret
restic init --repo s3:https://s3.amazonaws.com/bucket-name/myserver

# SFTP / SSH
restic init --repo sftp:user@backuphost:/backups/myserver

# Backblaze B2
export B2_ACCOUNT_ID=your_id
export B2_ACCOUNT_KEY=your_key
restic init --repo b2:bucket-name:myserver
```

**Backup script (`backup-restic.sh`):**
```bash
#!/usr/bin/env bash
set -euo pipefail

# ── Config ──────────────────────────────────────────────────
BACKUP_REPO="${BACKUP_REPO:-/mnt/backups/myserver}"
BACKUP_PASSWORD="${RESTIC_PASSWORD:?RESTIC_PASSWORD not set}"
SOURCES=("/etc" "/home" "/var/www" "/opt")
EXCLUDES=("*.log" "*.tmp" "/proc" "/sys" "/dev" "/run" "/tmp")
RETENTION_DAILY=7
RETENTION_WEEKLY=4
RETENTION_MONTHLY=6
LOG_FILE="/var/log/restic-backup.log"

export RESTIC_REPOSITORY="$BACKUP_REPO"
export RESTIC_PASSWORD="$BACKUP_PASSWORD"

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"; }

# ── Build exclude args ───────────────────────────────────────
EXCLUDE_ARGS=()
for excl in "${EXCLUDES[@]}"; do
  EXCLUDE_ARGS+=(--exclude "$excl")
done

# ── Run backup ───────────────────────────────────────────────
log "Starting backup to $BACKUP_REPO"
restic backup "${SOURCES[@]}" "${EXCLUDE_ARGS[@]}" \
  --tag "$(hostname)" \
  --tag "auto" \
  2>&1 | tee -a "$LOG_FILE"

# ── Apply retention policy ───────────────────────────────────
log "Applying retention policy"
restic forget \
  --keep-daily   "$RETENTION_DAILY" \
  --keep-weekly  "$RETENTION_WEEKLY" \
  --keep-monthly "$RETENTION_MONTHLY" \
  --prune \
  2>&1 | tee -a "$LOG_FILE"

# ── Verify last snapshot ─────────────────────────────────────
log "Verifying latest snapshot"
restic check 2>&1 | tee -a "$LOG_FILE"

log "Backup complete"
```

**Restore commands:**
```bash
# List snapshots
restic snapshots

# Restore latest snapshot to original paths
restic restore latest --target /

# Restore specific snapshot to a temp directory
restic restore abc1234 --target /tmp/restore

# Restore only specific paths
restic restore latest --target /tmp/restore --path /etc/nginx

# Mount snapshot as filesystem (browse before restoring)
restic mount /mnt/restic-mount
ls /mnt/restic-mount/snapshots/latest/
```

---

### BorgBackup

**Initialize repository:**
```bash
# Local with encryption
borg init --encryption=repokey /mnt/backups/myserver

# Remote via SSH
borg init --encryption=repokey user@backuphost:/backups/myserver
```

**Backup script (`backup-borg.sh`):**
```bash
#!/usr/bin/env bash
set -euo pipefail

BORG_REPO="${BORG_REPO:-/mnt/backups/myserver}"
export BORG_PASSPHRASE="${BORG_PASSPHRASE:?BORG_PASSPHRASE not set}"
SOURCES="/etc /home /var/www /opt"
LOG_FILE="/var/log/borg-backup.log"

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"; }

log "Starting borg backup"
borg create \
  --verbose \
  --filter AME \
  --list \
  --stats \
  --show-rc \
  --compression lz4 \
  --exclude-caches \
  --exclude '/home/*/.cache/*' \
  --exclude '/var/tmp/*' \
  "${BORG_REPO}::{hostname}-{now:%Y-%m-%dT%H:%M:%S}" \
  $SOURCES \
  2>&1 | tee -a "$LOG_FILE"

log "Pruning old backups"
borg prune \
  --list \
  --keep-daily   7 \
  --keep-weekly  4 \
  --keep-monthly 6 \
  "${BORG_REPO}" \
  2>&1 | tee -a "$LOG_FILE"

log "Checking repository integrity"
borg check "${BORG_REPO}" 2>&1 | tee -a "$LOG_FILE"

log "Backup complete"
```

---

### Database Backups

**PostgreSQL:**
```bash
#!/usr/bin/env bash
set -euo pipefail

DB_NAME="${POSTGRES_DB:?}"
DB_USER="${POSTGRES_USER:?}"
BACKUP_DIR="/var/backups/postgresql"
DATE=$(date +%Y%m%d_%H%M%S)
LOG_FILE="/var/log/pg-backup.log"

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"; }

mkdir -p "$BACKUP_DIR"

log "Dumping $DB_NAME"
pg_dump -U "$DB_USER" -Fc "$DB_NAME" \
  | gzip > "${BACKUP_DIR}/${DB_NAME}_${DATE}.dump.gz"

# Verify the dump is readable
pg_restore --list "${BACKUP_DIR}/${DB_NAME}_${DATE}.dump.gz" > /dev/null \
  && log "Dump verified OK" \
  || { log "ERROR: Dump verification failed"; exit 1; }

# Retain last 14 dumps
find "$BACKUP_DIR" -name "${DB_NAME}_*.dump.gz" -mtime +14 -delete

log "PostgreSQL backup complete: ${DB_NAME}_${DATE}.dump.gz"
```

**Restore PostgreSQL:**
```bash
# Drop and recreate the database
dropdb -U "$DB_USER" "$DB_NAME"
createdb -U "$DB_USER" "$DB_NAME"
pg_restore -U "$DB_USER" -d "$DB_NAME" backup.dump.gz
```

**MySQL / MariaDB:**
```bash
mysqldump \
  --single-transaction \
  --routines \
  --triggers \
  --all-databases \
  -u root -p"${MYSQL_ROOT_PASSWORD}" \
  | gzip > "/var/backups/mysql/all_${DATE}.sql.gz"

# Restore
gunzip < backup.sql.gz | mysql -u root -p"${MYSQL_ROOT_PASSWORD}"
```

**MongoDB:**
```bash
#!/usr/bin/env bash
set -euo pipefail

BACKUP_DIR="/var/backups/mongodb"
DATE=$(date +%Y%m%d_%H%M%S)
LOG_FILE="/var/log/mongo-backup.log"

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"; }

mkdir -p "$BACKUP_DIR"

log "Dumping all MongoDB databases"
mongodump \
  --host "${MONGO_HOST:-localhost}" \
  --port "${MONGO_PORT:-27017}" \
  --username "${MONGO_USER:?}" \
  --password "${MONGO_PASSWORD:?}" \
  --authenticationDatabase admin \
  --out "${BACKUP_DIR}/dump_${DATE}"

log "Compressing dump"
tar czf "${BACKUP_DIR}/mongo_${DATE}.tar.gz" -C "${BACKUP_DIR}" "dump_${DATE}"
rm -rf "${BACKUP_DIR}/dump_${DATE}"

# Retain last 14 dumps
find "$BACKUP_DIR" -name "mongo_*.tar.gz" -mtime +14 -delete

log "MongoDB backup complete: mongo_${DATE}.tar.gz"
```

```bash
# Restore MongoDB
tar xzf mongo_YYYYMMDD.tar.gz
mongorestore \
  --host "${MONGO_HOST:-localhost}" \
  --username "${MONGO_USER}" \
  --password "${MONGO_PASSWORD}" \
  --authenticationDatabase admin \
  --drop \
  dump_YYYYMMDD/
```

---

### Docker Volume Backup

```bash
#!/usr/bin/env bash
# Back up all named Docker volumes
set -euo pipefail

BACKUP_DIR="/var/backups/docker-volumes"
DATE=$(date +%Y%m%d_%H%M%S)
LOG_FILE="/var/log/docker-volume-backup.log"

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"; }

mkdir -p "$BACKUP_DIR"

# Get all named volumes
VOLUMES=$(docker volume ls --format '{{.Name}}')

for VOL in $VOLUMES; do
  log "Backing up volume: $VOL"
  docker run --rm \
    -v "${VOL}:/data:ro" \
    -v "${BACKUP_DIR}:/backup" \
    alpine \
    tar czf "/backup/${VOL}_${DATE}.tar.gz" -C /data . \
    && log "✓ $VOL backed up" \
    || log "✗ $VOL FAILED"
done

# Retain last 7 days
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +7 -delete

log "Docker volume backup complete"
```

**Restore a Docker volume:**
```bash
# Stop the container first
docker compose down

# Restore
docker run --rm \
  -v "${VOLUME_NAME}:/data" \
  -v "$(pwd):/backup:ro" \
  alpine \
  sh -c "cd /data && tar xzf /backup/${VOLUME_NAME}_YYYYMMDD.tar.gz"

# Restart
docker compose up -d
```

---

### Backup Testing (Mandatory)

Always include a test-restore script alongside every backup setup:

```bash
#!/usr/bin/env bash
# test-restore.sh — Run monthly to verify backups are actually restorable
set -euo pipefail

RESTORE_DIR="/tmp/backup-test-$(date +%Y%m%d)"
LOG_FILE="/var/log/backup-test.log"

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"; }

log "=== BACKUP RESTORE TEST ==="
mkdir -p "$RESTORE_DIR"

# Restic test restore
log "Testing restic restore..."
restic restore latest --target "$RESTORE_DIR" --path /etc/passwd
if [[ -f "${RESTORE_DIR}/etc/passwd" ]]; then
  log "✓ Restic restore: PASSED"
else
  log "✗ Restic restore: FAILED — /etc/passwd not found in restore"
  exit 1
fi

# Verify a critical file exists and is readable
LINES=$(wc -l < "${RESTORE_DIR}/etc/passwd")
if [[ "$LINES" -gt 5 ]]; then
  log "✓ File content check: PASSED ($LINES lines in passwd)"
else
  log "✗ File content check: FAILED (only $LINES lines)"
  exit 1
fi

rm -rf "$RESTORE_DIR"
log "=== TEST COMPLETE: All checks passed ==="
```

---

## 3-2-1 Backup Rule

Always explain and enforce this when generating a backup strategy:

| Rule | Meaning | Implementation |
|------|---------|---------------|
| **3** copies of data | Original + 2 backups | Local backup + remote backup |
| **2** different storage media | Not just 2 copies on same disk | Local disk + cloud or remote SSH |
| **1** off-site | At least one backup physically separate | S3 / Backblaze B2 / remote VPS |

---

## Output Format

Write all scripts to `./backup/` in the current working directory:

```
backup/
  backup.sh          ← main backup script
  test-restore.sh    ← test restore script (run monthly)
  .env.example       ← required env vars (repo path, password, etc.)
  README.md          ← what each script does, restore steps
```

Then print ONLY:

```
✅ Backup solution created in ./backup/

▶ First-time setup:
  cp .env.example .env && nano .env
  source .env

  # Initialize the backup repository (run once)
  [init command for chosen tool]

▶ Run first backup:
  chmod +x backup/backup.sh
  sudo ./backup/backup.sh

▶ Automate (choose one):
  # Cron — daily at 2 AM
  echo "0 2 * * * root /path/to/backup/backup.sh" >> /etc/cron.d/backup

  # systemd timer — see /linux-cron-manager for a full timer unit

▶ Test your backup (run monthly!):
  chmod +x backup/test-restore.sh
  ./backup/test-restore.sh

💡 Follow the 3-2-1 rule: local + remote/cloud copy + test regularly.
💡 Next: /linux-cron-manager to create a systemd timer for automated backups.
```
