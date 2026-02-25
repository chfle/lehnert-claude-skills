---
name: sw-boilerplate-root
description: Use when user wants to create root-level project config files (package.json, pom.xml, tsconfig.json, .gitignore etc.) based on requirements/tech-stack.yaml in the workspace root.
version: 1.1.0
author: Lehnert
---

# SW Boilerplate Root

## Overview

Creates or shows root-level project configuration files based on `requirements/tech-stack.yaml`. First asks the user whether to generate files or just show content. Waits for reply before doing anything else.

---

## Required Input

`requirements/tech-stack.yaml` must exist. If missing, stop:
> `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

---

## Step 1 – Always ask first (no exceptions)

Before reading tech-stack.yaml or doing anything else, ask the user **exactly** this question and wait for their reply:

---

Do you want to create the root files (package.json, pom.xml, build.gradle, Makefile, pyproject.toml etc.) yourself?

1. Yes, I will create them myself – show me the exact content based on tech-stack.yaml
2. No, generate them for me
   ⚠️ Warning: Generated library versions might not be the latest. After generation I recommend running pnpm update / ./mvnw versions:use-latest-releases or equivalent.

Reply with 1 or 2 only.

---

**Do not proceed until the user replies.**

---

## Step 2a – If user replies 1 (show content only)

Read tech-stack.yaml. Show the exact file content for each relevant root file based on the detected stack. Do NOT write any files to disk. After showing all content, print nothing else.

Show content for:
- JS/TS stacks: `package.json`, `tsconfig.json`, `.gitignore`, `.env.example`, and framework extras (next.config.ts, nest-cli.json, etc.)
- Spring Boot Maven: `pom.xml`
- Spring Boot Gradle: `build.gradle.kts`, `settings.gradle.kts`
- FastAPI: `pyproject.toml`, `requirements.txt`
- Go: `go.mod`

---

## Step 2b – If user replies 2 (generate files)

Read tech-stack.yaml silently. Write all root files directly to the workspace root. Do not output any file content or file list in chat.

### JavaScript / TypeScript stacks (Next.js, NestJS, React, Vite, Express)

| File | Content |
|------|---------|
| `package.json` | Correct name, scripts, and dependencies for the detected framework(s) and package manager |
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

### After writing all files, print exactly two lines:

```
✅ Root boilerplate files created in root
⚠️ Warning: Library versions might not be the latest. Run pnpm update (or equivalent) to update them.
```
