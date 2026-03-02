---
name: sw-boilerplate-docker
description: Use when user wants to create Docker infrastructure files (docker-compose.yml, Dockerfiles, .dockerignore, Traefik config) based on requirements/tech-stack.yaml directly in the workspace root.
version: 1.1.0
author: Lehnert
---

# SW Boilerplate Docker

## Overview

Creates all Docker infrastructure files directly in the workspace root based on `requirements/tech-stack.yaml`. Silent – nothing is printed in chat except the completion line.

If `docker.services` is empty in tech-stack.yaml, this skill does nothing and prints the completion line.

---

## Required Input

`requirements/tech-stack.yaml` must exist. If missing, stop:
> `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

---

## Files to Create

Read `docker.services`, `docker.volumes`, `docker.networks`, `deployment.reverse_proxy`, `frontend.framework`, `backend.framework`, and `database.primary` from tech-stack.yaml.

### docker-compose.yml (always, if docker.services is non-empty)

Build a complete `docker-compose.yml` using the exact `compose_version` from tech-stack.yaml:

- One service block per entry in `docker.services`
- Standard service configs per service type:

| Service | Config |
|---------|--------|
| `postgres` | Image `postgres:16-alpine`, env vars from `.env`, named volume, health check |
| `mysql` / `mariadb` | Image `mysql:8` / `mariadb:11`, env vars, named volume, health check |
| `redis` | Image `redis:7-alpine`, named volume, healthcheck (`redis-cli --raw ping`) |
| `backend` (NestJS/FastAPI/Go) | Build from `Dockerfile`, depends_on db, env_file `.env` |
| `frontend` (Next.js) | Build from `Dockerfile.frontend`, depends_on backend, ports |
| `traefik` | Image `traefik:v3`, ports 80/443, volume for socket + certs |

Include all named volumes from `docker.volumes` and the network from `docker.networks`.

### Dockerfiles

**`Dockerfile`** (backend) – create when a backend framework is in the stack:

| Backend | Base image / pattern |
|---------|---------------------|
| NestJS | `node:22-alpine` multi-stage: deps → build → production |
| FastAPI | `python:3.13-slim` with uvicorn |
| Spring Boot | `eclipse-temurin:21-jre-alpine` with fat jar |
| Go | `golang:1.23-alpine` multi-stage build → distroless |

**`Dockerfile.frontend`** – create when a frontend framework is in the stack:

| Frontend | Pattern |
|----------|---------|
| Next.js | `node:22-alpine` multi-stage: deps → builder → runner (standalone output) |
| React SPA (Vite) | `node:22-alpine` build stage → `nginx:alpine` serve stage |

### .dockerignore

Create `.dockerignore` in root:
- Always: `node_modules`, `.env`, `.git`, `dist`, `build`, `coverage`, `*.log`
- Java: `target/`, `.gradle/`, `build/` (Gradle)
- Python: `__pycache__`, `*.pyc`, `.venv`, `.pytest_cache`

### Traefik config

If `deployment.reverse_proxy` contains "Traefik":

| Path | Content |
|------|---------|
| `traefik.yml` | Static Traefik config: entryPoints (web 80, websecure 443), Let's Encrypt resolver, Docker provider |
| `dynamic/` | Empty directory placeholder for dynamic config |

**Traefik routing labels for exposed services** — add to every service that should be reachable via Traefik (backend, frontend):

```yaml
labels:
  traefik.enable: "true"
  traefik.http.routers.backend.rule: "Host(`api.${DOMAIN}`)"
  traefik.http.routers.backend.entrypoints: "websecure"
  traefik.http.routers.backend.tls.certresolver: "letsencrypt"
  traefik.http.services.backend.loadbalancer.server.port: "3000"
```

Database services (postgres, redis) must NOT have `traefik.enable: "true"` — they should never be publicly routed.

---

## Output Rules

- Write all files silently to the workspace root.
- Never output file content, code snippets, or file lists in chat.
- At the end, print exactly one line:

```
✅ sw-boilerplate-docker completed
```
