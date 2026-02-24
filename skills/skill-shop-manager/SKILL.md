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
| `sw-diagram-creator` | Generates Mermaid, PlantUML, and ASCII diagrams from any description or sw-idea-analyzer output | ✅ Active |
| `sw-user-story-creator` | Generates a complete user story document with acceptance criteria from vision.md or any description | ✅ Active |
| `sw-use-case-creator` | Generates a detailed use case document with actors, flows, and exceptions from user-stories.md or any description | ✅ Active |
| `sw-tech-stack-planner` | Recommends the best modern Docker-first tech stack and generates requirements/tech-stack.yaml | ✅ Active |

### 💻 Software Development

| Skill | Description | Status |
|-------|-------------|--------|
| `sw-use-case-coder` | Generates production-ready code for selected use cases from tech-stack.yaml and use-cases.md | ✅ Active |

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
| "Create a diagram for my app" | → `sw-diagram-creator` |
| "Generate an ERD / flowchart / sequence diagram" | → `sw-diagram-creator` |
| "Visualize my sw-idea-analyzer output" | → `sw-diagram-creator` |
| "Write user stories for my project" | → `sw-user-story-creator` |
| "Create acceptance criteria" | → `sw-user-story-creator` |
| "Turn my vision into requirements" | → `sw-user-story-creator` |
| "Create use cases for my project" | → `sw-use-case-creator` |
| "Write actor flows and exceptions" | → `sw-use-case-creator` |
| "Specify system behavior in detail" | → `sw-use-case-creator` |
| "Choose a tech stack for my project" | → `sw-tech-stack-planner` |
| "Set up Docker / docker-compose" | → `sw-tech-stack-planner` |
| "What framework should I use?" | → `sw-tech-stack-planner` |
| "Generate code for UC-001" | → `sw-use-case-coder` |
| "Implement all MVP use cases" | → `sw-use-case-coder` |
| "Scaffold files for a use case" | → `sw-use-case-coder` |

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
