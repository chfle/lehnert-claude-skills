---
name: docker-compose-writer
description: Use when user wants to write, generate, create, or optimize a docker-compose.yml — for a new project, an existing stack, a specific app description, or when asked to add services like databases, caches, reverse proxies, monitoring, or message queues to an existing compose file.
---

# Docker Compose Writer

## Overview

Generates or optimizes production-ready `docker-compose.yml` files from a project description or an existing compose file. Covers service definitions, networking, volumes, health checks, environment variable management, resource limits, and security hardening — following Docker Compose v3.8+ best practices.

**Language:** Respond in the user's language. YAML keys, comments, and service names default to English.

---

## When to Use

- User describes a project and wants a docker-compose.yml generated
- User has an existing compose file and wants it improved or extended
- User wants to add a service (database, Redis, nginx, Prometheus, etc.) to a stack
- User asks "how do I set up X with Docker Compose?"
- User wants a dev vs. production compose split

## When NOT to Use

- User wants Kubernetes manifests → use a dedicated k8s skill
- User wants a Dockerfile only → generate it directly without this skill
- User wants the full project boilerplate including compose → use `sw-boilerplate-docker`

---

## Clarification Protocol

Ask at most **one** clarifying question before generating. Start with a best-effort compose and note assumptions if the description is clear enough.

| Missing info | Question to ask |
|-------------|----------------|
| Environment target | "Is this for local development, staging, or production?" |
| Database type | "Which database? (PostgreSQL, MySQL, MongoDB, SQLite…)" |
| Existing images vs. Dockerfiles | "Are you using prebuilt images or building from local Dockerfiles?" |
| Reverse proxy needed | "Do you need a reverse proxy (nginx, Traefik) in front of the services?" |
| Secrets management | "Should secrets (passwords, keys) use Docker secrets, env files, or inline env vars?" |

---

## Output Modes

### Mode A — Generate from description
User describes their stack → produce a complete, annotated `docker-compose.yml`.

### Mode B — Optimize existing
User pastes an existing compose file → produce a diff-style review + improved version.

### Mode C — Add a service
User asks to add one service to an existing stack → produce only the new service block + any required network/volume additions.

---

## Quality Standards

Every generated compose file MUST include:

| Element | Implementation |
|---------|----------------|
| Compose version | `version: '3.8'` minimum |
| Named networks | At least one explicit network; never rely on default bridge only |
| Named volumes | All persistent data in named volumes, never bind mounts for DB data in prod |
| Health checks | `healthcheck:` on every service that other services depend on |
| `depends_on` with condition | `condition: service_healthy` not just `service_started` |
| Environment variables | Via `env_file: .env` — never hardcode passwords inline |
| Restart policy | `restart: unless-stopped` for prod; `restart: no` for dev workers |
| Resource limits | `deploy.resources.limits` for memory and CPU on prod configs |
| Image pinning | Specific image tags (e.g. `postgres:16-alpine`) — never `latest` in prod |
| `.env.example` note | Always remind user to create `.env` from `.env.example` |

---

## Service Catalogue

Use these battle-tested patterns when including common services:

### PostgreSQL
```yaml
postgres:
  image: postgres:16-alpine
  restart: unless-stopped
  environment:
    POSTGRES_DB: ${POSTGRES_DB}
    POSTGRES_USER: ${POSTGRES_USER}
    POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
  volumes:
    - postgres_data:/var/lib/postgresql/data
  healthcheck:
    test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
    interval: 10s
    timeout: 5s
    retries: 5
  networks:
    - backend
```

### Redis
```yaml
redis:
  image: redis:7-alpine
  restart: unless-stopped
  command: redis-server --requirepass ${REDIS_PASSWORD} --appendonly yes
  volumes:
    - redis_data:/data
  healthcheck:
    test: ["CMD", "redis-cli", "-a", "${REDIS_PASSWORD}", "ping"]
    interval: 10s
    timeout: 5s
    retries: 5
  networks:
    - backend
```

### Nginx reverse proxy
```yaml
nginx:
  image: nginx:alpine
  restart: unless-stopped
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
    - ./nginx/certs:/etc/nginx/certs:ro
  depends_on:
    app:
      condition: service_healthy
  networks:
    - frontend
    - backend
```

### Traefik (preferred for multi-service setups)
```yaml
traefik:
  image: traefik:v3.0
  restart: unless-stopped
  command:
    - "--providers.docker=true"
    - "--providers.docker.exposedbydefault=false"
    - "--entrypoints.web.address=:80"
    - "--entrypoints.websecure.address=:443"
    - "--certificatesresolvers.letsencrypt.acme.email=${ACME_EMAIL}"
    - "--certificatesresolvers.letsencrypt.acme.storage=/letsencrypt/acme.json"
    - "--certificatesresolvers.letsencrypt.acme.tlschallenge=true"
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock:ro
    - letsencrypt:/letsencrypt
  networks:
    - proxy
```

### MongoDB
```yaml
mongo:
  image: mongo:7
  restart: unless-stopped
  environment:
    MONGO_INITDB_ROOT_USERNAME: ${MONGO_USER}
    MONGO_INITDB_ROOT_PASSWORD: ${MONGO_PASSWORD}
    MONGO_INITDB_DATABASE: ${MONGO_DB}
  volumes:
    - mongo_data:/data/db
  healthcheck:
    test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
    interval: 10s
    timeout: 5s
    retries: 5
  networks:
    - backend
```

### RabbitMQ
```yaml
rabbitmq:
  image: rabbitmq:3-management-alpine
  restart: unless-stopped
  environment:
    RABBITMQ_DEFAULT_USER: ${RABBITMQ_USER}
    RABBITMQ_DEFAULT_PASS: ${RABBITMQ_PASSWORD}
  volumes:
    - rabbitmq_data:/var/lib/rabbitmq
  healthcheck:
    test: ["CMD", "rabbitmq-diagnostics", "ping"]
    interval: 10s
    timeout: 5s
    retries: 5
  networks:
    - backend
```

---

## Security Rules

Apply these to every generated compose file:

- **No plaintext secrets inline** — all passwords/keys via `${ENV_VAR}` from `.env`
- **Read-only mounts where possible** — add `:ro` to config volume mounts
- **Drop capabilities** — add `cap_drop: [ALL]` and only add back what is needed
- **No privileged mode** — never use `privileged: true` unless the user explicitly needs it (and warn them)
- **Non-root users** — add `user: "1000:1000"` for app services where image supports it
- **Limit exposed ports** — only expose ports on `0.0.0.0` that must be public; internal services use networks only

---

## Dev vs. Production Split

When the user needs both environments, generate two files:

**`docker-compose.yml`** — base config (shared services, networks, volumes)
**`docker-compose.override.yml`** — dev overrides (bind mounts, debug ports, hot reload)
**`docker-compose.prod.yml`** — prod overrides (resource limits, TLS, no debug)

Usage:
```bash
# Development (auto-merges override)
docker compose up

# Production
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

---

## Output Format

1. Print the complete `docker-compose.yml` in a fenced `yaml` code block
2. Print the `.env.example` block with all required variables and placeholder values
3. List **assumptions made** if the description was ambiguous
4. Show **quick start commands:**

```bash
cp .env.example .env
# Edit .env with your values
docker compose up -d
docker compose ps        # verify all services healthy
docker compose logs -f   # tail logs
```

5. Suggest next steps

---

## Next Steps (always include)

> **Audit the config:** Use `/linux-config-auditor` to review nginx or any service configs inside the stack
> **Harden the host:** Use `/linux-security-hardener` before exposing this stack to the internet
> **Monitor the stack:** Use `/linux-monitoring-setup` to add Prometheus + Grafana to this compose
> **Automate deployment:** Use `/linux-shell-scriptor` to write a deploy script for this stack
