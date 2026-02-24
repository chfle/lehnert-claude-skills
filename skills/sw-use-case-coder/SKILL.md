---
name: sw-use-case-coder
description: Use when user wants to generate code for a use case, says "code UC-01", "implement UC-02 UC-03", "generate all MVP code", "scaffold UC-01", or wants production-ready files from use-cases.md and tech-stack.yaml.
version: 1.0.0
author: Lehnert
---

# SW Use Case Coder

## Overview

Reads `requirements/tech-stack.yaml` and `requirements/use-cases.md` to generate production-ready, stack-specific code for selected use cases. All files land in `requirements/code/<uc-slug>/`. Nothing is printed in chat.

**Language detection:** Read the user's input language and respond entirely in that language. Default to German if undetectable.

---

## Required Input – Check in Order

1. **`requirements/tech-stack.yaml` missing** → stop immediately:

   > `requirements/tech-stack.yaml` not found.
   > Run `/sw-tech-stack-planner` first to define your tech stack.

2. **`requirements/use-cases.md` missing** → stop immediately:

   > `requirements/use-cases.md` not found.
   > Run `/sw-use-case-creator` first to define your use cases.

3. **Both exist** → read `requirements/user-stories.md` as optional context for acceptance criteria, then proceed.

---

## Use Case Selection

Parse the argument to determine which use cases to implement:

| Input | Meaning |
|-------|---------|
| `UC-001` | Single use case by ID |
| `UC-001 UC-003 UC-005` | Multiple specific use cases |
| `all MVP` | All UCs marked MVP or with `[MVP]` tag in use-cases.md |
| `all` | Every use case in use-cases.md |
| *(no argument)* | Ask: "Which use cases should I implement? (e.g. UC-001, all MVP, all)" |

---

## Output Folder Naming

Derive the slug from the UC ID and name – lowercase, kebab-case:

| Use Case | Folder |
|----------|--------|
| UC-001: Register Device | `uc-001-register-device` |
| UC-002: Send Alert Notification | `uc-002-send-alert-notification` |
| UC-003: View Dashboard | `uc-003-view-dashboard` |

Full path: `requirements/code/<uc-slug>/`

All files go flat into that folder. The developer copies them to the right place in `src/`.

---

## File Generation Rules

Read `tech-stack.yaml` in full before generating anything. Match every file to the detected stack. Never generate files for frameworks not listed in the stack.

### Frontend Files

| Stack detected | Files to generate |
|----------------|-------------------|
| Next.js App Router | `page.tsx`, `<Feature>Form.tsx` (if flow has input step), `<Feature>List.tsx` (if flow shows a list/table), `<feature>.actions.ts` (server actions) |
| Next.js Pages Router | `<feature>.page.tsx`, `<Feature>Form.tsx` (if form in flow), `<feature>.api.ts` |
| React SPA (Vite/CRA) | `<Feature>Page.tsx`, `<Feature>Form.tsx` (if form), `<feature>.api.ts` |
| Vanilla TypeScript | `<feature>.ts`, `<feature>.html` (if UI step in main flow) |
| None / API-only | Skip frontend files |

Rules:
- Add `'use client'` only when the component uses state, effects, or event handlers
- Server Components are the default in Next.js App Router
- Use the `ui_library` value from tech-stack.yaml (e.g. shadcn/ui, Tailwind CSS, Material UI)

### Backend Files

| Stack detected | Files to generate |
|----------------|-------------------|
| NestJS | `<feature>.controller.ts`, `<feature>.service.ts`, `<feature>.module.ts`, `create-<feature>.dto.ts`, `update-<feature>.dto.ts` (if UC has update flow) |
| Express / Fastify | `<feature>.routes.ts`, `<feature>.controller.ts`, `<feature>.service.ts` |
| Spring Boot | `<Feature>Controller.java`, `<Feature>Service.java`, `<Feature>Repository.java`, `<Feature>Dto.java` |
| FastAPI | `<feature>.router.py`, `<feature>.schema.py`, `<feature>.service.py` |
| Next.js (no separate backend) | `<feature>.actions.ts` (server action) or `route.ts` (API route) – skip separate backend files |
| None | Skip backend files |

### Shared Types

Always generate a types file:

| Language | File |
|----------|------|
| TypeScript | `<feature>.types.ts` |
| Java | `<Feature>Dto.java` (if not already in backend files) |
| Python | `<feature>.schema.py` (if not already in backend files) |

Types must reflect the exact fields from the use case main flow – no generic placeholders.

### Database / ORM

| ORM detected | File to generate |
|-------------|-----------------|
| Prisma | `<feature>.schema.prisma` – complete `model` block(s) to append to `prisma/schema.prisma` |
| TypeORM | `<Feature>.entity.ts` |
| Hibernate / JPA | `<Feature>Entity.java` (if not already in backend files) |
| SQLAlchemy | `<feature>.model.py` |
| jOOQ + Flyway | `V<n>__add_<feature>.sql` – versioned Flyway migration with all required tables |
| None / no DB in UC | Skip |

### Tests

Generate tests that match the `testing` section of tech-stack.yaml:

| Test type | Condition | File |
|-----------|-----------|------|
| Unit test | Always | `<feature>.service.spec.ts` (or `.test.ts` / `ServiceTest.java` / `test_<feature>.py`) |
| Integration (Testcontainers) | `testcontainers: true` in yaml | `<feature>.integration.spec.ts` |
| E2E | `e2e` key present in yaml | `<feature>.e2e.spec.ts` |
| API / Supertest | `api` key present in yaml | `<feature>.api.spec.ts` |

In every test file, add `// TODO: AC-X: <criterion text>` comments above each test case, linked to the acceptance criteria from use-cases.md.

### Docker Snippet

Generate `docker-compose.<uc-slug>.snippet.yml` **only** when the use case requires a new service not already listed in `docker.services` in tech-stack.yaml (e.g., a message queue, a file storage service, a new microservice). Skip this file if no new infrastructure is needed.

---

## Code Quality Rules

- Use the exact language from `frontend.language` / `backend.language` in tech-stack.yaml
- Follow the naming convention of the detected framework:
  - NestJS / React: PascalCase for classes and components, camelCase for methods
  - Spring Boot: PascalCase for classes, camelCase for methods
  - FastAPI / Python: snake_case throughout
- DTOs / schemas must contain the exact fields found in the use case main flow
- Acceptance criteria from use-cases.md become `// TODO: AC-X` comments in test files
- Use realistic field names from the use case – never `fooBar`, `lorem`, or generic placeholder names
- No hardcoded secrets, credentials, or connection strings in any file
- Import paths use `@/` alias (Next.js) or relative imports – match the detected framework convention
- For Prisma: include `@@map("table_name")` and all required relations

---

## Output Rules

- **Never output any code or file content in the chat.**
- Do not show file paths, snippets, diffs, imports, or Markdown code blocks in the chat response.
- The only chat output allowed is the confirmation lines at the end.
- If an error occurs (missing file, ambiguous UC ID), report only the error – no partial code output.

---

## File Output

For each use case:

1. `mkdir -p requirements/code/<uc-slug>`
2. Generate and write all files
3. Print **only** these lines – nothing else:

Single use case:
> ✅ Files created in `requirements/code/<uc-slug>/` (X files)
> Open the folder in your editor or copy to `src/` when ready.

Multiple use cases:
> ✅ Files created in `requirements/code/uc-001-register-device/` (7 files)
> ✅ Files created in `requirements/code/uc-003-view-dashboard/` (5 files)
> Open the folders in your editor or copy to `src/` when ready.

One optional summary sentence max (e.g. "Implemented UC-001 and UC-003 – 12 files total, Prisma schema and Testcontainers integration tests included."). No code, no file listings, no Markdown blocks.

---

## Next Skill

> ▶ **Next steps:**
> - Review the generated files in `requirements/code/` and copy to `src/` when satisfied
> - Run `/sw-diagram-creator` to generate sequence or architecture diagrams from your use cases
