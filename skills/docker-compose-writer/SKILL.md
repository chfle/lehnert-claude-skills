---
name: docker-compose-writer
description: Use when user wants to write, generate, create, or optimize a docker-compose.yml — for a new project, an existing stack, any self-hosted app, or when they want to replace a cloud service (Google Drive, Gmail, GitHub, Slack, Notion, ChatGPT, etc.) with a self-hosted alternative.
version: 2.4.0
author: Lehnert
---

# Docker Compose Writer

## Overview

Three modes:

**Selection Mode** — User asks for a category or vague need → present 3–4 real options → user picks → generate complete stack.

**Direct Mode** — User names a specific app or describes a full custom stack → generate immediately.

**Optimize Mode** — User pastes existing `docker-compose.yml` → audit and fix issues → write improved version.

**Language:** Respond in the user's language. YAML keys and comments always in English.

---

## Step 1 — Detect Mode and Handle "Replace X" Requests

First, map "I want to replace X" requests to the right category:

| User wants to replace / run | Point to |
|----------------------------|----------|
| Google Drive / Dropbox | File Storage → Nextcloud, Seafile |
| Google Photos / iCloud Photos | Photos → Immich |
| Gmail / email hosting | Email → Mailcow, Mailu |
| GitHub / GitLab | Git Hosting → Gitea, Forgejo, GitLab CE |
| Slack / Teams | Chat → Mattermost, Element/Matrix |
| Notion / Confluence | Notes/Wiki → Outline, BookStack, Trilium |
| LastPass / 1Password / Bitwarden | Passwords → Vaultwarden, Passbolt |
| Trello / Jira / Linear | Project Management → Plane, Vikunja |
| Google Analytics | Analytics → Plausible, Umami, Matomo |
| ChatGPT / Claude (locally) | AI → Ollama + Open WebUI |
| Spotify (music streaming) | Media → Navidrome |
| Netflix (video streaming) | Media → Jellyfin |
| YouTube (video hosting) | Media → PeerTube |
| Evernote / Apple Notes sync | Notes → Joplin Server, Trilium |
| Pocket / Instapaper | Read Later → Wallabag |
| Feedly / RSS reader | RSS → FreshRSS, Miniflux |
| Google Bookmarks | Bookmarks → Linkding, Hoarder |
| Mint / YNAB (budgeting) | Finance → Firefly III, Actual Budget |
| Goodreads / e-book library | Books → Calibre-Web, Kavita |
| DNS / ad blocking | Network → Pi-hole, AdGuard Home |
| Okta / Auth0 / SSO | Identity → Authelia, Keycloak |

Then decide mode:

| User says | Mode |
|-----------|------|
| Names specific app ("Vaultwarden", "Gitea", "Nextcloud") | Direct → generate immediately |
| Pastes existing compose file | Optimize → audit and fix |
| Names category ("database", "notes", "monitoring") | Selection → show 3–4 options |
| Describes vague need ("I want to back up my photos") | Map to category → Selection |
| Custom app stack ("Next.js + Postgres + Redis") | Direct → generate immediately |
| "I want to replace Google Drive" | Map → File Storage Selection |

---

## Step 2 — Selection Mode: Present Options

When in Selection Mode, respond with:

> Here are the best self-hosted options for **[need]**. Which fits your use case?
>
> **1. [App Name]** — [one sentence what it is]
> Best for: [who/what it's best for] · RAM: [rough requirement] · Requires: [dependencies]
>
> **2. [App Name]** — [one sentence]
> Best for: ... · RAM: ... · Requires: ...
>
> **3. [App Name]** — [one sentence]
> Best for: ...
>
> *(Optional)* **4. [App Name]** — ...
>
> Type a number to generate the full Docker Compose stack.

Never ask follow-up questions during selection — user picks a number, then generate immediately.

---

## App Catalogue

### 📦 Databases & Data Stores

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **PostgreSQL** | Industry-standard relational DB | 256MB+ | Most apps, complex queries |
| **MariaDB** | MySQL-compatible, fast, reliable | 256MB+ | WordPress, legacy apps |
| **MongoDB** | Document NoSQL database | 512MB+ | JSON-heavy, flexible schemas |
| **Redis** | In-memory cache, session store, queue | 64MB+ | Caching, pub/sub, rate limiting |
| **ClickHouse** | Columnar DB for analytics, extremely fast | 1GB+ | Time-series data, large-scale analytics |
| **InfluxDB** | Purpose-built time-series database | 256MB+ | IoT metrics, monitoring |

*Extras to suggest alongside:* pgAdmin (PostgreSQL UI) · RedisInsight (Redis UI) · Mongo Express (MongoDB UI)

---

### 📝 Notes, Wikis & Knowledge Base

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Outline** | Modern team wiki, Slack/Google auth, real-time | 512MB+ | Teams, documentation |
| **BookStack** | Structured wiki: books → chapters → pages | 256MB+ | Simple personal/team docs |
| **Trilium Notes** | Hierarchical personal KB, code, relations | 256MB+ | Power users, large wikis |
| **Joplin Server** | Note sync backend for Joplin clients (E2E encrypted) | 128MB+ | Existing Joplin users |
| **SilverBullet** | Markdown-based PKM, self-hosted Obsidian alternative | 64MB+ | Markdown note-takers |
| **Affine** | Next-gen Notion/Miro hybrid — docs + whiteboard | 512MB+ | Visual knowledge work |

---

### 🔒 Password Managers

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Vaultwarden** | Unofficial Bitwarden server, extremely lightweight | 64MB+ | Personal/family |
| **Passbolt CE** | Team password manager with GPG | 256MB+ | Teams, organizations |

---

### ☁️ File Storage & Cloud

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Nextcloud** | Full Google Drive replacement: files, calendar, contacts, office | 512MB+ | Full cloud suite |
| **Seafile** | Fast file sync and share, reliable | 256MB+ | Pure file sync |
| **Filebrowser** | Minimal web file browser and uploader | 32MB+ | Simple access, no sync needed |
| **Syncthing** | Peer-to-peer continuous file sync (no server-side storage) | 128MB+ | Device sync without central server |

---

### 📸 Photos

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Immich** | Self-hosted Google Photos: auto-backup, face recognition, albums, mobile app | 2GB+ | Phone photo backup |
| **PhotoPrism** | AI-powered photo manager, facial recognition, maps | 1GB+ | Large collections, AI tags |
| **Photoview** | Fast, simple photo viewer for existing directory structure | 256MB+ | Existing photo directories |
| **Piwigo** | Traditional photo gallery with albums and sharing | 256MB+ | Public photo sharing |

*Note for Immich:* Requires PostgreSQL + Redis + Machine Learning service in the same stack.

---

### 🎬 Media Servers

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Jellyfin** | Fully open-source media server, no subscription | 512MB+ | Privacy-first streaming |
| **Plex** | Feature-rich server with mobile apps, remote access | 1GB+ | Best client support |
| **Navidrome** | Lightweight music server (Subsonic API) | 64MB+ | Music-only, minimal |
| **Audiobookshelf** | Audiobook + podcast server with mobile apps | 256MB+ | Audiobooks, podcasts |

*Media automation (* arr stack):* Suggest Sonarr + Radarr + Prowlarr + qBittorrent together when user asks for automated downloads.

---

### 📥 Media Automation (*arr Stack)

| App | Description |
|-----|-------------|
| **Sonarr** | TV show monitoring and download automation |
| **Radarr** | Movie monitoring and download automation |
| **Lidarr** | Music monitoring and download automation |
| **Prowlarr** | Indexer manager for Sonarr/Radarr/Lidarr |
| **qBittorrent** | Torrent client with web UI |
| **SABnzbd** | Usenet downloader |

*Always generate the full *arr stack together when user asks for any of these — they're meant to work as a unit.*

---

### 🤖 AI / LLM

| App | Description | RAM / GPU | Best for |
|-----|-------------|-----------|----------|
| **Ollama + Open WebUI** | Run LLMs locally (Llama, Mistral, etc.) with a ChatGPT-like UI | 8GB+ RAM, GPU optional | Local AI chat, privacy |
| **LocalAI** | OpenAI-compatible API for local models | 4GB+ | API-compatible local AI |
| **Stable Diffusion WebUI** | Local image generation (AUTOMATIC1111) | 4GB+ GPU | AI image generation |
| **Open WebUI** (standalone) | Chat UI that connects to external OpenAI/Anthropic APIs | 256MB+ | Self-hosted chat frontend |

*Note for Ollama:* If user has NVIDIA GPU, add `deploy: resources: reservations: devices` for GPU passthrough.

---

### 📧 Email

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Mailcow** | Full-featured mail server: SMTP, IMAP, web UI, spam filter | 2GB+ | Complete email hosting |
| **Mailu** | Simpler mail server, easier setup than Mailcow | 1GB+ | Simple email hosting |
| **Stalwart Mail** | Modern all-in-one mail server (JMAP/SMTP/IMAP) | 256MB+ | Modern standards, low resource |

*Warning: Email delivery requires proper DNS (SPF, DKIM, DMARC, PTR) — mention this when generating.*

---

### 🛡️ Identity, SSO & Access

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Authelia** | Lightweight SSO + 2FA proxy for nginx/Traefik | 64MB+ | Protecting services with single login |
| **Keycloak** | Full enterprise identity provider, OIDC/SAML | 512MB+ | Full SSO platform, many apps |
| **Zitadel** | Modern cloud-native identity platform | 256MB+ | Developer-friendly, modern stack |
| **Authentik** | Flexible IdP with beautiful UI | 512MB+ | Best balance of features and ease |

---

### 🔀 Reverse Proxy

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Traefik v3** | Auto-discovers Docker services, Let's Encrypt TLS | 64MB+ | Multi-service, automated HTTPS |
| **Nginx Proxy Manager** | Web UI for nginx reverse proxy + Let's Encrypt | 256MB+ | GUI over config files |
| **Caddy** | Automatic HTTPS, simple Caddyfile config | 64MB+ | Simple setups |

---

### 🌐 Network & Ad Blocking

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Pi-hole** | DNS-based ad blocker for entire network | 64MB+ | Network-wide ad blocking |
| **AdGuard Home** | DNS ad blocker with modern UI, DoH/DoT support | 64MB+ | Same as Pi-hole, more modern UI |
| **Unbound** | Validating, recursive DNS resolver | 32MB+ | Privacy-focused recursive DNS |
| **WireGuard (wg-easy)** | WireGuard VPN with simple web UI | 32MB+ | Easy VPN setup with UI |

---

### 📖 RSS & Read Later

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **FreshRSS** | Full-featured RSS/Atom aggregator | 128MB+ | Power RSS users |
| **Miniflux** | Minimalist, fast RSS reader | 32MB+ | Simple, fast, no-frills |
| **Wallabag** | Save articles for later reading (Pocket replacement) | 256MB+ | Read-later queue |

---

### 📚 Books, Comics & Audiobooks

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Calibre-Web** | Web UI for Calibre e-book library, OPDS support | 256MB+ | E-book library management |
| **Kavita** | Fast manga/comic/book server, cross-platform reader | 128MB+ | Comics, manga, mixed library |
| **Komga** | Comic/manga server with OPDS + web reader | 256MB+ | Comics focus, excellent reader |
| **Audiobookshelf** | Audiobook and podcast server with mobile apps | 256MB+ | Audiobooks + podcasts |

---

### 🔗 Bookmarks & Links

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Linkding** | Minimal bookmark manager, browser extensions | 64MB+ | Simple, fast, self-hosted bookmarks |
| **Hoarder** | AI-powered bookmark manager with auto-tagging | 512MB+ | Smart bookmarks with AI tags |
| **Shaarli** | Personal link sharing / bookmark tool | 32MB+ | Simple, PHP-based, no DB |

---

### 💰 Finance & Budget

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Firefly III** | Personal finance manager, budgets, accounts, reports | 256MB+ | Detailed personal finance |
| **Actual Budget** | Modern local-first budget app (YNAB alternative) | 64MB+ | Simple, fast budgeting |

---

### 📊 Analytics & Business Intelligence

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Plausible** | Privacy-friendly, GDPR-compliant web analytics | 512MB+ | Privacy-first website analytics |
| **Umami** | Minimal, fast analytics, multi-site | 256MB+ | Simple GA replacement |
| **Matomo** | Full-featured analytics, full data ownership | 512MB+ | Full Google Analytics replacement |
| **Metabase** | BI tool — query databases visually, dashboards | 1GB+ | SQL queries without SQL knowledge |
| **Grafana** | Metric visualization + dashboards | 256MB+ | Technical dashboards, time-series |

---

### 🐙 Git & Code Hosting

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Gitea** | Lightweight self-hosted Git | 128MB+ | Personal/small team |
| **Forgejo** | Community fork of Gitea | 128MB+ | Same, more open governance |
| **GitLab CE** | Full DevOps platform: Git, CI/CD, registry | 4GB+ | Teams needing full GitLab |

---

### 🚀 CI/CD

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Woodpecker CI** | Gitea/Forgejo-native, lightweight | 256MB+ | Gitea/Forgejo users |
| **Drone CI** | Container-native pipeline-as-code | 256MB+ | Modern container CI |
| **Jenkins** | Battle-tested, massively extensible | 1GB+ | Complex pipelines, many plugins |
| **Tekton** | Kubernetes-native CI/CD pipelines | 512MB+ | K8s environments |

---

### 📦 Container Registry

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Harbor** | Enterprise container registry with security scanning | 2GB+ | Production, security scanning |
| **Zot** | OCI-native, minimal, production-ready registry | 64MB+ | Lightweight, OCI standards |
| **Docker Registry** | Official Docker registry (minimal) | 32MB+ | Simple internal registry |

---

### ✅ Project Management & Productivity

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Plane** | Open-source Linear/Jira alternative | 1GB+ | Teams, software projects |
| **Vikunja** | To-do app with tasks, teams, projects | 128MB+ | Personal/small team tasks |
| **Focalboard** | Kanban/calendar boards (open-source Trello) | 128MB+ | Simple Kanban |

---

### 🗓️ Calendar & Contacts

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Radicale** | Lightweight CalDAV + CardDAV server | 32MB+ | Simple calendar + contacts sync |
| **Nextcloud** | Full calendar, contacts, tasks as part of cloud suite | 512MB+ | Part of full Nextcloud setup |

---

### 💬 Chat & Communication

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Mattermost** | Open-source Slack: channels, DMs, integrations | 512MB+ | Team chat |
| **Matrix + Element** | Federated, E2E encrypted chat (Synapse + Element Web) | 1GB+ | Secure, federated messaging |
| **Rocket.Chat** | Full-featured chat platform, video calls | 1GB+ | Enterprise, video calls |

---

### 🔔 Notifications

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Ntfy** | Simple pub/sub push notification server | 32MB+ | Server alerts, cron failures |
| **Gotify** | Self-hosted push notifications with Android app | 32MB+ | Android push notifications |

---

### 📊 Monitoring & Observability

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Prometheus + Grafana** | Industry-standard metrics + visualization | 512MB+ | Server/app dashboards |
| **Uptime Kuma** | Uptime monitor + status page | 128MB+ | Service availability |
| **Netdata** | Real-time metrics, zero config | 256MB+ | Live system monitoring |
| **Dozzle** | Real-time Docker log viewer | 16MB+ | Quick container log access |

*For full observability stacks → use `/linux-monitoring-setup` instead.*

---

### 🏠 Home Automation

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Home Assistant** | Gold standard home automation | 512MB+ | Full smart home |
| **Node-RED** | Visual flow-based IoT programming | 128MB+ | Custom automations |

---

### 🛠️ Developer Tools & Utilities

| App | Description | RAM | Best for |
|-----|-------------|-----|----------|
| **Portainer CE** | Docker management web UI | 128MB+ | Visual Docker management |
| **pgAdmin 4** | PostgreSQL web admin | 256MB+ | PostgreSQL administration |
| **Stirling PDF** | 50+ PDF operations in browser | 256MB+ | PDF processing |
| **IT-Tools** | Developer utility tools web UI | 32MB+ | Quick dev utilities |
| **Dashdot** | Beautiful server stats dashboard | 64MB+ | Server overview page |
| **Homepage** | Personal dashboard for all your services | 32MB+ | Homelab start page |
| **SearXNG** | Privacy-respecting meta search engine | 128MB+ | Self-hosted search |
| **Shlink** | URL shortener with analytics | 128MB+ | Link shortening |
| **Infisical** | Open-source secrets manager (Vault alternative) | 512MB+ | App secrets management |

---

## Step 3 — Generate the Compose Stack

After the user selects (or in Direct Mode), generate the **complete, working** stack immediately. No more questions.

### Quality Standards — Every compose file MUST have:

| Element | Rule |
|---------|------|
| No `version:` field | Omit it — Compose V2 ignores it; Compose V1 compatibility not needed |
| Named networks | At least one; never rely on default bridge |
| Named volumes | All persistent data in named volumes — no bind mounts for DB data |
| Health checks | On every service others `depends_on` — use real test commands |
| `depends_on` condition | `condition: service_healthy`, not `service_started` |
| Env variables | Via `${ENV_VAR}` — never hardcode passwords or secrets inline |
| Restart policy | `restart: unless-stopped` on every service |
| Image tags | Specific pinned tags (e.g. `postgres:16-alpine`) — **never** `latest` |
| Log limits | `logging: options: max-size: "10m" max-file: "3"` to prevent disk fill |
| Security | `cap_drop: [ALL]`, read-only config mounts `:ro`, no `privileged: true` unless required |
| Internal-only ports | Services not exposed to host must NOT have `ports:` — use Docker network |

### Companion Service Health Check Templates

When generating Redis as a companion service, always include a health check so dependent services use `condition: service_healthy`:

```yaml
  redis:
    image: redis:7-alpine
    restart: unless-stopped
    volumes:
      - redis_data:/data
    networks:
      - app_network
    healthcheck:
      test: ["CMD", "redis-cli", "--raw", "ping"]
      # redis:7-alpine includes redis-cli. For minimal custom images use:
      # test: ["CMD", "sh", "-c", "redis-cli ping | grep -q PONG"]
      interval: 10s
      timeout: 5s
      retries: 5
```

---

### App-Specific Requirements

Some apps require specific companion services — always include them:

| App | Required companions |
|-----|-------------------|
| **Immich** | PostgreSQL 16 + Redis + `immich-machine-learning` service |
| **Nextcloud** | MariaDB or PostgreSQL + Redis (for caching + locking) |
| **Mailcow** | Do not use docker-compose-writer — Mailcow has its own installer; direct user to `curl -LO https://github.com/mailcow/mailcow-dockerized/archive/master.tar.gz` |
| **Outline** | PostgreSQL + Redis |
| **Plane** | PostgreSQL + Redis + MinIO (object storage) |
| **Authentik** | PostgreSQL + Redis |
| **Keycloak** | PostgreSQL |
| **Matrix/Synapse** | PostgreSQL |
| **GitLab CE** | PostgreSQL + Redis (bundled in official image, or external) |
| **Metabase** | PostgreSQL (for app metadata) |
| **Firefly III** | MariaDB or PostgreSQL |
| **Actual Budget** | None (local SQLite, no companion needed) |
| **Hoarder** | Redis + `meilisearch` for full-text search |
| **Ollama + Open WebUI** | No companions needed; add GPU passthrough if user has NVIDIA |

### NVIDIA GPU Passthrough (for Ollama, Stable Diffusion)

Ask if user has NVIDIA GPU. If yes, add to the service:
```yaml
runtime: nvidia       # Required — separate from deploy.resources
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: all
          capabilities: [gpu]
```

Requirements:
- NVIDIA Container Toolkit must be installed on host (both `runtime:` and `deploy:` require it)
- After install: `sudo systemctl restart docker`
- Test GPU access: `docker run --rm --runtime=nvidia nvidia/cuda:12.0-base nvidia-smi`

Install guide: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html

---

## Step 4 — Write Files Silently

Do NOT print the compose file or .env.example content in chat. Write them to disk silently.

**File locations** — kebab-case subdirectory in current working directory:
```
[app-name]/
  docker-compose.yml    ← complete, production-ready
  .env.example          ← all required vars with descriptions and safe placeholder values
```

**Generate complete .env.example** — every env var must have:
- A comment explaining what it is
- A safe placeholder value (not empty — `CHANGE_ME_strong_password_here`, `your-domain.com`, etc.)
- A note if it's auto-generated (e.g. `# Generate with: openssl rand -hex 32`)

### Output — print ONLY this after writing:

```
✅ [App Name] stack created in ./[app-name]/

▶ Setup:
  cd [app-name]
  cp .env.example .env
  nano .env              # ← edit ALL passwords and domain settings first!

▶ Start:
  docker compose up -d

▶ Check health:
  docker compose ps
  docker compose logs -f [service-name]

📋 Useful commands:
  docker compose pull && docker compose up -d    # update to latest images
  docker compose down                            # stop (data preserved)
  docker compose down -v                         # ⚠️  stop + DELETE ALL DATA

🌐 Access: http://localhost:[PORT]
[default login credentials if applicable]

⚠️  [Any important warnings — email DNS, firewall ports, first-run setup steps]

💡 Next steps:
  [1-2 relevant next steps, e.g. "Set up Traefik for HTTPS → /docker-compose-writer Traefik"]
  [e.g. "Back up volumes → /linux-backup-restore Docker volumes"]
  [e.g. "Monitor this stack → /linux-monitoring-setup for Prometheus + Grafana"]
```

List each UI separately if multiple services expose ports:
```
🌐 App:      http://localhost:8080
🌐 Admin UI: http://localhost:9000  (admin / [password from .env])
```

---

## Optimize Mode (existing compose pasted)

When user pastes an existing compose file:

1. Audit silently for all issues:
   - `latest` image tags → pin to specific version
   - Hardcoded secrets → replace with `${ENV_VAR}`
   - Missing health checks → add working test commands
   - Missing restart policy → add `unless-stopped`
   - Default bridge network → add named network
   - Missing log limits → add `logging` block
   - `privileged: true` without reason → warn and remove if possible
   - Bind mounts for DB data → move to named volumes

2. Write fixed `docker-compose.yml` to `./[app-name]/docker-compose.yml`
3. If secrets were hardcoded, also write `.env.example`

4. Print only:
```
✅ Optimized compose written to ./[app-name]/docker-compose.yml
   Fixed [N] issues:
   - Pinned [X] image tags (removed :latest)
   - Moved secrets to .env.example
   - Added health checks to [services]
   - Added log rotation limits
   - [other changes]

🌐 Access: http://localhost:[PORT]
```

---

## Security Rules (non-negotiable)

- **No inline secrets** — `${ENV_VAR}` only, never `password: mysecret`
- **Read-only config mounts** — always `:ro` for config files that the app only reads
- **`cap_drop: [ALL]`** — drop all Linux capabilities; add back only what's explicitly needed
- **Never `privileged: true`** — if the user insists, warn with the security risk and require explicit confirmation
- **Non-root user** — `user: "1000:1000"` for app services where the image supports it
- **Internal networks** — services that communicate only internally must NOT have `ports:` exposed to host
- **Log limits** — always add `logging` block to prevent disk fill from container logs

---

## Homelab / Multi-App Stacks

When user asks for a "full homelab setup", "all my self-hosted apps in one compose", or similar:

1. Ask which categories they want: file cloud, media, monitoring, git, CI/CD, etc.
2. Generate a single `docker-compose.yml` with all requested apps + shared infrastructure (Traefik as reverse proxy, shared PostgreSQL, shared Redis)
3. Use Traefik labels for routing instead of port mappings where possible
4. Group services with comments (`# ── Media ──`, `# ── Storage ──`, etc.)
