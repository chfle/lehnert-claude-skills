---
name: sw-boilerplate-structure
description: Use when user wants to create the source code skeleton (entry points, module files, app structure) based on requirements/tech-stack.yaml directly in the workspace root.
version: 1.0.0
author: Lehnert
---

# SW Boilerplate Structure

## Overview

Creates the source directory skeleton and entry point files directly in the workspace root based on `requirements/tech-stack.yaml`. Silent – nothing is printed in chat except the completion line.

---

## Required Input

`requirements/tech-stack.yaml` must exist. If missing, stop:
> `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

---

## Files to Create

### Next.js App Router

| Path | Content |
|------|---------|
| `app/layout.tsx` | Root layout with html/body, font setup, metadata export |
| `app/page.tsx` | Minimal home page Server Component |
| `app/globals.css` | Base CSS (Tailwind directives if Tailwind, or CSS reset) |
| `lib/utils.ts` | `cn()` utility if shadcn/ui, else empty barrel |
| `components/.gitkeep` | Placeholder for components directory |
| `public/.gitkeep` | Placeholder |

If `preferences.monorepo: true`: create under `apps/web/` prefix instead of root.

### NestJS

| Path | Content |
|------|---------|
| `src/main.ts` | NestFactory bootstrap with port from env |
| `src/app.module.ts` | Root AppModule with ConfigModule |
| `src/app.controller.ts` | Health check GET / endpoint |
| `src/app.service.ts` | AppService with getHello() |
| `src/app.controller.spec.ts` | Basic controller unit test |

If `preferences.monorepo: true`: create under `apps/api/` prefix instead of root.

### Next.js + NestJS (both present)

Apply both sections above with their respective prefixes when monorepo, or apply Next.js to root and NestJS to `api/` when flat.

### Spring Boot

| Path | Content |
|------|---------|
| `src/main/java/{groupId}/{artifactId}/Application.java` | `@SpringBootApplication` main class |
| `src/main/resources/application.yml` | Server port, datasource, JPA settings |
| `src/main/resources/application-test.yml` | Test profile overrides |
| `src/test/java/{groupId}/{artifactId}/ApplicationTests.java` | Context loads test |

Derive `groupId` and `artifactId` from `project.name` in tech-stack.yaml (snake_case → Java package convention).

### FastAPI

| Path | Content |
|------|---------|
| `main.py` | FastAPI app instance, include_router calls, startup event |
| `routers/__init__.py` | Empty |
| `schemas/__init__.py` | Empty |
| `services/__init__.py` | Empty |
| `models/__init__.py` | Empty |
| `core/config.py` | Settings class using pydantic-settings |
| `core/__init__.py` | Empty |

### Go

| Path | Content |
|------|---------|
| `cmd/main.go` | main() with HTTP server setup |
| `internal/handler/.gitkeep` | Placeholder |
| `internal/service/.gitkeep` | Placeholder |
| `internal/repository/.gitkeep` | Placeholder |

---

## Output Rules

- Write all files silently to the workspace root.
- Never output file content, code snippets, or file lists in chat.
- At the end, print exactly one line:

```
✅ sw-boilerplate-structure completed
```
