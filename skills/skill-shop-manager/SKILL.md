---
name: skill-shop-manager
description: Use when user says "skill shop", "list skills", "my skills", "show skills", "create new skill", "add skill", "which skill for", "recommend a skill", "install skill", "skill catalog", "what can I install", or wants to browse, manage, or discover personal skills.
---

# Skill Shop Manager

## Overview

You are the manager of Lehnert's personal skill shop. Your job: display skills, recommend the right one, and create new skills on demand.

**Core principle:** Every skill is a Markdown file (`SKILL.md`) inside its own folder under `skills/`. One skill = one folder.

## Skill Catalog

### 🗓️ Software Planning

| Skill | Description | Status |
|-------|-------------|--------|
| `sw-idea-analyzer` | Analyzes software ideas for feasibility, features, risks, and MVP scope | ✅ Active |
| `sw-diagram-creator` | Generates Mermaid, PlantUML, and ASCII diagrams from any description or sw-idea-analyzer output | ✅ Active |
| `sw-user-story-creator` | Generates a complete user story document with acceptance criteria from vision.md or any description | ✅ Active |
| `sw-use-case-creator` | Generates a detailed use case document with actors, flows, and exceptions from user-stories.md or any description | ✅ Active |
| `sw-tech-stack-planner` | Recommends the best modern Docker-first tech stack and generates requirements/tech-stack.yaml | ✅ Active |

### 💻 Software Development

| Skill | Description | Status |
|-------|-------------|--------|
| `sw-use-case-coder` | Generates production-ready code for selected use cases from tech-stack.yaml and use-cases.md | ✅ Active |
| `sw-boilerplate` | Scaffolds a complete project from tech-stack.yaml into the workspace root (root files, source structure, DB, Docker, UI — all in one command) | ✅ Active |

### 🐧 Linux & Sysadmin

| Skill | Description | Status |
|-------|-------------|--------|
| `linux-shell-scriptor` | Generates production-ready bash scripts for backups, monitoring, deployment, cron jobs, and any sysadmin task | ✅ Active |
| `linux-config-auditor` | Audits nginx, sshd, systemd units, iptables, fail2ban, sudoers, sysctl for security issues and performance gaps | ✅ Active |
| `docker-compose-writer` | Generates or optimizes docker-compose.yml from a project description or existing stack | ✅ Active |
| `linux-security-hardener` | Generates a complete server hardening plan with SSH, firewall, fail2ban, sysctl, auditd, and a ready-to-run harden.sh script | ✅ Active |
| `linux-log-analyzer` | Analyzes auth.log, syslog, nginx, Apache, journalctl, Docker, and fail2ban logs for errors, security threats, and anomalies | ✅ Active |
| `linux-monitoring-setup` | Sets up Prometheus+Grafana, Loki, Alertmanager, Uptime Kuma, or Netdata for any server or Docker host | ✅ Active |
| `linux-network-configurer` | Configures static IP, VLANs, bonding, WireGuard, iptables, nftables, DNS across all Linux network stacks | ✅ Active |
| `linux-backup-restore` | Generates backup solutions with restic, BorgBackup, rsync, or tar — encryption, remote/cloud storage, DB/Docker backups, retention, restore | ✅ Active |
| `linux-systemd-manager` | Creates, debugs, and hardens systemd services, timers, sockets, and path units — with sandboxing, resource limits, and restart policies | ✅ Active |
| `linux-cron-manager` | Generates cron jobs with /etc/cron.d/ entries and wrapper scripts — flock locking, logging, failure alerting, debugging | ✅ Active |
| `linux-selinux-helper` | Diagnoses and fixes SELinux denials — file contexts, booleans, port labels, custom policy modules | ✅ Active |
| `linux-performance-tuner` | Profiles a Linux system and generates tuned sysctl, CPU governor, I/O scheduler, and workload-specific configs | ✅ Active |

### 🔒 Pentesting

| Skill | Description | Status |
|-------|-------------|--------|
| `linux-vuln-scanner` | Scans servers, containers, and web apps for CVEs using Trivy, nmap, testssl.sh, and Nikto — authorized assessments only | ✅ Active |

---

## Automatic Recommendations

| Task / Topic | Recommended Skill |
|--------------|-------------------|
| "Analyze my software idea" | → `sw-idea-analyzer` |
| "Is my app idea worth building?" | → `sw-idea-analyzer` |
| "Help me scope an MVP" | → `sw-idea-analyzer` |
| "Check feasibility of my idea" | → `sw-idea-analyzer` |
| "Create a diagram for my app" | → `sw-diagram-creator` |
| "Generate an ERD / flowchart / sequence diagram" | → `sw-diagram-creator` |
| "Visualize my sw-idea-analyzer output" | → `sw-diagram-creator` |
| "Write user stories for my project" | → `sw-user-story-creator` |
| "Create acceptance criteria" | → `sw-user-story-creator` |
| "Turn my vision into requirements" | → `sw-user-story-creator` |
| "Create use cases for my project" | → `sw-use-case-creator` |
| "Write actor flows and exceptions" | → `sw-use-case-creator` |
| "Specify system behavior in detail" | → `sw-use-case-creator` |
| "Choose a tech stack for my project" | → `sw-tech-stack-planner` |
| "Set up Docker / docker-compose" | → `sw-tech-stack-planner` |
| "What framework should I use?" | → `sw-tech-stack-planner` |
| "Generate code for UC-001" | → `sw-use-case-coder` |
| "Implement all MVP use cases" | → `sw-use-case-coder` |
| "Scaffold files for a use case" | → `sw-use-case-coder` |
| "Create project boilerplate" | → `sw-boilerplate` |
| "Set up the project skeleton" | → `sw-boilerplate` |
| "Generate package.json / tsconfig" | → `sw-boilerplate` |
| "Create docker-compose.yml for my project" | → `sw-boilerplate` |
| "Set up Prisma / database files" | → `sw-boilerplate` |
| "Create Tailwind / shadcn setup" | → `sw-boilerplate` |

| "Write a bash script for backups" | → `linux-shell-scriptor` |
| "Create a shell script to monitor disk space" | → `linux-shell-scriptor` |
| "Generate a deployment script" | → `linux-shell-scriptor` |
| "Script to clean up old log files" | → `linux-shell-scriptor` |
| "Automate this sysadmin task with bash" | → `linux-shell-scriptor` |
| "Audit my nginx config" | → `linux-config-auditor` |
| "Is my sshd_config secure?" | → `linux-config-auditor` |
| "Review my iptables / firewall rules" | → `linux-config-auditor` |
| "Check my systemd service for security issues" | → `linux-config-auditor` |
| "Audit my sudoers file" | → `linux-config-auditor` |
| "Write a docker-compose.yml for my project" | → `docker-compose-writer` |
| "Add PostgreSQL / Redis / nginx to my compose file" | → `docker-compose-writer` |
| "Optimize my existing docker-compose.yml" | → `docker-compose-writer` |
| "Set up a Docker stack for my app" | → `docker-compose-writer` |
| "I want to self-host X" / "replace Google Y" | → `docker-compose-writer` |
| "Set up Nextcloud / Immich / Jellyfin / Gitea" | → `docker-compose-writer` |
| "Self-hosted AI / local ChatGPT" | → `docker-compose-writer` |
| "Harden my Linux server" | → `linux-security-hardener` |
| "Secure a fresh VPS" | → `linux-security-hardener` |
| "Lock down SSH / set up a firewall" | → `linux-security-hardener` |
| "CIS benchmark / security checklist for my server" | → `linux-security-hardener` |
| "Analyze my auth.log / nginx logs" | → `linux-log-analyzer` |
| "Find brute force attacks in my logs" | → `linux-log-analyzer` |
| "Why did my service crash? Check the logs" | → `linux-log-analyzer` |
| "What's happening on my server? Check syslog" | → `linux-log-analyzer` |
| "Set up Prometheus and Grafana" | → `linux-monitoring-setup` |
| "Monitor my server / Docker host" | → `linux-monitoring-setup` |
| "Set up uptime monitoring / status page" | → `linux-monitoring-setup` |
| "I want alerts when disk is full or service is down" | → `linux-monitoring-setup` |
| "Set a static IP on my Linux server" | → `linux-network-configurer` |
| "Set up WireGuard VPN" | → `linux-network-configurer` |
| "Configure iptables / nftables / firewalld" | → `linux-network-configurer` |
| "Configure VLANs / bonding / bridge" | → `linux-network-configurer` |
| "My network stopped working" | → `linux-network-configurer` |
| "Back up my server / files / database" | → `linux-backup-restore` |
| "Set up automated backups with restic or borg" | → `linux-backup-restore` |
| "Restore from a backup" | → `linux-backup-restore` |
| "Back up Docker volumes to S3 / Backblaze" | → `linux-backup-restore` |
| "Run my app as a systemd service" | → `linux-systemd-manager` |
| "Create a systemd timer (cron replacement)" | → `linux-systemd-manager` |
| "My systemd service keeps failing / crashing" | → `linux-systemd-manager` |
| "Harden my systemd service unit" | → `linux-systemd-manager` |
| "Schedule a cron job" | → `linux-cron-manager` |
| "My cron job isn't running" | → `linux-cron-manager` |
| "Cron job with locking and logging" | → `linux-cron-manager` |
| "Convert cron to systemd timer" | → `linux-cron-manager` |
| "SELinux is blocking my app" | → `linux-selinux-helper` |
| "AVC denial / permission denied on RHEL/Rocky" | → `linux-selinux-helper` |
| "Fix SELinux file context / restorecon" | → `linux-selinux-helper` |
| "Write a custom SELinux policy module" | → `linux-selinux-helper` |
| "My server is slow / high load" | → `linux-performance-tuner` |
| "Tune kernel parameters / sysctl" | → `linux-performance-tuner` |
| "Set CPU governor to performance" | → `linux-performance-tuner` |
| "Optimize for PostgreSQL / web / HPC" | → `linux-performance-tuner` |
| "Profile where my server is spending time" | → `linux-performance-tuner` |
| "Scan my server for vulnerabilities / CVEs" | → `linux-vuln-scanner` |
| "What ports are open on my server?" | → `linux-vuln-scanner` |
| "Audit my SSL/TLS configuration" | → `linux-vuln-scanner` |
| "Run a web app security scan" | → `linux-vuln-scanner` |
| "Check my Docker images for CVEs" | → `linux-vuln-scanner` |

---

## New Here? Common Starting Points

| Goal | Recommended path |
|------|-----------------|
| **Build a new web app from scratch** | `sw-idea-analyzer` → `sw-user-story-creator` → `sw-use-case-creator` → `sw-tech-stack-planner` → `sw-boilerplate` → `sw-use-case-coder` |
| **Self-host an app (Nextcloud, Jellyfin, etc.)** | `docker-compose-writer` |
| **Harden a fresh server / VPS** | `linux-security-hardener` |
| **Something is broken on my server** | `linux-log-analyzer` |
| **Set up backups** | `linux-backup-restore` |
| **Monitor my server** | `linux-monitoring-setup` |
| **Write a bash script** | `linux-shell-scriptor` |

> No skill for your task yet? Say: **"Create the skill [name] for me"** – I'll build the folder and SKILL.md right away.

---

## How Skills Chain Together (Software Development Pipeline)

Skills pass information through **files on disk**, not through chat history. Each skill reads what the previous one wrote and writes its own output. You can run them in separate conversations — they always pick up from disk.

```
/sw-idea-analyzer
  └─ writes → requirements/vision.md

/sw-user-story-creator        (reads vision.md)
  └─ writes → requirements/user-stories.md

/sw-use-case-creator          (reads vision.md + user-stories.md)
  └─ writes → requirements/use-cases.md

/sw-tech-stack-planner        (reads vision.md + user-stories.md + use-cases.md)
  └─ writes → requirements/tech-stack.yaml

/sw-boilerplate               (reads tech-stack.yaml)
  └─ writes → all root config files, src/, docker-compose.yml, Dockerfile, etc.
              directly into the workspace root

/sw-use-case-coder UC-001     (reads tech-stack.yaml + use-cases.md)
  └─ writes → production-ready code for each use case into the workspace root
```

**Key rules:**
- Every skill writes files **silently** (no file content in chat, just a ✅ confirmation)
- If a required input file is missing, the skill stops and tells you which skill to run first
- You can run any skill standalone by passing a description directly (no prior files needed)

## Adding a New Skill

Just say:
> "Create skill for [topic/task]"

I will automatically create:
```
skills/
  your-skill-name/
    SKILL.md    ← with YAML frontmatter, trigger description, and template
```

### Manual Installation (advanced)

```bash
# 1. Create folder
mkdir -p ~/.claude/skills/your-skill-name

# 2. Create SKILL.md with minimum content:
cat > ~/.claude/skills/your-skill-name/SKILL.md << 'EOF'
---
name: your-skill-name
description: Use when [specific trigger situation]
---

# Skill Title

## Overview
[What does this skill do?]

## Instructions
[Step-by-step or table]
EOF
```

## Installation Paths

| Platform | Path |
|----------|------|
| **Claude Code** | `~/.claude/skills/` |
| **Claude.ai** | Upload the SKILL.md file manually in the chat |
| **This repository** | `skills/` (source for both) |

## Status Legend

| Icon | Meaning |
|------|---------|
| ✅ Active | Skill is finished and ready to use |
| 🚧 In Progress | Skill is currently being developed |
| 🔲 Planned | Slot reserved, not yet created |
