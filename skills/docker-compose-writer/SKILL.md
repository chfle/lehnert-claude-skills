---
name: docker-compose-writer
description: Use when user wants to write, generate, create, or optimize a docker-compose.yml — for a new project, an existing stack, a self-hosted app, a database, or when asked to set up tools like note-taking apps, password managers, media servers, monitoring, git hosting, file storage, CI/CD, analytics, or any service that runs in Docker.
version: 1.1.0
author: Lehnert
---

# Docker Compose Writer

## Overview

Two modes:

**Selection Mode** — User asks for a category ("I need a database", "I want a note-taking app", "set up monitoring"). Present 3–4 real options with a short description. User picks one. Generate the full stack.

**Direct Mode** — User names a specific tool or pastes an existing compose. Generate or optimize immediately, no questions.

**Language:** Respond in the user's language. YAML keys and comments always in English.

---

## Step 1 — Detect Mode

| User says | Mode |
|-----------|------|
| Names a specific tool ("Vaultwarden", "Gitea", "Nextcloud") | Direct → generate immediately |
| Pastes an existing compose file | Direct → optimize it |
| Names a category ("a database", "monitoring", "a wiki") | Selection → show options |
| Describes a need ("I want to store files", "I need to track uptime") | Selection → map to category → show options |
| Describes a custom app stack ("Next.js + Postgres + Redis") | Direct → generate immediately |

---

## Step 2 — Selection Mode: Present Options

When in Selection Mode, respond with a numbered list like this:

> Here are the best options for **[category]**. Which one fits your needs?
>
> **1. [App Name]** — [one sentence what it is]
> Best for: [who/what it's best for] · Requires: [any dependencies, e.g. "PostgreSQL"]
>
> **2. [App Name]** — [one sentence what it is]
> Best for: [who/what it's best for] · Requires: [any dependencies]
>
> **3. [App Name]** — [one sentence what it is]
> Best for: [who/what it's best for] · Requires: [any dependencies]
>
> *(Optional)* **4. [App Name]** — ...
>
> Type a number to generate the full Docker Compose stack, or tell me more about what you need.

Never ask follow-up questions during selection — the user picks a number, then you generate.

---

## App Catalogue

Use these options per category. Always recommend the best options for self-hosting.

### 📦 Databases

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **PostgreSQL** | Industry-standard relational database | Most apps, complex queries |
| 2 | **MariaDB** | MySQL-compatible, fast, reliable | WordPress, legacy apps, wide compatibility |
| 3 | **MongoDB** | Document-oriented NoSQL database | JSON-heavy apps, flexible schemas |
| 4 | **Redis** | In-memory cache, session store, message broker | Caching, queues, pub/sub |

Extras to suggest alongside: **pgAdmin** (PostgreSQL UI), **RedisInsight** (Redis UI), **Mongo Express** (MongoDB UI)

### 📝 Notes & Knowledge Base

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Trilium Notes** | Hierarchical personal knowledge base with rich text, code, and relational notes | Power users, large personal wikis |
| 2 | **Joplin Server** | End-to-end encrypted note syncing backend for Joplin clients | Users who already use Joplin |
| 3 | **Outline** | Modern team wiki with Slack/Google auth | Teams, documentation |
| 4 | **BookStack** | Simple, structured wiki with books, chapters, and pages | Personal or team documentation |

### 🔒 Password Managers

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Vaultwarden** | Unofficial Bitwarden-compatible server, extremely lightweight | Personal or family use |
| 2 | **Passbolt CE** | Team-oriented password manager with GPG encryption | Teams, organizations |

### ☁️ File Storage & Cloud

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Nextcloud** | Full-featured self-hosted cloud: files, calendar, contacts, office | Full Google Drive / Dropbox replacement |
| 2 | **Seafile** | Fast, reliable file sync and share | Pure file sync, large file collections |
| 3 | **Filebrowser** | Minimal web-based file browser and upload tool | Simple file access without full cloud |

### 📊 Monitoring & Observability

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Prometheus + Grafana** | Industry-standard metrics collection and visualization | Server and app metrics dashboards |
| 2 | **Uptime Kuma** | Beautiful self-hosted uptime and status page monitor | Website/service availability monitoring |
| 3 | **Netdata** | Real-time system metrics with zero config | Live system performance monitoring |
| 4 | **Dozzle** | Real-time Docker log viewer in the browser | Quick Docker container log access |

### 🔀 Reverse Proxy

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Traefik v3** | Auto-discovers Docker services, built-in Let's Encrypt TLS | Multi-service setups, automated HTTPS |
| 2 | **Nginx Proxy Manager** | Web UI for nginx reverse proxy with Let's Encrypt | Users who prefer a GUI over config files |
| 3 | **Caddy** | Automatic HTTPS, simple Caddyfile config | Simple setups, fast to configure |

### 🐙 Git & Code Hosting

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Gitea** | Lightweight self-hosted Git service | Personal or small team Git hosting |
| 2 | **Forgejo** | Community fork of Gitea, more open governance | Same as Gitea, community-driven |
| 3 | **GitLab CE** | Full DevOps platform: Git, CI/CD, registry, issues | Teams needing full GitLab feature set |

### 🚀 CI/CD

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Woodpecker CI** | Lightweight, Gitea/Forgejo-native container CI | Teams using Gitea/Forgejo |
| 2 | **Drone CI** | Container-native CI with pipeline-as-code | Modern container-first pipelines |
| 3 | **Jenkins** | Battle-tested, massively extensible CI server | Complex pipelines, many plugins needed |

### 🎬 Media

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Jellyfin** | Fully open-source media server, no premium features | Personal media streaming, privacy-first |
| 2 | **Plex** | Feature-rich media server with mobile apps and remote access | Best client support, polished UX |
| 3 | **Navidrome** | Lightweight music streaming server (Subsonic-compatible) | Music-only, very fast and minimal |

### ✅ Project Management & Productivity

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Plane** | Open-source Linear/Jira alternative with issues, cycles, modules | Teams, software projects |
| 2 | **Vikunja** | Self-hosted to-do app with tasks, teams, and projects | Personal and small team task management |
| 3 | **Kanboard** | Minimal Kanban board with time tracking | Simple Kanban workflow |

### 📈 Analytics

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Plausible** | Privacy-friendly, GDPR-compliant web analytics | Privacy-conscious website owners |
| 2 | **Umami** | Fast, minimal analytics with multi-site support | Simple, lightweight alternative to GA |
| 3 | **Matomo** | Full-featured analytics with full data ownership | Full GA replacement |

### 🏠 Home Automation

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Home Assistant** | The gold standard self-hosted home automation platform | Full smart home control |
| 2 | **Node-RED** | Visual flow-based programming for IoT and automation | Custom automations and integrations |

### 🛠️ Developer Tools

| # | App | Description | Best for |
|---|-----|-------------|----------|
| 1 | **Portainer CE** | Docker and Swarm management web UI | Visual Docker management |
| 2 | **pgAdmin 4** | Web-based PostgreSQL management tool | PostgreSQL administration |
| 3 | **Stirling PDF** | 50+ PDF operations in a self-hosted web UI | PDF processing without cloud tools |
| 4 | **IT-Tools** | Collection of developer utility tools in a web UI | Quick dev utilities |

---

## Step 3 — Generate the Compose Stack

After the user selects an option (or in Direct Mode), generate the full stack immediately. No more questions.

### Quality Standards — Every compose file MUST have:

| Element | Rule |
|---------|------|
| Compose version | `version: '3.8'` minimum |
| Named networks | At least one; never rely on default bridge |
| Named volumes | All persistent data in named volumes — no bind mounts for DB data |
| Health checks | On every service others `depends_on` |
| `depends_on` condition | `condition: service_healthy`, not just `service_started` |
| Env variables | Via `${ENV_VAR}` — never hardcode passwords inline |
| Restart policy | `restart: unless-stopped` |
| Image pinning | Specific tags (e.g. `postgres:16-alpine`) — never `latest` |
| Security | `cap_drop: [ALL]`, read-only config mounts `:ro`, no `privileged: true` |

---

## Step 4 — Output Format

Output in this exact order, nothing else:

### 1. One-line intro
```
Here is the full Docker Compose stack for [App Name]:
```

### 2. docker-compose.yml
Complete file in a fenced `yaml` code block. Well-commented. Production-ready.

### 3. .env.example
All required environment variables with example/placeholder values. In a fenced code block labeled `.env.example`.

```bash
# .env.example — copy to .env and fill in your values

POSTGRES_DB=myapp
POSTGRES_USER=myuser
POSTGRES_PASSWORD=change_me_strong_password
```

### 4. Setup & run commands
Exact commands the user needs to run, in order. No vague instructions — copy-paste ready.

```bash
# 1. Create the project directory
mkdir -p ~/docker/[app-name] && cd ~/docker/[app-name]

# 2. Save the compose file (paste content above)
nano docker-compose.yml

# 3. Create your .env from the example
cp .env.example .env
nano .env  # fill in your passwords and settings

# 4. Start the stack
docker compose up -d

# 5. Verify all services are healthy
docker compose ps

# 6. View logs if something is wrong
docker compose logs -f

# 7. Access the app
# → http://localhost:[PORT]  or  https://[your-domain]
```

### 5. Useful day-to-day commands
Always include this block after setup:

```bash
# Stop the stack
docker compose down

# Stop and remove volumes (DELETES ALL DATA)
docker compose down -v

# Pull latest images and restart
docker compose pull && docker compose up -d

# Backup a named volume
docker run --rm -v [app]_data:/data -v $(pwd):/backup alpine \
  tar czf /backup/[app]-backup-$(date +%Y%m%d).tar.gz -C /data .

# Restore a backup
docker run --rm -v [app]_data:/data -v $(pwd):/backup alpine \
  tar xzf /backup/[app]-backup-YYYYMMDD.tar.gz -C /data
```

### 6. Next steps (always include)
> **Expose to the internet?** Add Traefik with Let's Encrypt — run `/docker-compose-writer Traefik reverse proxy`
> **Harden the host first:** `/linux-security-hardener`
> **Monitor this stack:** `/linux-monitoring-setup`
> **Automate updates:** `/linux-shell-scriptor script to pull and restart Docker containers weekly`

---

## Optimize Mode (existing compose pasted)

When the user pastes an existing compose file:

1. Identify all issues (missing health checks, `latest` tags, hardcoded secrets, missing networks, no restart policy, etc.)
2. Print a short findings summary: "Found X issues"
3. Output the fully corrected compose file
4. List what changed and why (brief, one line per change)
5. Show the same setup/run commands section

---

## Security Rules (non-negotiable)

- **No inline secrets** — `${ENV_VAR}` only, never `password: mysecret`
- **Read-only config mounts** — always `:ro` for config files
- **`cap_drop: [ALL]`** — drop all Linux capabilities, add back only what's needed
- **Never `privileged: true`** — if user explicitly needs it, warn them and explain the risk
- **Non-root user** — set `user: "1000:1000"` for custom app services where supported
- **Internal ports only** — services that don't need to be public must NOT have `ports:` — use the Docker network instead
