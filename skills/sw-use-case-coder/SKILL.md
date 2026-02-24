---
name: sw-use-case-coder
description: Use when user wants to generate code for a use case, says "code UC-01", "implement UC-02 UC-03", "generate all MVP code", "scaffold UC-01", or wants production-ready files from use-cases.md and tech-stack.yaml written directly into the project root.
version: 2.0.0
author: Lehnert
---

# SW Use Case Coder

## Overview

Reads `requirements/tech-stack.yaml` and `requirements/use-cases.md`, then writes production-ready code **directly into the current workspace root** using the correct real project paths. Operates silently – nothing is printed in chat except the final confirmation.

**Language detection:** Read the user's input language and respond entirely in that language. Default to German if undetectable.

---

## Required Input – Check in Order

1. **`requirements/tech-stack.yaml` missing** → stop immediately:

   > `requirements/tech-stack.yaml` not found.
   > Run `/sw-tech-stack-planner` first to define your tech stack.

2. **`requirements/use-cases.md` missing** → stop immediately:

   > `requirements/use-cases.md` not found.
   > Run `/sw-use-case-creator` first to define your use cases.

3. **Both exist** → read `requirements/user-stories.md` as optional context, then proceed to the boilerplate check.

---

## Use Case Selection

Parse the argument:

| Input | Meaning |
|-------|---------|
| `UC-001` | Single use case by ID |
| `UC-001 UC-003` | Multiple specific use cases |
| `all MVP` | All UCs marked MVP or tagged `[MVP]` in use-cases.md |
| `all` | Every use case in use-cases.md |
| *(no argument)* | Ask: "Which use cases should I implement? (e.g. UC-001, all MVP, all)" |

---

## Step 1 – Boilerplate Check

Before generating any code, check the workspace root for a project bootstrap file:

**Detected files:** `package.json`, `pom.xml`, `build.gradle`, `build.gradle.kts`, `Makefile`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `composer.json`

### If a bootstrap file IS found → skip to Step 2.

### If NO bootstrap file is found:

**For JavaScript / TypeScript stacks** (Next.js, NestJS, React, Vite, Express, Fastify), ask exactly:

> No project boilerplate detected in root. Shall I create the full boilerplate now based on tech-stack.yaml? Reply YES to proceed.

**For all other stacks** (Java, Python, Go, Rust, PHP, etc.), ask exactly:

> No project boilerplate detected in root. Which build file should I generate?
> Options based on your stack: [list relevant options from tech-stack.yaml, e.g. pom.xml (Maven), build.gradle (Gradle)]
> Reply with your choice to proceed.

**Wait for the user's reply before doing anything else.**

### After user confirms (YES or chooses a build file):

Silently create the complete boilerplate in the root. What "complete boilerplate" means per stack:

| Stack | Boilerplate files to create |
|-------|-----------------------------|
| Next.js | `package.json`, `tsconfig.json`, `next.config.ts`, `tailwind.config.ts` (if Tailwind), `postcss.config.js` (if Tailwind), `.env.example`, `.gitignore`, `app/layout.tsx`, `app/page.tsx` |
| NestJS | `package.json`, `tsconfig.json`, `tsconfig.build.json`, `nest-cli.json`, `.env.example`, `.gitignore`, `src/main.ts`, `src/app.module.ts`, `src/app.controller.ts`, `src/app.service.ts` |
| Next.js + NestJS (monorepo) | All files above, plus `turbo.json` or `pnpm-workspace.yaml` if monorepo is true in tech-stack.yaml |
| Spring Boot (Maven) | `pom.xml`, `src/main/java/.../Application.java`, `src/main/resources/application.yml`, `.gitignore` |
| Spring Boot (Gradle) | `build.gradle.kts`, `settings.gradle.kts`, `src/main/java/.../Application.java`, `src/main/resources/application.yml`, `.gitignore` |
| FastAPI | `pyproject.toml`, `main.py`, `requirements.txt`, `.env.example`, `.gitignore` |
| Docker-first | `docker-compose.yml` (full, using all services from tech-stack.yaml `docker.services`) plus a `.env.example` |

Always include a `docker-compose.yml` when `docker.services` is non-empty in tech-stack.yaml, regardless of other stack choices.

---

## Step 2 – Generate Use Case Code

Write all files for the selected use cases directly into their correct project paths in the workspace root. Do not create any intermediate staging folder.

### Path conventions per stack

| Stack | Example real paths |
|-------|--------------------|
| Next.js App Router | `app/(feature)/page.tsx`, `app/(feature)/<Feature>Form.tsx`, `app/(feature)/actions.ts`, `lib/types/<feature>.ts` |
| Next.js Pages Router | `pages/<feature>/index.tsx`, `components/<Feature>Form.tsx`, `lib/<feature>.ts` |
| NestJS | `src/<feature>/<feature>.controller.ts`, `src/<feature>/<feature>.service.ts`, `src/<feature>/<feature>.module.ts`, `src/<feature>/dto/create-<feature>.dto.ts` |
| Spring Boot | `src/main/java/<package>/<feature>/<Feature>Controller.java`, `.../service/<Feature>Service.java`, `.../repository/<Feature>Repository.java`, `.../dto/<Feature>Dto.java` |
| FastAPI | `routers/<feature>.py`, `schemas/<feature>.py`, `services/<feature>.py` |
| Prisma | Append model block to `prisma/schema.prisma` |
| Flyway | `src/main/resources/db/migration/V<n>__add_<feature>.sql` |
| Jest / Vitest tests | `src/<feature>/<feature>.service.spec.ts` |
| Testcontainers tests | `src/<feature>/<feature>.integration.spec.ts` |
| JUnit tests | `src/test/java/<package>/<feature>/<Feature>ServiceTest.java` |
| Playwright E2E | `e2e/<feature>.spec.ts` |

### File generation rules

- Read `frontend.language` and `backend.language` from tech-stack.yaml for the exact language
- Use the `ui_library` value (shadcn/ui, Tailwind, Material UI) for component styling
- Add `'use client'` in Next.js only for components with state, effects, or event handlers
- DTOs and schemas must contain the exact fields from the use case main flow
- Acceptance criteria from use-cases.md become `// TODO: AC-X: <text>` comments in test files
- No hardcoded secrets or connection strings – use `process.env.VAR` or equivalent
- Use `@/` alias for Next.js imports; use relative imports for NestJS and other frameworks
- For Prisma: include `@@map("table_name")` and all required relations

### Tests to generate

| Test type | Condition | File location |
|-----------|-----------|---------------|
| Unit test | Always | `src/<feature>/<feature>.service.spec.ts` or equivalent |
| Integration (Testcontainers) | `testcontainers: true` in yaml | `src/<feature>/<feature>.integration.spec.ts` |
| E2E | `e2e` key present in yaml | `e2e/<feature>.spec.ts` |
| API / Supertest | `api` key present in yaml | `src/<feature>/<feature>.api.spec.ts` |

---

## Output Rules

- **Never output any code, file content, directory tree, or file list in the chat.**
- Do not show snippets, diffs, imports, or Markdown code blocks.
- The only chat output allowed is the confirmation lines below.
- If an error occurs, report only the error message – no partial code.

---

## Final Output

After all files are written, print **exactly two lines** – nothing else:

```
✅ UC-001 implemented in root (X files updated/created)
To test: <dynamic command>
```

The dynamic command is determined from tech-stack.yaml:

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
| Docker-first (docker.services non-empty) | `docker compose up` |
| Go | `go run ./cmd/...` |
| Rust | `cargo run` |

When `docker.services` is non-empty in tech-stack.yaml, always prefer `docker compose up` as the primary command. If multiple use cases were implemented, list each on its own line before the "To test" line:

```
✅ UC-001 implemented in root (7 files)
✅ UC-003 implemented in root (5 files)
To test: pnpm install && pnpm dev
```

---

## Next Skill

> ▶ **Next steps:**
> - Run `/sw-diagram-creator` to generate sequence or architecture diagrams from your use cases
