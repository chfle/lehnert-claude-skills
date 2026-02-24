# Lehnert's Claude Skill Shop 🚀

> Your personal collection of Claude skills – installable for Claude Code and Claude.ai.

---

## What is this?

This shop is a Git repository with **ready-made skills** for Claude. Every skill lives in its own folder as a `SKILL.md` file and can be installed in seconds. The central meta-skill `skill-shop-manager` knows all skills, recommends the right one, and creates new ones on demand.

---

## Installation

### Option A – Claude Code (`~/.claude/skills/`)

```bash
# Clone the repository
git clone https://github.com/your-username/lehnert-claude-skills.git
cd lehnert-claude-skills

# Install a single skill (example: skill-shop-manager)
cp -r skills/skill-shop-manager ~/.claude/skills/

# OR: Install all skills at once
cp -r skills/* ~/.claude/skills/
```

Skills are automatically detected on the next Claude Code start.

### Option B – Claude.ai (File Upload)

1. Open a new conversation on [claude.ai](https://claude.ai)
2. Click the **paperclip icon** (attach file)
3. Upload the desired `SKILL.md` directly
4. Claude reads the skill and applies it immediately

### Option C – Claude Code Plugin Install

```bash
claude plugin install /path/to/lehnert-claude-skills --scope user
```

---

## Skill Overview

| # | Skill | Category | Description | Status |
|---|-------|----------|-------------|--------|
| 01 | `skill-shop-manager` | 🏪 Meta | Manages this shop, lists and recommends skills | ✅ Active |
| 02 | `brand-guidelines` | 🎨 Brand | Brand colors, tone of voice, corporate identity | 🔲 Planned |
| 03 | `email-marketing` | 📧 Marketing | Newsletters, subject lines, A/B variants | 🔲 Planned |
| 04 | `blog-writer` | ✍️ Content | SEO blog articles in Lehnert's tone | 🔲 Planned |
| 05 | `social-media-manager` | 📱 Marketing | LinkedIn, Instagram, Twitter/X posts | 🔲 Planned |
| 06 | `code-reviewer` | 💻 Dev | Code reviews following quality standards | 🔲 Planned |
| 07 | `sql-analyst` | 📊 Data | SQL queries, data analysis, reports | 🔲 Planned |
| 08 | `project-planner` | 📋 Business | Project plans, milestones, resources | 🔲 Planned |
| 09 | `customer-support` | 🤝 Service | Customer emails, complaints, FAQ | 🔲 Planned |
| 10 | `seo-optimizer` | 🔍 Marketing | Meta tags, keywords, on-page SEO | 🔲 Planned |
| 11 | `meeting-summarizer` | 🗒️ Productivity | Minutes, action items, decisions | 🔲 Planned |
| 12 | `research-assistant` | 🔬 Research | Market research, competitive analysis, sources | 🔲 Planned |
| 13 | `api-documenter` | 💻 Dev | OpenAPI specs, endpoint docs | 🔲 Planned |
| 14 | `sprint-planner` | ⚡ Agile | Sprint planning, velocity, backlog | 🔲 Planned |
| 15 | `user-story-writer` | ⚡ Agile | User stories, acceptance criteria | 🔲 Planned |
| 16 | `legal-reviewer` | ⚖️ Legal | ToS checks, privacy (not legal advice) | 🔲 Planned |
| 17 | `financial-analyst` | 💰 Finance | Budgets, KPIs, cost analysis | 🔲 Planned |
| 18 | `onboarding-guide` | 🧑‍💼 HR | Employee onboarding, checklists | 🔲 Planned |
| 19 | `changelog-writer` | 💻 Dev | Release notes, CHANGELOG.md | 🔲 Planned |
| 20 | `data-visualizer` | 📊 Data | Charts, Mermaid diagrams, infographics | 🔲 Planned |
| 21 | `presentation-builder` | 🎤 Content | Slides, pitch decks, executive summaries | 🔲 Planned |
| 22 | `ai-prompt-engineer` | 🤖 AI | System prompts, templates, LLM optimization | 🔲 Planned |

---

## Adding a New Skill

### Step 1 – Create folder and file

```bash
# Choose a kebab-case name, e.g. "my-new-skill"
mkdir -p skills/my-new-skill
```

### Step 2 – Create SKILL.md

```bash
cat > skills/my-new-skill/SKILL.md << 'EOF'
---
name: my-new-skill
description: Use when [specific trigger, e.g. "user asks to write X" or "user mentions Y"]
---

# My New Skill

## Overview
[Short description, 1-2 sentences]

## Instructions
[Step-by-step or table]

## Example
[One concrete, reusable example]
EOF
```

### Step 3 – Update the tables

Add the new skill to the table in `README.md` **and** in `skills/skill-shop-manager/SKILL.md`.

### Step 4 – Commit and push

```bash
git add skills/my-new-skill/
git add README.md skills/skill-shop-manager/SKILL.md
git commit -m "feat: add my-new-skill"
git push
```

### Step 5 – Install

```bash
cp -r skills/my-new-skill ~/.claude/skills/
```

---

## Skill Rules

| Rule | Detail |
|------|--------|
| **File name** | Always exactly `SKILL.md` |
| **Folder name** | kebab-case, letters/numbers/hyphens only |
| **Frontmatter** | Only `name` and `description`, max 1024 characters total |
| **description** | Starts with `Use when …`, describes triggers only – no workflow summary |
| **No README** | No `README.md` inside skill folders |
| **Flat structure** | `skills/skill-name/SKILL.md` – no deeper nesting |

---

## Folder Structure

```
lehnert-claude-skills/
├── .claude-plugin/
│   └── plugin.json
├── README.md
└── skills/
    ├── skill-shop-manager/
    │   └── SKILL.md
    ├── brand-guidelines/        ← to be created
    │   └── SKILL.md
    └── ...
```

---

## Status Legend

| Icon | Meaning |
|------|---------|
| ✅ Active | Finished and ready to use |
| 🚧 In Progress | Currently being developed |
| 🔲 Planned | Slot reserved, not yet created |

---

*Built with Claude Code · Maintained by Lehnert*
