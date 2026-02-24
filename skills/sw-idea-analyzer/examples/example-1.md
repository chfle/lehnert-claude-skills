# Example 1 – SW Idea Analyzer

---

## German Example

**User input:**
> Ich habe eine Idee für eine App: Freelancer können ihre Arbeitszeiten tracken, automatisch Rechnungen generieren und den Status (bezahlt/offen) im Blick behalten. Alles in einer App, ohne Abo.

**Skill output:**

---

### Idea Summary
Eine All-in-One-App für Freelancer, die Zeiterfassung, automatische Rechnungsstellung und Zahlungsstatus in einer einzigen, abo-freien Anwendung vereint.

### Zielgruppe

| Segment | Beschreibung | Hauptproblem |
|---------|-------------|--------------|
| Primär | Solo-Freelancer (Design, Dev, Beratung) | Zu viele Tools, manueller Aufwand für Rechnungen |
| Sekundär | Kleine Agenturen (2–5 Personen) | Keine gemeinsame Übersicht über offene Posten |

### Problem & Solution Fit
- **Problem:** Freelancer jonglieren zwischen Timetracking-Tool, Rechnungssoftware und Excel – jede Übergabe kostet Zeit und ist fehleranfällig.
- **Lösung:** Ein einziger Flow: Zeit tracken → Rechnung generieren → Status verfolgen.
- **Differenzierung:** Kein Abo (einmaliger Kauf oder kostenlos mit optionalem Pro), kein Overhead durch Buchhaltungsfunktionen.

### Core Features (MVP)

| # | Feature | Warum Core |
|---|---------|------------|
| 1 | Zeiterfassung (Start/Stop pro Projekt/Kunde) | Kern des Workflows |
| 2 | Rechnungsgenerator (PDF, aus Zeiteinträgen) | Hauptnutzen |
| 3 | Kundenverwaltung (Name, Stundensatz, Adresse) | Voraussetzung für Rechnungen |
| 4 | Zahlungsstatus (offen / bezahlt / überfällig) | Übersicht ohne externe Tabellen |
| 5 | Exportfunktion (PDF, CSV) | Steuerberater-Kompatibilität |

**Bewusst aus dem MVP ausgeschlossen:** Buchhaltungsintegration (DATEV, Lexoffice), Teamfunktionen, Wiederkehrende Rechnungen, Mobile App (zunächst Web-first).

### Technische Komplexität: 🟢 Simple

| Dimension | Bewertung | Notizen |
|-----------|-----------|---------|
| Auth & Berechtigungen | 🟢 | Nur Single-User im MVP |
| Externe Integrationen | 🟢 | Keine im MVP |
| Echtzeit | 🟢 | Kein Echtzeit-Bedarf |
| AI / ML | 🟢 | Keine |
| Datenvolumen | 🟢 | Minimal |
| Mobile / Cross-Platform | 🟡 | Web-first, später PWA |

### Markt & Wettbewerb

| Konkurrent | Schwäche | Dein Winkel |
|------------|----------|-------------|
| Toggl + Invoicely | Zwei separate Tools, beide Abo | Alles in einem, kein Abo |
| Harvest | Teuer ($12/Monat), überladen | Schlank, einmaliger Kauf |
| Excel / Notion | Manuell, keine PDF-Generierung | Automatisiert, professionell |

### Top Risiken

| Risiko | Wahrscheinlichkeit | Impact | Mitigation |
|--------|-------------------|--------|------------|
| Markt ist bereits gesättigt | Hoch | Mittel | Starke Positionierung: kein Abo, kein Bloat |
| Steuerrechtliche Anforderungen je Land | Mittel | Hoch | Zunächst nur D/A/CH, klarer Disclaimer |
| Nutzer wechseln nicht von bestehenden Tools | Mittel | Hoch | Kostenloser Import aus Toggl/CSV |

### Next Steps
1. Mockup der 3 Hauptscreens in Figma (Zeiterfassung, Rechnungsübersicht, Rechnungseditor)
2. 5 Freelancer-Interviews: Welche Tools nutzen sie heute, was nervt?
3. Tech-Stack entscheiden: Web-App (Next.js + Supabase empfohlen) vs. Electron
4. Landing Page bauen und E-Mail-Liste aufbauen vor dem Launch
5. MVP in 4-Wochen-Sprints planen

---

> **Suggested next skill:** Features sind klar – nutze **`user-story-writer`** um daraus actionable User Stories zu machen, oder **`project-planner`** für den 3-Monats-Plan.

---
---

## English Example

**User input:**
> I want to build a browser extension that summarizes any webpage in 3 bullet points using AI. One click, no signup required.

**Skill output:**

---

### Idea Summary
A zero-friction browser extension that uses AI to summarize any webpage into three bullet points with a single click – no account needed.

### Target Audience

| Segment | Description | Primary Pain |
|---------|-------------|--------------|
| Primary | Knowledge workers, researchers, students | Information overload, too much to read |
| Secondary | Casual readers, news consumers | Quickly assess whether an article is worth reading |

### Problem & Solution Fit
- **Problem:** Users open dozens of tabs daily and spend time reading content that turns out to be irrelevant or repetitive.
- **Solution:** Instant 3-bullet summary surfaced before the user commits to reading.
- **Differentiation:** No signup, no subscription, one-click UX. Most competitors require accounts and have cluttered interfaces.

### Core Features (MVP)

| # | Feature | Why It's Core |
|---|---------|---------------|
| 1 | One-click summarization of current tab | The entire value proposition |
| 2 | 3-bullet output format | Scannable, consistent, on-brand |
| 3 | Works on articles, docs, blog posts | Core use case coverage |
| 4 | Popup UI in browser toolbar | Standard extension UX pattern |
| 5 | Copy-to-clipboard button | Immediate utility for notes/sharing |

**Explicitly out of MVP scope:** History of summarized pages, custom summary length, PDF support, team sharing, offline mode.

### Technical Complexity: 🟡 Medium

| Dimension | Rating | Notes |
|-----------|--------|-------|
| Auth & permissions | 🟢 | None required |
| External integrations | 🟡 | LLM API call (OpenAI / Claude) required |
| Real-time | 🟢 | Single synchronous API call |
| AI / ML | 🟡 | Prompt engineering needed for quality |
| Data volume | 🟢 | Stateless, nothing stored |
| Cross-platform | 🟡 | Chrome first, then Firefox (MV3 differences) |

### Market & Competition

| Competitor | Weakness | Your Angle |
|------------|----------|------------|
| TLDR This | Requires site visit, not in-browser | Native extension, zero friction |
| Summarize (Chrome ext.) | Requires signup, paid | No account, free tier |
| ChatGPT sidebar | Heavy, context-switching | Lightweight single-purpose tool |

### Top Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| LLM API costs unsustainable at scale | High | High | Rate limit free tier; offer API-key-bring-your-own model |
| Chrome Web Store review delays | Medium | Medium | Submit early, build email list while waiting |
| Content scripts blocked on certain sites | Medium | Low | Graceful error message, fallback to selected text |

### Next Steps
1. Build a prototype in a single `content.js` + `popup.html` – no framework needed
2. Test prompt quality on 20 diverse URLs (news, docs, Reddit, academic)
3. Implement API-key-bring-your-own as the free tier mechanism
4. Submit to Chrome Web Store (allow 3–7 days for review)
5. Post on HackerNews "Show HN" and relevant subreddits on launch day

---

> **Suggested next skill:** Ready to scope the build? Use **`project-planner`** to map out the sprints, or **`user-story-writer`** to write the stories for each feature.
