---
name: sw-boilerplate
description: Use when user wants to scaffold a complete project boilerplate, says "create boilerplate", "set up the project", "generate the skeleton", or wants all project files created from requirements/tech-stack.yaml directly in the workspace root.
version: 1.0.0
author: Lehnert
---

# SW Boilerplate

## Overview

Orchestrates all boilerplate sub-skills in sequence based on `requirements/tech-stack.yaml`. Writes every file directly into the workspace root. Completely silent – no code output, no questions, no file lists. Prints sub-skill progress lines and exactly two final lines.

---

## Required Input

`requirements/tech-stack.yaml` must exist. If missing, stop:
> `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

---

## Execution Order

Read `requirements/tech-stack.yaml` fully, then execute the following modules in order. Each module runs silently and prints its completion line before the next starts.

### Module 1 – Root (always runs)

Apply the full behavior of `sw-boilerplate-root`:
- Creates `package.json`, `tsconfig.json`, `pom.xml` or `build.gradle.kts`, `pyproject.toml`, `go.mod`, `.gitignore`, `.env.example`, and all other root config files appropriate to the detected stack.
- At completion, print: `✅ sw-boilerplate-root completed`

### Module 2 – Structure (always runs)

Apply the full behavior of `sw-boilerplate-structure`:
- Creates entry points and source skeleton: `src/main.ts`, `app/layout.tsx`, `Application.java`, `main.py`, `cmd/main.go`, and all supporting module/controller/service stubs.
- At completion, print: `✅ sw-boilerplate-structure completed`

### Module 3 – DB (run if `database.primary` is not "None" and not absent)

Apply the full behavior of `sw-boilerplate-db`:
- Creates `prisma/schema.prisma`, `V1__init.sql`, `database.py`, ORM config, and migration foundation based on the detected ORM.
- At completion, print: `✅ sw-boilerplate-db completed`

### Module 4 – Docker (run if `docker.services` array is non-empty)

Apply the full behavior of `sw-boilerplate-docker`:
- Creates `docker-compose.yml`, `Dockerfile`, `Dockerfile.frontend`, `.dockerignore`, and `traefik.yml` if applicable.
- At completion, print: `✅ sw-boilerplate-docker completed`

### Module 5 – UI (run if `frontend.framework` is set and not "None")

Apply the full behavior of `sw-boilerplate-ui`:
- Creates `tailwind.config.ts`, `globals.css`, `components.json` (shadcn), `lib/utils.ts`, or React/Vue app files based on the detected UI library.
- At completion, print: `✅ sw-boilerplate-ui completed`

---

## Sub-skill File Reference

Each module above follows the exact file generation rules defined in its own SKILL.md. Use those rules fully – do not skip any files listed there.

| Module | Skill |
|--------|-------|
| Module 1 | sw-boilerplate-root |
| Module 2 | sw-boilerplate-structure |
| Module 3 | sw-boilerplate-db |
| Module 4 | sw-boilerplate-docker |
| Module 5 | sw-boilerplate-ui |

---

## Output Rules

- **Never output any code, file content, directory tree, or file list in the chat.**
- No questions, no design presentations, no stack summaries.
- The only chat output allowed is the per-module completion lines and the two final lines.

---

## Final Output

After all modules complete, print exactly two lines:

```
✅ Boilerplate created in root
To test: <dynamic command>
```

Dynamic command from tech-stack.yaml:

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
