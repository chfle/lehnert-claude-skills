---
name: skill-shop-manager
description: Use when user says "skill shop", "list skills", "my skills", "show skills", "create new skill", "add skill", "which skill for", "recommend a skill", "install skill", "skill catalog", "what can I install", or wants to browse, manage, or discover personal skills.
---

# Skill Shop Manager

## Overview

You are the manager of Lehnert's personal skill shop. Your job: display skills, recommend the right one, and create new skills on demand.

**Core principle:** Every skill is a Markdown file (`SKILL.md`) inside its own folder under `skills/`. One skill = one folder.

## Skill Catalog

| # | Skill Name | Category | Description | Status |
|---|-----------|----------|-------------|--------|
| 01 | `skill-shop-manager` | 🏪 Meta | Manages this skill shop, lists skills, recommends and creates new ones | ✅ Active |
| 02 | `brand-guidelines` | 🎨 Brand & Design | Brand colors, tone of voice, typography, and corporate identity | 🔲 Planned |
| 03 | `email-marketing` | 📧 Marketing | Newsletter copy, subject lines, A/B variants | 🔲 Planned |
| 04 | `blog-writer` | ✍️ Content | SEO-optimized blog articles in Lehnert's tone | 🔲 Planned |
| 05 | `social-media-manager` | 📱 Marketing | LinkedIn, Instagram, and Twitter/X posts | 🔲 Planned |
| 06 | `code-reviewer` | 💻 Dev | Code reviews following defined quality standards | 🔲 Planned |
| 07 | `sql-analyst` | 📊 Data | SQL queries, data analysis, report generation | 🔲 Planned |
| 08 | `project-planner` | 📋 Business | Project plans, milestones, resource planning | 🔲 Planned |
| 09 | `customer-support` | 🤝 Service | Customer emails, complaint handling, FAQ responses | 🔲 Planned |
| 10 | `seo-optimizer` | 🔍 Marketing | Meta tags, keywords, on-page SEO, structure checks | 🔲 Planned |
| 11 | `meeting-summarizer` | 🗒️ Productivity | Meeting minutes, action items, decisions | 🔲 Planned |
| 12 | `research-assistant` | 🔬 Research | Market research, competitive analysis, sources | 🔲 Planned |
| 13 | `api-documenter` | 💻 Dev | OpenAPI specs, endpoint docs, code examples | 🔲 Planned |
| 14 | `sprint-planner` | ⚡ Agile | Sprint planning, velocity estimates, backlog | 🔲 Planned |
| 15 | `user-story-writer` | ⚡ Agile | User stories, acceptance criteria, definition of done | 🔲 Planned |
| 16 | `legal-reviewer` | ⚖️ Legal | ToS checks, privacy, contract clauses (not legal advice) | 🔲 Planned |
| 17 | `financial-analyst` | 💰 Finance | Budget plans, KPI dashboards, cost analysis | 🔲 Planned |
| 18 | `onboarding-guide` | 🧑‍💼 HR | Employee onboarding, checklists, welcome docs | 🔲 Planned |
| 19 | `changelog-writer` | 💻 Dev | Release notes, CHANGELOG.md, version history | 🔲 Planned |
| 20 | `data-visualizer` | 📊 Data | Chart recommendations, Mermaid diagrams, infographics | 🔲 Planned |
| 21 | `presentation-builder` | 🎤 Content | Slide structures, executive summaries, pitch decks | 🔲 Planned |
| 22 | `ai-prompt-engineer` | 🤖 AI | System prompts, prompt templates, LLM optimization | 🔲 Planned |

## Automatic Recommendations

When the user describes a task, recommend the matching skill:

| Task / Topic | Recommended Skill |
|--------------|-------------------|
| "Write a blog post" | → `blog-writer` |
| "Create a LinkedIn post" | → `social-media-manager` |
| "Review my code" | → `code-reviewer` |
| "Analyze this data" | → `sql-analyst` or `data-visualizer` |
| "Write a newsletter" | → `email-marketing` |
| "Plan the next project" | → `project-planner` |
| "Summarize the meeting" | → `meeting-summarizer` |
| "Optimize this page for SEO" | → `seo-optimizer` |
| "Create user stories" | → `user-story-writer` |
| "Write release notes" | → `changelog-writer` |
| "Onboard a new employee" | → `onboarding-guide` |

> Skill not active yet? Say: **"Create the skill [name] for me"** – I'll build the folder and SKILL.md right away.

## Adding a New Skill

Just say:
> "Create skill for [topic/task]"

I will automatically create:
```
skills/
  your-skill-name/
    SKILL.md    ← with YAML frontmatter, trigger description, and template
```

### Manual Installation (advanced)

```bash
# 1. Create folder
mkdir -p ~/.claude/skills/your-skill-name

# 2. Create SKILL.md with minimum content:
cat > ~/.claude/skills/your-skill-name/SKILL.md << 'EOF'
---
name: your-skill-name
description: Use when [specific trigger situation]
---

# Skill Title

## Overview
[What does this skill do?]

## Instructions
[Step-by-step or table]
EOF
```

## Installation Paths

| Platform | Path |
|----------|------|
| **Claude Code** | `~/.claude/skills/` |
| **Claude.ai** | Upload the SKILL.md file manually in the chat |
| **This repository** | `skills/` (source for both) |

## Status Legend

| Icon | Meaning |
|------|---------|
| ✅ Active | Skill is finished and ready to use |
| 🚧 In Progress | Skill is currently being developed |
| 🔲 Planned | Slot reserved, not yet created |
