---
name: sw-boilerplate
description: Use when user wants to scaffold a complete project boilerplate, says "create boilerplate", "set up the project", "generate the skeleton", or wants all project files created from requirements/tech-stack.yaml directly in the workspace root.
version: 1.1.0
author: Lehnert
---

# SW Boilerplate

## Overview

Reads `requirements/tech-stack.yaml` and writes every project file directly into the workspace root. Completely silent – no code output, no questions, no file lists. Prints a progress line after each module and exactly two lines at the end.

**Do NOT invoke any sub-skills or use the Skill tool. Execute all modules inline, in sequence, as described below.**

---

## Required Input

`requirements/tech-stack.yaml` must exist. If missing, stop:
> `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

Read the full file before starting. Note: `frontend.framework`, `backend.framework`, `backend.language`, `database.primary`, `database.orm`, `database.cache`, `docker.services`, `deployment.reverse_proxy`, `frontend.ui_library`.

---

## Module 1 – Root Config (always runs)

Create all root-level configuration files for the detected stack.

**JavaScript / TypeScript** (Next.js, NestJS, React, Vite, Express, Fastify):
- `package.json` – correct name, scripts (`dev`, `build`, `start`, `test`), and dependencies for the detected framework(s) and package manager
- `tsconfig.json` – strict TypeScript config for the framework (Next.js paths, NestJS decorators, etc.)
- `.eslintrc.js` – ESLint config for the framework
- `.prettierrc` – Prettier defaults
- `.env.example` – all env vars referenced by the stack (DB URL, auth secret, ports) with placeholder values
- `.gitignore` – Node ignores + `.env`
- Next.js: also `next.config.ts`
- NestJS: also `nest-cli.json`, `tsconfig.build.json`
- Monorepo (`preferences.monorepo: true`): also `pnpm-workspace.yaml` or `turbo.json`

**Spring Boot:**
- Maven: `pom.xml` with Spring Boot parent + all required dependencies (Web, JPA/jOOQ, Flyway, Security, Testcontainers, Lombok)
- Gradle: `build.gradle.kts` + `settings.gradle.kts` with same dependencies
- `.gitignore` – Java/Maven/Gradle ignores

**Python / FastAPI:**
- `pyproject.toml` – project metadata, FastAPI, uvicorn, SQLAlchemy/Alembic, pytest
- `requirements.txt` – pinned versions
- `.env.example`, `.gitignore`

**Go:**
- `go.mod` – module path + Go version + direct dependencies
- `.gitignore`

After writing all root files, print: `✅ [1/5] Root config files created`

---

## Module 2 – Source Structure (always runs)

Create the source directory skeleton and entry point files.

**Next.js App Router:**
- `app/layout.tsx` – root layout with html/body, font setup, metadata export
- `app/page.tsx` – minimal home page Server Component
- `app/globals.css` – Tailwind directives or CSS reset
- `lib/utils.ts` – `cn()` if shadcn/ui, else empty barrel
- `components/.gitkeep`, `public/.gitkeep`
- Monorepo: use `apps/web/` prefix

**NestJS:**
- `src/main.ts` – NestFactory bootstrap with port from env
- `src/app.module.ts` – root AppModule with ConfigModule
- `src/app.controller.ts` – health check GET /
- `src/app.service.ts` – AppService
- `src/app.controller.spec.ts` – basic unit test
- Monorepo: use `apps/api/` prefix

**Spring Boot** (derive package from `project.name`):
- `src/main/java/{package}/Application.java` – @SpringBootApplication main class
- `src/main/resources/application.yml` – server port, datasource, JPA settings with `${ENV_VAR}` placeholders
- `src/main/resources/application-test.yml` – test profile overrides
- `src/test/java/{package}/ApplicationTests.java` – context loads test

**FastAPI:**
- `main.py` – FastAPI app instance, include_router calls, startup event
- `routers/__init__.py`, `schemas/__init__.py`, `services/__init__.py`, `models/__init__.py`
- `core/config.py` – Settings class using pydantic-settings
- `core/__init__.py`

**Go:**
- `cmd/main.go` – main() with HTTP server setup
- `internal/handler/.gitkeep`, `internal/service/.gitkeep`, `internal/repository/.gitkeep`

After writing all structure files, print: `✅ [2/5] Source structure created`

---

## Module 3 – Database (run only if `database.primary` is set and not "None")

Create database/ORM schema, config, and migration foundation.

**Prisma:**
- `prisma/schema.prisma` – `datasource db` block (provider from stack), `generator client` block, `// Add models here` comment

**TypeORM:**
- `src/database/database.module.ts` – NestJS TypeOrmModule.forRootAsync() with ConfigService
- `src/database/database.config.ts` – TypeORM DataSourceOptions from env vars

**jOOQ + Flyway:**
- `src/main/resources/db/migration/V1__init.sql` – empty migration with header comment

**SQLAlchemy + Alembic:**
- `database.py` – engine + SessionLocal + Base
- `alembic.ini` – config pointing to `alembic/` directory
- `alembic/env.py` – env with target_metadata
- `alembic/versions/.gitkeep`

**GORM:**
- `internal/database/database.go` – GORM db init with DSN from env

**Redis / cache** (if `database.cache` is set):
- NestJS: `src/cache/cache.module.ts` – CacheModule.registerAsync()

After writing all DB files, print: `✅ [3/5] Database files created`

---

## Module 4 – Docker (run only if `docker.services` array is non-empty)

Create Docker infrastructure files.

**`docker-compose.yml`** – always create when this module runs:
- Use `compose_version` from tech-stack.yaml
- One service block per entry in `docker.services`
- Standard configs: postgres (image `postgres:16-alpine`, health check, named volume), redis (`redis:7-alpine`), backend (build Dockerfile, depends_on db), frontend (build Dockerfile.frontend), traefik (image `traefik:v3`, ports 80/443)
- Include all named volumes from `docker.volumes` and network from `docker.networks`

**`Dockerfile`** (backend) – create when backend framework is in stack:
- NestJS: `node:22-alpine` multi-stage (deps → build → production)
- FastAPI: `python:3.13-slim` with uvicorn
- Spring Boot: `eclipse-temurin:21-jre-alpine` with fat jar
- Go: `golang:1.23-alpine` multi-stage → distroless

**`Dockerfile.frontend`** – create when frontend framework is in stack:
- Next.js: `node:22-alpine` multi-stage (deps → builder → runner with standalone output)
- React SPA: `node:22-alpine` build → `nginx:alpine` serve

**`.dockerignore`** – node_modules, .env, .git, dist, build, coverage, *.log; Java: target/, .gradle/; Python: __pycache__, .venv

**Traefik** (if `deployment.reverse_proxy` contains "Traefik"):
- `traefik.yml` – static config: entryPoints (web 80, websecure 443), Let's Encrypt resolver, Docker provider
- `dynamic/.gitkeep`

After writing all Docker files, print: `✅ [4/5] Docker infrastructure created`

---

## Module 5 – UI Foundation (run only if `frontend.framework` is set and not "None")

Create UI foundation files.

**Next.js + Tailwind CSS:**
- `tailwind.config.ts` – content paths for app/**, components/**, lib/**
- `postcss.config.js` – tailwindcss + autoprefixer
- `app/globals.css` – @tailwind base/components/utilities + CSS variables

**Next.js + shadcn/ui** (all Tailwind files above, plus):
- `components.json` – shadcn/ui config (style default, baseColor neutral, cssVariables true)
- `lib/utils.ts` – `cn()` using clsx + tailwind-merge
- `components/ui/.gitkeep`

**Next.js + Material UI:**
- `lib/theme.ts` – MUI createTheme()
- `components/ThemeProvider.tsx` – AppRouterCacheProvider + ThemeProvider wrapper

**React SPA (Vite):**
- `src/index.css` – CSS reset
- `src/App.tsx` – root component
- `src/main.tsx` – ReactDOM.createRoot mount
- If Tailwind: also `tailwind.config.ts`, `postcss.config.js`

After writing all UI files, print: `✅ [5/5] UI foundation created`

---

## Output Rules

- **Never output any code, file content, directory tree, or file list in the chat.**
- No questions, no design presentations, no stack summaries before writing.
- Print only the per-module completion lines above, then the two final lines below.

---

## Final Output

After all modules complete, print exactly two lines:

```
✅ Boilerplate created in root
To test: <dynamic command>
```

| Stack / package manager | Command |
|------------------------|---------|
| Next.js + pnpm | `pnpm install && pnpm dev` |
| Next.js + npm | `npm install && npm run dev` |
| Next.js + yarn | `yarn && yarn dev` |
| NestJS + pnpm | `pnpm install && pnpm start:dev` |
| NestJS + npm | `npm install && npm run start:dev` |
| Spring Boot + Maven | `./mvnw spring-boot:run` |
| Spring Boot + Gradle | `./gradlew bootRun` |
| FastAPI | `uvicorn main:app --reload` |
| Go | `go run ./cmd/...` |
| Rust | `cargo run` |
| Docker-first (docker.services non-empty) | `docker compose up` |

When `docker.services` is non-empty, always prefer `docker compose up`.
