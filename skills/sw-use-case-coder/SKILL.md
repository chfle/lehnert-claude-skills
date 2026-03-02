---
name: sw-use-case-coder
description: Use when user wants to generate code for a use case or user story, says "code UC-01", "implement US-03", "generate all MVP", "scaffold UC-05 UC-07", or wants production-ready files from use-cases.md and tech-stack.yaml written directly into the project root.
version: 2.8.0
author: Lehnert
---

# SW Use Case Coder

## Overview

Reads `requirements/tech-stack.yaml` and `requirements/use-cases.md`, then writes or updates production-ready code **directly into the workspace root** using real project paths. Works for any language and framework. Iterative – calling it again for the same ID refines and improves existing code. Completely silent except for the three final lines.

**Language detection:** Read the user's input language and respond entirely in that language. Default to German if undetectable.

---

## Safety: Working Directory

Before creating or editing any file, verify the current working directory is the workspace **root** (the directory containing `package.json`, `pom.xml`, `src/`, `app/`, etc.).

If the working directory is inside `requirements/` or any subdirectory, immediately navigate to the workspace root before proceeding. Never write files relative to `requirements/`.

---

## Required Input – Check in Order

### 0. Boilerplate check (first, always)

Check the workspace root for at least one of:
`package.json`, `pom.xml`, `build.gradle`, `build.gradle.kts`, `Makefile`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `composer.json`

If NONE exist → stop immediately, output exactly:

```
❌ No project boilerplate found in root.
Please run /sw-boilerplate first to create the full project structure.
Then try this command again.
```

### 1. Requirements check

- `requirements/tech-stack.yaml` missing → stop:
  > `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

- `requirements/use-cases.md` missing → stop:
  > `requirements/use-cases.md` not found. Run `/sw-use-case-creator` first.

- Also read `requirements/user-stories.md` as optional context if it exists.

---

## Use Case Selection

Accept **any ID exactly as the user typed it** – never normalize, pad, or change it.

| Input example | Meaning |
|---------------|---------|
| `UC-01`, `UC-1`, `UC-001` | Single use case by exact ID |
| `US-03`, `US-11` | Single user story by exact ID |
| `UC-01 UC-04`, `US-01 US-03` | Multiple items, space-separated |
| `all MVP` | Every item tagged MVP or `[MVP]` |
| `all Nice-to-have` | Every item tagged Nice-to-have |
| `all` | Every item in the source files |
| *(no argument)* | Ask: "Which items should I implement? (e.g. UC-01, US-03, all MVP, all)" |

Look up each ID in both `requirements/use-cases.md` and `requirements/user-stories.md`.

---

## Code Generation Rules

Read `requirements/tech-stack.yaml` fully. Adapt every file to the detected language, framework, ORM, and test tooling. Never generate files for frameworks not in the stack.

### Monorepo vs Single-App Paths

Before generating any file path, read `project.structure` from `requirements/tech-stack.yaml`:

| Value | Path prefix rule |
|-------|-----------------|
| `single-app` (or field absent) | Use paths as-is from the table below |
| `monorepo` | Prefix frontend files with `apps/web/`, backend files with `apps/api/` |

Example — Next.js in monorepo: `app/devices/page.tsx` → `apps/web/app/devices/page.tsx`
Example — NestJS in monorepo: `src/devices/devices.service.ts` → `apps/api/src/devices/devices.service.ts`

### Generic path conventions

| Stack | Real paths to write |
|-------|---------------------|
| Next.js App Router | `app/(feature)/page.tsx`, `app/(feature)/<Feature>Form.tsx`, `app/(feature)/actions.ts`, `lib/types/<feature>.ts`, `components/<Feature>.tsx` |
| Next.js Pages Router | `pages/<feature>/index.tsx`, `components/<Feature>Form.tsx`, `lib/<feature>.ts` |
| NestJS | `src/<feature>/<feature>.controller.ts`, `src/<feature>/<feature>.service.ts`, `src/<feature>/<feature>.module.ts`, `src/<feature>/dto/create-<feature>.dto.ts` |
| Spring Boot | `src/main/java/<pkg>/<feature>/<Feature>Controller.java`, `<Feature>Service.java`, `<Feature>Repository.java`, `<Feature>Dto.java` |
| FastAPI | `routers/<feature>.py`, `schemas/<feature>.py`, `services/<feature>.py` |
| Go | `internal/handler/<feature>.go`, `internal/service/<feature>.go`, `internal/repository/<feature>.go` |
| Rust (Axum) | `src/handlers/<feature>.rs`, `src/models/<feature>.rs`, `src/routes.rs` (update) |
| Prisma | Append model to `prisma/schema.prisma` |
| Flyway | `src/main/resources/db/migration/V<n>__add_<feature>.sql` |
| SQLAlchemy | `models/<feature>.py`, `alembic/versions/<n>_add_<feature>.py` |
| GORM | `internal/models/<feature>.go` |

### Quality rules

- Use exact language from `frontend.language` / `backend.language` in tech-stack.yaml
- Use `ui_library` value (shadcn/ui, Tailwind, MUI) for component styling
- Add `'use client'` in Next.js only when component uses state, effects, or event handlers
- DTOs and schemas contain the exact fields from the use case main flow
- **No hardcoded secrets** — use the environment variable pattern for the detected language:

| Language | Pattern |
|----------|---------|
| TypeScript / JS | `process.env.VAR_NAME` |
| Python | `os.environ["VAR_NAME"]` or `os.getenv("VAR_NAME")` |
| Java (Spring) | `@Value("${VAR_NAME}")` |
| Go | `os.Getenv("VAR_NAME")` |
| Rust | `std::env::var("VAR_NAME").expect("VAR_NAME not set")` |
| PHP | `$_ENV["VAR_NAME"]` |

- **Import conventions per framework**: `@/` alias for Next.js; relative imports within module for NestJS; absolute package path from `go.mod` for Go; standard Java package imports for Spring; absolute from project root for FastAPI

- **ORM model table naming** — use the convention for the detected ORM:

| ORM | Table mapping |
|-----|--------------|
| Prisma | `@@map("table_name")` + all required `@relation` fields |
| SQLAlchemy | `__tablename__ = "table_name"` |
| GORM | Model name auto-maps to snake_case plural; use `gorm:"column:col_name"` for custom field names |
| Spring JPA / Hibernate | `@Table(name = "table_name")` on entity class |
| TypeORM | `@Entity("table_name")` on entity class |
| Eloquent (Laravel) | `protected $table = "table_name"` |

- **Acceptance criteria** from use-cases.md become TODO comments in E2E test files using the language-appropriate comment syntax (`// TODO: AC-X:` for C-family languages, `# TODO: AC-X:` for Python/Ruby/Shell); for unit/integration tests use them to guide mock setup and assertions — do not repeat them as literal comments
- **Iterative**: if a file already exists, edit and improve it — add or enhance functions rather than replacing the entire file. For test files, always regenerate with all acceptance criteria covered. Never delete existing working logic.
- **Migrations are additive** — each use case appends only new migration files; never modify or delete existing ones. If a conflict is detected, stop and report it.

| ORM | New migration pattern | Post-write command to show user |
|-----|----------------------|---------------------------------|
| Prisma | Append model to `prisma/schema.prisma` | `npx prisma migrate dev --name add_<feature>` |
| Flyway | New `V<n+1>__add_<feature>.sql` in `src/main/resources/db/migration/` | Applied automatically on startup |
| Alembic | New `alembic/versions/<n>_add_<feature>.py` | `alembic upgrade head` |
| GORM | Add struct to `internal/models/`; register in AutoMigrate call | `db.AutoMigrate(&Feature{})` |
| TypeORM | Generate via `typeorm migration:generate` | `typeorm migration:run` |
| Liquibase | New changeset in `db/changelog/` | Applied automatically on startup |

### Tests to generate

| Type | Condition | Generate when |
|------|-----------|---------------|
| Unit | Always | For every use case |
| Integration (Testcontainers) | `testcontainers: true` in tech-stack.yaml | DB-level integration tests |
| E2E | `e2e` key present | Full flow from UI to DB |
| API / Contract | `api` key present | HTTP endpoint tests |

**Test path by language** — use the convention for the detected backend/frontend language:

| Language / Framework | Unit test | Integration test | E2E test |
|----------------------|-----------|-----------------|---------|
| TypeScript (Jest/Vitest) | `src/<f>/<f>.service.spec.ts` | `src/<f>/<f>.integration.spec.ts` | `e2e/<f>.spec.ts` |
| Python (pytest) | `tests/unit/test_<f>.py` | `tests/integration/test_<f>.py` | `tests/e2e/test_<f>.py` |
| Java (JUnit 5) | `src/test/java/<pkg>/<F>Test.java` | `src/test/java/<pkg>/<F>IT.java` | `src/test/java/<pkg>/<F>E2ETest.java` |
| Go (testing) | `internal/<f>/<f>_test.go` (same package) | `tests/<f>_integration_test.go` | `tests/<f>_e2e_test.go` |
| Rust (cargo test) | Inline `#[cfg(test)]` in `src/<f>.rs` | `tests/<f>_integration.rs` | `tests/<f>_e2e.rs` |
| PHP (PHPUnit) | `tests/Unit/<F>Test.php` | `tests/Feature/<F>Test.php` | `tests/Feature/<F>E2ETest.php` |

Apply the same monorepo prefix to all test paths: `apps/api/src/<feature>/...` for backend tests, `apps/web/...` for frontend tests.

---

## Output Rules (absolute)

- **Never output any code, file content, directory tree, file list, or folder names in the chat.**
- No explanations, no brainstorming, no "I created folder X", no Markdown code blocks.
- Do not create any use-case-specific staging folder (`requirements/code/`, `uc-01-.../`, `generated-/`, etc.).
- All changes go directly into the existing project structure.
- If an error occurs, output only the error message – no partial code.

---

## Final Output

After all files are written, print **exactly three lines** – nothing else.

Single item:
```
✅ [exact user input] implemented in root (X files updated/created)
To test: <dynamic command>
💡 Tip: Run git add . && git commit -m "feat: implement [exact user input]" to allow easy reset on mistakes.
```

Multiple items:
```
✅ [exact user input 1] implemented in root (X files)
✅ [exact user input 2] implemented in root (X files)
To test: <dynamic command>
💡 Tip: Run git add . && git commit -m "feat: implement [exact user input 1] [exact user input 2]" to allow easy reset on mistakes.
```

Batch (`all MVP`, `all`, etc.):
```
✅ all MVP implemented in root (X files updated/created)
To test: <dynamic command>
💡 Tip: Run git add . && git commit -m "feat: implement all MVP" to allow easy reset on mistakes.
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

---

## Example Usage

```
User: /sw-use-case-coder US-01

Skill: (silent)
  → reads requirements/tech-stack.yaml  (Next.js + Prisma + pnpm)
  → reads requirements/user-stories.md  (US-01: Register Device)
  → writes app/devices/new/page.tsx
  → writes app/devices/new/RegisterDeviceForm.tsx
  → writes app/devices/actions.ts
  → appends model Device to prisma/schema.prisma
  → writes src/devices/devices.service.spec.ts
  → writes lib/types/device.ts
  → writes e2e/register-device.spec.ts

Final output:
✅ US-01 implemented in root (7 files updated/created)
To test: pnpm install && pnpm dev
💡 Tip: Run git add . && git commit -m "feat: implement US-01" to allow easy reset on mistakes.
```

---

## Next Skill

After code generation, test and commit your work:

> ▶ **Next steps:**
> - Commit: `git add . && git commit -m "feat: implement [use-case IDs]"`
> - Run the test suite to verify: `pnpm test` / `./mvnw test` / `pytest` (based on your stack)
> - Run `/sw-use-case-coder` again with the next use case to continue building out the application
