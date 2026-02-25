---
name: sw-boilerplate-root
description: Use when user wants to create root-level project config files (package.json, pom.xml, tsconfig.json, .gitignore etc.) based on requirements/tech-stack.yaml in the workspace root.
version: 1.0.0
author: Lehnert
---

# SW Boilerplate Root

## Overview

Creates all root-level project configuration files directly in the workspace root based on `requirements/tech-stack.yaml`. Silent – nothing is printed in chat except the completion line.

---

## Required Input

`requirements/tech-stack.yaml` must exist. If missing, stop:
> `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

---

## Files to Create

Read `frontend.framework`, `backend.framework`, `backend.language`, and `database.orm` from tech-stack.yaml, then create:

### JavaScript / TypeScript stacks (Next.js, NestJS, React, Vite, Express)

| File | Content |
|------|---------|
| `package.json` | Correct name, scripts, and dependencies for the detected framework(s) and package manager from `deployment` or inferred from stack |
| `tsconfig.json` | Strict TypeScript config matching the framework (Next.js paths, NestJS decorators, etc.) |
| `.eslintrc.js` | ESLint config for the framework |
| `.prettierrc` | Prettier defaults |
| `.env.example` | All env vars referenced in the stack (DB URL, auth secret, ports) with placeholder values |
| `.gitignore` | Node-appropriate ignores + `.env` |

**Next.js additions:** `next.config.ts`

**NestJS additions:** `nest-cli.json`, `tsconfig.build.json`

**Next.js + NestJS monorepo** (if `preferences.monorepo: true`): `pnpm-workspace.yaml` or `turbo.json`

### Java / Spring Boot

| File | Content |
|------|---------|
| `pom.xml` | Spring Boot parent POM, correct dependencies (Web, JPA/jOOQ, Flyway, Security, Testcontainers, Lombok) – Maven only |
| `build.gradle.kts` + `settings.gradle.kts` | Spring Boot Gradle plugin, same dependencies – Gradle only |
| `.gitignore` | Java/Maven/Gradle ignores |
| `src/main/resources/application.yml` | Datasource, server port, JPA settings with `${ENV_VAR}` placeholders |

### Python / FastAPI

| File | Content |
|------|---------|
| `pyproject.toml` | Project metadata, FastAPI, uvicorn, SQLAlchemy/Alembic, pytest dependencies |
| `requirements.txt` | Pinned versions matching pyproject.toml |
| `.env.example` | DB URL, secret key, port |
| `.gitignore` | Python ignores + `.env` |

### Go

| File | Content |
|------|---------|
| `go.mod` | Module path + Go version + direct dependencies |
| `.gitignore` | Go ignores |

---

## Output Rules

- Write all files silently to the workspace root.
- Never output file content, code snippets, or file lists in chat.
- At the end, print exactly one line:

```
✅ sw-boilerplate-root completed
```
