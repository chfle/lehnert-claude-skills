# Lehnerts Claude Skill Shop 🚀

> Deine persönliche Sammlung von Claude-Skills – installierbar für Claude Code und Claude.ai.

---

## Was ist das hier?

Dieser Shop ist ein Git-Repository mit **fertigen Skills** für Claude. Jeder Skill steckt in einem eigenen Ordner als `SKILL.md` und kann in Sekunden installiert werden. Der zentrale Meta-Skill `skill-shop-manager` kennt alle Skills, empfiehlt den passenden und erstellt auf Befehl neue.

---

## Installation

### Option A – Claude Code (`~/.claude/skills/`)

```bash
# Repository klonen
git clone https://github.com/dein-username/my-claude-skill-shop.git
cd my-claude-skill-shop

# Einzelnen Skill installieren (Beispiel: skill-shop-manager)
cp -r skills/skill-shop-manager ~/.claude/skills/

# ODER: Alle Skills auf einmal installieren
cp -r skills/* ~/.claude/skills/
```

Skills werden beim nächsten Claude-Code-Start automatisch erkannt.

### Option B – Claude.ai (Datei-Upload)

1. Öffne ein neues Gespräch auf [claude.ai](https://claude.ai)
2. Klicke auf das **Büroklammer-Symbol** (Datei anhängen)
3. Lade die gewünschte `SKILL.md` direkt hoch
4. Claude liest den Skill und wendet ihn sofort an

---

## Skill-Übersicht

| # | Skill | Kategorie | Beschreibung | Status |
|---|-------|-----------|--------------|--------|
| 01 | `skill-shop-manager` | 🏪 Meta | Verwaltet diesen Shop, listet und empfiehlt Skills | ✅ Aktiv |
| 02 | `brand-guidelines` | 🎨 Brand | Markenfarben, Tonalität, Corporate Identity | 🔲 Geplant |
| 03 | `email-marketing` | 📧 Marketing | Newsletter, Betreffzeilen, A/B-Varianten | 🔲 Geplant |
| 04 | `blog-writer` | ✍️ Content | SEO-Blogartikel in Lehnerts Tonalität | 🔲 Geplant |
| 05 | `social-media-manager` | 📱 Marketing | LinkedIn, Instagram, Twitter/X Posts | 🔲 Geplant |
| 06 | `code-reviewer` | 💻 Dev | Code-Reviews nach Qualitätsstandards | 🔲 Geplant |
| 07 | `sql-analyst` | 📊 Data | SQL-Abfragen, Datenanalysen, Reports | 🔲 Geplant |
| 08 | `project-planner` | 📋 Business | Projektpläne, Meilensteine, Ressourcen | 🔲 Geplant |
| 09 | `customer-support` | 🤝 Service | Kunden-E-Mails, Beschwerden, FAQ | 🔲 Geplant |
| 10 | `seo-optimizer` | 🔍 Marketing | Meta-Tags, Keywords, On-Page-SEO | 🔲 Geplant |
| 11 | `meeting-summarizer` | 🗒️ Productivity | Protokolle, Action Items, Entscheidungen | 🔲 Geplant |
| 12 | `research-assistant` | 🔬 Research | Marktrecherche, Wettbewerb, Quellen | 🔲 Geplant |
| 13 | `api-documenter` | 💻 Dev | OpenAPI-Specs, Endpoint-Docs | 🔲 Geplant |
| 14 | `sprint-planner` | ⚡ Agile | Sprint-Planung, Velocity, Backlog | 🔲 Geplant |
| 15 | `user-story-writer` | ⚡ Agile | User Stories, Akzeptanzkriterien | 🔲 Geplant |
| 16 | `legal-reviewer` | ⚖️ Legal | AGB-Checks, Datenschutz (kein Rechtsrat) | 🔲 Geplant |
| 17 | `financial-analyst` | 💰 Finance | Budgets, KPIs, Kostenanalysen | 🔲 Geplant |
| 18 | `onboarding-guide` | 🧑‍💼 HR | Mitarbeiter-Onboarding, Checklisten | 🔲 Geplant |
| 19 | `changelog-writer` | 💻 Dev | Release Notes, CHANGELOG.md | 🔲 Geplant |
| 20 | `data-visualizer` | 📊 Data | Charts, Mermaid-Diagramme, Infografiken | 🔲 Geplant |
| 21 | `presentation-builder` | 🎤 Content | Slides, Pitch Decks, Executive Summaries | 🔲 Geplant |
| 22 | `ai-prompt-engineer` | 🤖 AI | System-Prompts, Templates, LLM-Optimierung | 🔲 Geplant |

---

## Neuen Skill hinzufügen

### Schritt 1 – Ordner und Datei erstellen

```bash
# Kebab-case Name wählen, z.B. "mein-neuer-skill"
mkdir -p skills/mein-neuer-skill
```

### Schritt 2 – SKILL.md anlegen

```bash
cat > skills/mein-neuer-skill/SKILL.md << 'EOF'
---
name: mein-neuer-skill
description: Use when [konkreter Auslöser, z.B. "user asks to write X" or "user mentions Y"]
---

# Mein Neuer Skill

## Overview
[Kurze Beschreibung, 1-2 Sätze]

## Anleitung
[Schritt-für-Schritt oder Tabelle]

## Beispiel
[Ein konkretes, übertragbares Beispiel]
EOF
```

### Schritt 3 – In die Tabelle eintragen

Trage den neuen Skill in die Tabelle in `README.md` **und** in `skills/skill-shop-manager/SKILL.md` ein.

### Schritt 4 – Committen und pushen

```bash
git add skills/mein-neuer-skill/
# README und skill-shop-manager aktualisiert? Dann auch:
git add README.md skills/skill-shop-manager/SKILL.md
git commit -m "feat: add mein-neuer-skill"
git push
```

### Schritt 5 – Installieren

```bash
cp -r skills/mein-neuer-skill ~/.claude/skills/
```

---

## Regeln für Skills

| Regel | Detail |
|-------|--------|
| **Dateiname** | Immer exakt `SKILL.md` |
| **Ordnername** | kebab-case, nur Buchstaben/Zahlen/Bindestriche |
| **Frontmatter** | Nur `name` und `description`, max. 1024 Zeichen gesamt |
| **description** | Beginnt mit `Use when …`, beschreibt NUR Trigger, kein Workflow |
| **Keine README** | Kein `README.md` in Skill-Ordnern |
| **Flache Struktur** | `skills/skill-name/SKILL.md` – keine tieferen Verschachtelungen |

---

## Ordnerstruktur

```
my-claude-skill-shop/
├── README.md
└── skills/
    ├── skill-shop-manager/
    │   └── SKILL.md
    ├── brand-guidelines/        ← noch anzulegen
    │   └── SKILL.md
    └── ...
```

---

## Status-Legende

| Icon | Bedeutung |
|------|-----------|
| ✅ Aktiv | Fertig und einsatzbereit |
| 🚧 In Arbeit | Wird gerade entwickelt |
| 🔲 Geplant | Slot reserviert, noch nicht erstellt |

---

*Erstellt mit Claude Code · Gepflegt von Lehnert*
