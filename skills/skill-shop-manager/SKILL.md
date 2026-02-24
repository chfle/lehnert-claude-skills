---
name: skill-shop-manager
description: Use when user says "skill shop", "liste skills", "meine skills", "zeige skills", "neuen skill erstellen", "skill hinzufügen", "welcher skill für", "skill empfehlen", "skill installieren", "skill catalog", "was kann ich installieren", or wants to browse, manage, or discover personal skills.
---

# Skill Shop Manager

## Overview

Du bist der Verwalter von Lehnerts persönlichem Skill-Shop. Deine Aufgabe: Skills anzeigen, empfehlen und neue Skills auf Befehl anlegen.

**Kernprinzip:** Jeder Skill ist eine Markdown-Datei (`SKILL.md`) in einem eigenen Ordner unter `skills/`. Ein Skill = ein Ordner.

## Skill-Katalog

| # | Skill-Name | Kategorie | Beschreibung | Status |
|---|-----------|-----------|--------------|--------|
| 01 | `skill-shop-manager` | 🏪 Meta | Verwaltet diesen Skill-Shop, listet Skills, empfiehlt und erstellt neue | ✅ Aktiv |
| 02 | `brand-guidelines` | 🎨 Brand & Design | Markenfarben, Tonalität, Schriften und Corporate Identity | 🔲 Geplant |
| 03 | `email-marketing` | 📧 Marketing | Newsletter-Texte, Betreffzeilen, A/B-Varianten | 🔲 Geplant |
| 04 | `blog-writer` | ✍️ Content | SEO-optimierte Blogartikel in Lehnerts Tonalität | 🔲 Geplant |
| 05 | `social-media-manager` | 📱 Marketing | LinkedIn-, Instagram- und Twitter/X-Posts erstellen | 🔲 Geplant |
| 06 | `code-reviewer` | 💻 Dev | Code-Reviews nach definierten Qualitätsstandards | 🔲 Geplant |
| 07 | `sql-analyst` | 📊 Data | SQL-Abfragen, Datenanalysen, Report-Generierung | 🔲 Geplant |
| 08 | `project-planner` | 📋 Business | Projektpläne, Meilensteine, Ressourcenplanung | 🔲 Geplant |
| 09 | `customer-support` | 🤝 Service | Kunden-E-Mails, Beschwerdebehandlung, FAQ-Antworten | 🔲 Geplant |
| 10 | `seo-optimizer` | 🔍 Marketing | Meta-Tags, Keywords, On-Page-SEO, Struktur-Checks | 🔲 Geplant |
| 11 | `meeting-summarizer` | 🗒️ Productivity | Meeting-Protokolle, Action Items, Entscheidungen | 🔲 Geplant |
| 12 | `research-assistant` | 🔬 Research | Marktrecherchen, Wettbewerbsanalysen, Quellen | 🔲 Geplant |
| 13 | `api-documenter` | 💻 Dev | OpenAPI-Specs, Endpoint-Docs, Code-Beispiele | 🔲 Geplant |
| 14 | `sprint-planner` | ⚡ Agile | Sprint-Planung, User Stories, Velocity-Schätzungen | 🔲 Geplant |
| 15 | `user-story-writer` | ⚡ Agile | User Stories, Akzeptanzkriterien, Definition of Done | 🔲 Geplant |
| 16 | `legal-reviewer` | ⚖️ Legal | AGB-Checks, Datenschutz, Vertragsklauseln (kein Rechtsrat) | 🔲 Geplant |
| 17 | `financial-analyst` | 💰 Finance | Budgetpläne, KPI-Dashboards, Kostenanalysen | 🔲 Geplant |
| 18 | `onboarding-guide` | 🧑‍💼 HR | Mitarbeiter-Onboarding, Checklisten, Willkommens-Docs | 🔲 Geplant |
| 19 | `changelog-writer` | 💻 Dev | Release Notes, CHANGELOG.md, Versionshistorien | 🔲 Geplant |
| 20 | `data-visualizer` | 📊 Data | Chart-Empfehlungen, Mermaid-Diagramme, Infografiken | 🔲 Geplant |
| 21 | `presentation-builder` | 🎤 Content | Slide-Strukturen, Executive Summaries, Pitch Decks | 🔲 Geplant |
| 22 | `ai-prompt-engineer` | 🤖 AI | System-Prompts, Prompt-Templates, LLM-Optimierung | 🔲 Geplant |

## Automatische Empfehlungen

Wenn der Nutzer eine Aufgabe nennt, empfiehl den passenden Skill:

| Aufgabe / Thema | Empfohlener Skill |
|-----------------|-------------------|
| „Schreib einen Blogartikel" | → `blog-writer` |
| „Erstelle einen LinkedIn-Post" | → `social-media-manager` |
| „Review meinen Code" | → `code-reviewer` |
| „Analysiere diese Daten" | → `sql-analyst` oder `data-visualizer` |
| „Schreib einen Newsletter" | → `email-marketing` |
| „Plane das nächste Projekt" | → `project-planner` |
| „Fasse das Meeting zusammen" | → `meeting-summarizer` |
| „Optimiere diese Seite für SEO" | → `seo-optimizer` |
| „Erstelle User Stories" | → `user-story-writer` |
| „Schreib Release Notes" | → `changelog-writer` |
| „Neuen Mitarbeiter einarbeiten" | → `onboarding-guide` |

> Skill noch nicht aktiv? Sage: **„Erstelle den Skill [name] für mich"** – ich baue den Ordner und die SKILL.md sofort.

## Neuen Skill hinzufügen

Sage einfach:
> „Erstelle skill für [Thema/Aufgabe]"

Ich lege dann automatisch an:
```
skills/
  dein-skill-name/
    SKILL.md    ← mit YAML-Frontmatter, Trigger-Beschreibung und Vorlage
```

### Manuelle Installation (für Fortgeschrittene)

```bash
# 1. Ordner anlegen
mkdir -p ~/.claude/skills/dein-skill-name

# 2. SKILL.md erstellen mit Mindest-Inhalt:
cat > ~/.claude/skills/dein-skill-name/SKILL.md << 'EOF'
---
name: dein-skill-name
description: Use when [konkrete Trigger-Situation]
---

# Skill-Titel

## Overview
[Was macht dieser Skill?]

## Anleitung
[Wie wende ich ihn an?]
EOF
```

## Installations-Pfade

| Plattform | Pfad |
|-----------|------|
| **Claude Code** | `~/.claude/skills/` |
| **Claude.ai** | Skills-Datei manuell hochladen im Gespräch |
| **Dieses Repository** | `skills/` (Basis für beide) |

## Status-Legende

| Icon | Bedeutung |
|------|-----------|
| ✅ Aktiv | Skill ist fertig und einsatzbereit |
| 🚧 In Arbeit | Skill wird gerade entwickelt |
| 🔲 Geplant | Slot reserviert, noch nicht erstellt |
