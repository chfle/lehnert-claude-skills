# Example 1 – SW Use Case Coder

Both examples show what gets written to `requirements/code/`. Nothing below is shown in the chat – only the confirmation lines.

---

## German Example – FreelanceTracker (Next.js + NestJS + Prisma)

**Stack:** Next.js 15 App Router + NestJS + PostgreSQL + Prisma + Jest + Testcontainers + Playwright

**User input:** `/sw-use-case-coder UC-001`

**UC-001 from use-cases.md:**
```
## UC-001: Rechnung erstellen

**Actors:** Freelancer
**Preconditions:** Freelancer ist eingeloggt; mindestens ein Kunde existiert
**Postconditions:** Rechnung wurde erstellt und hat Status "Entwurf"

### Main Flow
1. Freelancer öffnet "Neue Rechnung"
2. System zeigt Formular mit Kunde, Leistungen, Datum, Fälligkeit
3. Freelancer füllt alle Pflichtfelder aus
4. Freelancer klickt "Entwurf speichern"
5. System validiert Eingaben und speichert Rechnung

### Acceptance Criteria
- [ ] Linked to US-004: Rechnung enthält Kundendaten, Positionen und Gesamtbetrag
- [ ] Linked to US-005: Status ist "Entwurf" nach dem Erstellen
```

**Chat output (everything the user sees):**
```
✅ Files created in requirements/code/uc-001-rechnung-erstellen/ (10 files)
Open the folder in your editor or copy to src/ when ready.

UC-001 implementiert – 10 Dateien: Next.js-Seite mit Formular, NestJS-Controller und Service, Prisma-Schema, Unit-Test, Testcontainers-Integrationstest und Playwright-E2E-Test.
```

**Contents of requirements/code/uc-001-rechnung-erstellen/ (not shown in chat):**

```
page.tsx                          ← Next.js App Router page (Server Component)
RechnungErstellenForm.tsx         ← Client Component with 'use client'
rechnung.actions.ts               ← Server Action: createRechnung()
rechnung.types.ts                 ← TypeScript interfaces: Rechnung, RechnungPosition, CreateRechnungDto
rechnung.controller.ts            ← NestJS: POST /rechnungen
rechnung.service.ts               ← NestJS service with createRechnung()
rechnung.module.ts                ← NestJS module
create-rechnung.dto.ts            ← DTO with class-validator decorators
rechnung.schema.prisma            ← Prisma model: Rechnung, RechnungPosition
rechnung.service.spec.ts          ← Jest unit test with // TODO: AC-1, AC-2
rechnung.integration.spec.ts      ← Testcontainers: real PostgreSQL in Docker
rechnung.e2e.spec.ts              ← Playwright: fill form → submit → verify status
```

---

## English Example – IoT Device Manager (NestJS API-only + Spring Boot + jOOQ + Flyway)

**Stack:** NestJS + PostgreSQL + jOOQ + Flyway + Jest + Testcontainers

**User input:** `/sw-use-case-coder UC-001 UC-003`

**UC-001:** Register Device | **UC-003:** View Device Status

**Chat output (everything the user sees):**
```
✅ Files created in requirements/code/uc-001-register-device/ (8 files)
✅ Files created in requirements/code/uc-003-view-device-status/ (6 files)
Open the folders in your editor or copy to src/ when ready.

Implemented UC-001 and UC-003 – 14 files total, Flyway migration and Testcontainers integration tests included.
```

**Contents of requirements/code/uc-001-register-device/ (not shown in chat):**

```
DeviceController.java             ← POST /devices endpoint
DeviceService.java                ← registerDevice() business logic
DeviceRepository.java             ← jOOQ queries: insert, findById
DeviceDto.java                    ← Request/response DTO with validation
device.types.ts                   ← (skipped – Java stack, no TS types needed)
V3__add_device_table.sql          ← Flyway migration: CREATE TABLE device (...)
DeviceServiceTest.java            ← JUnit 5 unit test, mocked repository
DeviceIntegrationTest.java        ← Testcontainers: @Testcontainers + @Container PostgreSQL
```

**Contents of requirements/code/uc-003-view-device-status/ (not shown in chat):**

```
DeviceStatusController.java       ← GET /devices/{id}/status
DeviceStatusService.java          ← getDeviceStatus() with last-seen logic
DeviceStatusDto.java              ← Response DTO: id, status, lastSeen, location
V4__add_device_status_column.sql  ← Flyway migration: ALTER TABLE device ADD COLUMN status
DeviceStatusServiceTest.java      ← JUnit 5 unit test
DeviceStatusIntegrationTest.java  ← Testcontainers integration test
```
