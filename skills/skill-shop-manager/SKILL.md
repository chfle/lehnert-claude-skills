---
name: skill-shop-manager
description: Use when user says "skill shop", "list skills", "my skills", "show skills", "create new skill", "add skill", "which skill for", "recommend a skill", "install skill", "skill catalog", "what can I install", or wants to browse, manage, or discover personal skills.
---

# Skill Shop Manager

## Overview

You are the manager of Lehnert's personal skill shop. Your job: display skills, recommend the right one, and create new skills on demand.

**Core principle:** Every skill is a Markdown file (`SKILL.md`) inside its own folder under `skills/`. One skill = one folder.

## Skill Catalog

### 🗓️ Software Planning

| Skill | Description | Status |
|-------|-------------|--------|
| `sw-idea-analyzer` | Analyzes software ideas for feasibility, features, risks, and MVP scope | ✅ Active |

### 💻 Software Development

| Skill | Description | Status |
|-------|-------------|--------|
| *(no skills yet)* | | |

### 🐧 Linux & Sysadmin

| Skill | Description | Status |
|-------|-------------|--------|
| *(no skills yet)* | | |

### 🔒 Pentesting

| Skill | Description | Status |
|-------|-------------|--------|
| *(no skills yet)* | | |

---

## Automatic Recommendations

| Task / Topic | Recommended Skill |
|--------------|-------------------|
| "Analyze my software idea" | → `sw-idea-analyzer` |
| "Is my app idea worth building?" | → `sw-idea-analyzer` |
| "Help me scope an MVP" | → `sw-idea-analyzer` |
| "Check feasibility of my idea" | → `sw-idea-analyzer` |

> No skill for your task yet? Say: **"Create the skill [name] for me"** – I'll build the folder and SKILL.md right away.

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
