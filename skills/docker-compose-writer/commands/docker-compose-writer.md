# /docker-compose-writer

Set up any self-hosted app or Docker stack — generates a complete, production-ready `docker-compose.yml` with health checks, named volumes, and run commands.

## Usage

```
/docker-compose-writer <app name, category, cloud service to replace, or existing compose>
```

## Examples

```
/docker-compose-writer Vaultwarden password manager
/docker-compose-writer replace Google Photos
/docker-compose-writer replace Gmail with self-hosted email
/docker-compose-writer I want to run a local AI like ChatGPT
/docker-compose-writer replace Notion with a self-hosted wiki
/docker-compose-writer Nextcloud with PostgreSQL and Redis
/docker-compose-writer note-taking app
/docker-compose-writer media server with automatic downloads
/docker-compose-writer CI/CD server
/docker-compose-writer RSS reader
/docker-compose-writer ad blocker for my home network
/docker-compose-writer Next.js + PostgreSQL + Redis + Traefik with SSL
/docker-compose-writer I need uptime monitoring for my websites
/docker-compose-writer homelab: git + wiki + monitoring + passwords
/docker-compose-writer [paste existing docker-compose.yml to optimize]
```

## Generates

```
<app-name>/
  docker-compose.yml    ← production-ready, with health checks and named volumes
  .env.example          ← all required environment variables with descriptions
```

## Behavior

**Replace X mode** — if the user says "replace Google X", "I want a self-hosted alternative to Y", or mentions a cloud service → map to the right self-hosted app and generate.

**Direct mode** — if the user names a specific tool (Gitea, Jellyfin, Nextcloud, Immich, Ollama, etc.) or describes a full custom stack → generate immediately without asking.

**Selection mode** — if the user names a category ("note-taking", "monitoring", "CI/CD", "media server") or describes a vague need ("I want to back up my files") → present 3–4 real options with one-line descriptions, let the user pick a number, then generate.

**Optimize mode** — if the user pastes an existing `docker-compose.yml` → audit for issues (missing health checks, root user, no restart policy, pinned versions) and write an improved version.

After the user picks (or in direct mode):
1. Write `docker-compose.yml` and `.env.example` silently to `./<app-name>/`
2. Do NOT print the compose file or .env content in chat
3. Print only:
   - Confirmation: files written to `./<app-name>/`
   - Setup + start commands: `cp .env.example .env`, `docker compose up -d`
   - Useful day-to-day commands: logs, restart, update, backup volume
   - Access URL and default credentials (if applicable)
   - Any first-run setup steps (admin account creation, DNS records, etc.)

$ARGUMENTS
