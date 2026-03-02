# /docker-compose-writer

Set up any self-hosted app or Docker stack — generates a complete, production-ready `docker-compose.yml` with run commands.

## Usage

```
/docker-compose-writer <app name, category, or existing compose>
```

## Examples

```
/docker-compose-writer note-taking app
/docker-compose-writer Vaultwarden
/docker-compose-writer I need a database with a web UI
/docker-compose-writer monitoring stack
/docker-compose-writer Next.js + PostgreSQL + Redis + Traefik
/docker-compose-writer [paste existing docker-compose.yml]
```

## Behavior

If the user names a **specific tool** (Gitea, Nextcloud, Jellyfin, etc.) or describes a **custom stack** → generate the full compose immediately.

If the user names a **category** ("database", "note-taking", "monitoring", "file storage", "CI/CD") or describes a **need** ("I want to self-host my files", "I need uptime monitoring") → present 3–4 real options with descriptions, let the user pick a number, then generate the full stack.

After the user picks (or for direct mode), output in this order:
1. Complete `docker-compose.yml` in a fenced yaml block
2. `.env.example` with all required variables
3. Step-by-step setup and run commands (copy-paste ready)
4. Day-to-day management commands (stop, backup, update)
5. Next steps

$ARGUMENTS
