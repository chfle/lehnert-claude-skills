# Example 1 – SW Tech Stack Planner

Both examples show what gets written to `requirements/tech-stack.yaml`.
Nothing below is shown in the chat – only the confirmation lines.

---

## German Example – FreelanceTracker

**User input:** `/sw-tech-stack-planner` ← vision.md + user-stories.md exist

**Chat output (everything the user sees):**
```
✅ File created: requirements/tech-stack.yaml
This is now the official tech stack for all following coding skills.

Empfohlen: Next.js 15 + NestJS + PostgreSQL + Redis in Docker mit Testcontainers.
```

**Contents of requirements/tech-stack.yaml (not shown in chat):**

```yaml
project:
  name: "FreelanceTracker"
  type: "web-app"
  complexity: "medium"

frontend:
  framework: "Next.js 15"
  language: "TypeScript"
  ui_library: "shadcn/ui + Tailwind CSS"
  state_management: "Zustand (client state) + React Query (server state)"
  notes: "App Router, Server Components für alle datenlastigen Seiten, Client Components nur für interaktive Teile"

backend:
  framework: "NestJS"
  language: "TypeScript"
  runtime: "Node.js 22"
  notes: "REST API mit OpenAPI/Swagger; PDF-Generierung via Puppeteer oder pdfmake"

database:
  primary: "PostgreSQL 16"
  cache: "Redis 7"
  orm: "Prisma"
  migrations: "Prisma Migrate"

auth:
  solution: "Auth.js v5 (NextAuth)"
  strategy: "session"
  notes: "Credentials Provider für eigene User-Tabelle; JWT-Sessions im Cookie"

testing:
  unit: "Jest + ts-jest"
  integration: "Testcontainers for Node.js (echte PostgreSQL-Instanz pro Test-Run)"
  e2e: "Playwright"
  api: "Supertest"
  testcontainers: true

deployment:
  strategy: "Docker Compose auf eigenem VPS"
  reverse_proxy: "Traefik v3 mit automatischem Let's Encrypt"
  ci_cd: "GitHub Actions (lint + test + build + deploy)"
  registry: "GitHub Container Registry (ghcr.io)"

docker:
  compose_version: "3.9"
  services:
    - postgres
    - redis
    - backend
    - frontend
    - traefik
  volumes:
    - postgres_data
    - redis_data
  networks:
    - app_network

rationale:
  frontend: "Next.js 15 mit App Router ermöglicht Server-seitiges Rendering für schnelle Ladezeiten und einfache SEO – ideal für ein SaaS-Dashboard."
  backend: "NestJS bietet TypeScript-native Architektur mit Modulen, Guards und Interceptors – gut skalierbar für einen Solo-Entwickler."
  database: "PostgreSQL für relationale Daten (Kunden, Projekte, Rechnungen); Redis für Session-Cache und zukünftige Job-Queues."
  auth: "Auth.js v5 ist framework-nativ für Next.js und erfordert keine externe Abhängigkeit für einen Solo-Freelancer."
  deployment: "Traefik auf eigenem VPS minimiert laufende Kosten (ca. 5-10€/Monat) bei voller Kontrolle und automatischem HTTPS."
```

---

---

## English Example – Browser Extension Summarizer

**User input:** `/sw-tech-stack-planner` ← vision.md exists, no user-stories.md

**Chat output (everything the user sees):**
```
✅ File created: requirements/tech-stack.yaml
This is now the official tech stack for all following coding skills.

Recommended: Vanilla TypeScript extension (MV3) + Vite build + Vitest + Playwright E2E.
```

**Contents of requirements/tech-stack.yaml (not shown in chat):**

```yaml
project:
  name: "Webpage Summarizer Extension"
  type: "browser-extension"
  complexity: "simple"

frontend:
  framework: "Vanilla TypeScript (no framework needed)"
  language: "TypeScript"
  ui_library: "None – minimal popup HTML + CSS"
  state_management: "chrome.storage.local (built-in)"
  notes: "Manifest V3; popup.html + content.ts + background.ts; Vite for bundling"

backend:
  framework: "None (stateless – LLM API called directly from background worker)"
  language: "TypeScript"
  runtime: "Chrome Extension Service Worker"
  notes: "No server needed; API key stored client-side in chrome.storage.local"

database:
  primary: "None"
  cache: "chrome.storage.session (in-memory tab cache)"
  orm: "None"
  migrations: "None"

auth:
  solution: "None – user provides own API key"
  strategy: "api-key"
  notes: "API key entered in settings, stored in chrome.storage.local, never sent to third parties"

testing:
  unit: "Vitest"
  integration: "Vitest + chrome-mock (mocks chrome.* APIs)"
  e2e: "Playwright with chromium extension loading"
  api: "None (mocked in tests)"
  testcontainers: false

deployment:
  strategy: "Chrome Web Store + Firefox Add-ons (AMO)"
  reverse_proxy: "None"
  ci_cd: "GitHub Actions (lint + test + build + zip + upload to stores)"
  registry: "GitHub Releases (zip artifacts)"

docker:
  compose_version: "3.9"
  services: []
  volumes: []
  networks: []
  notes: "No Docker needed – browser extension has no server component"

rationale:
  frontend: "Vanilla TypeScript keeps the extension lightweight (<50kb) and avoids framework overhead for a single-purpose popup UI."
  backend: "No server required – the LLM API is called directly from the background service worker, keeping architecture minimal."
  database: "chrome.storage.session provides zero-config tab-level caching without any persistence concerns."
  auth: "BYOK (bring your own key) model eliminates auth infrastructure and operating costs entirely."
  deployment: "GitHub Actions automates the full pipeline from commit to store submission with signed artifacts."
```
