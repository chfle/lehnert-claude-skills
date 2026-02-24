# Lehnert's Claude Skill Shop 🚀

> Your personal collection of Claude skills – installable for Claude Code and Claude.ai.

---

## 🚀 Marketplace Installation (Claude Code 2026)

The fastest way to get everything – one command registers the marketplace, one command installs any skill:

```bash
# Step 1: Register this repo as a marketplace (once)
/plugin marketplace add chfle/lehnert-claude-skills

# Step 2: Install individual skills
/plugin install skill-shop-manager@lehnert-claude-skills
/plugin install sw-idea-analyzer@lehnert-claude-skills
/plugin install sw-diagram-creator@lehnert-claude-skills
```

**To install all active skills at once:**

```bash
/plugin install skill-shop-manager@lehnert-claude-skills
```

**When a new skill is released**, just run:

```bash
/plugin install <skill-name>@lehnert-claude-skills
```

No cloning, no copying files – Claude Code fetches and installs directly from GitHub.

---

## What is this?

This shop is a Git repository with **ready-made skills** for Claude. Every skill lives in its own folder as a `SKILL.md` file and can be installed in seconds. The central meta-skill `skill-shop-manager` knows all skills, recommends the right one, and creates new ones on demand.

---

## Commands vs Skills

There are two types of active plugins in this shop:

### Slash Commands (`/command-name`)

Skills that have a `commands/` directory with a matching `.md` file become **direct slash commands** in Claude Code. They appear in the `/` menu and can be invoked immediately with optional arguments:

```bash
/sw-idea-analyzer A gym tracking app with AI coaching
/sw-diagram-creator ERD for the freelancer invoicing app
```

### Meta / Sub-Skills (loaded automatically)

Skills without a `commands/` directory are **helpers** – they are loaded automatically by the `skill-shop-manager` or referenced as next steps by other skills. They don't appear as `/` commands but Claude knows when to use them based on context.

**Current skills and their type:**

| Skill | Type | How to invoke |
|-------|------|---------------|
| `skill-shop-manager` | Meta | Loaded automatically |
| `sw-idea-analyzer` | Slash command | `/sw-idea-analyzer` |
| `sw-diagram-creator` | Slash command | `/sw-diagram-creator` |
| `sw-user-story-creator` | Slash command | `/sw-user-story-creator` |
| `sw-use-case-creator` | Slash command | `/sw-use-case-creator` |

**Rule of thumb:** If a skill is a standalone user-facing tool → give it a `commands/` file. If it's a helper or orchestrator → leave it as a pure skill.

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

### 🗓️ Software Planning

| Skill | Description | Status |
|-------|-------------|--------|
| `sw-idea-analyzer` | Analyzes software ideas for feasibility, features, risks, and MVP scope | ✅ Active |
| `sw-diagram-creator` | Generates Mermaid, PlantUML, and ASCII diagrams from any description or sw-idea-analyzer output | ✅ Active |
| `sw-user-story-creator` | Generates a complete user story document with acceptance criteria from vision.md or any description | ✅ Active |
| `sw-use-case-creator` | Generates a detailed use case document with actors, flows, and exceptions from user-stories.md or any description | ✅ Active |

### 💻 Software Development

| Skill | Description | Status |
|-------|-------------|--------|
| *(coming soon)* | | |

### 🐧 Linux & Sysadmin

| Skill | Description | Status |
|-------|-------------|--------|
| *(coming soon)* | | |

### 🔒 Pentesting

| Skill | Description | Status |
|-------|-------------|--------|
| *(coming soon)* | | |

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

| Rule               | Detail                                                                  |
| ------------------ | ----------------------------------------------------------------------- |
| **File name**      | Always exactly `SKILL.md`                                               |
| **Folder name**    | kebab-case, letters/numbers/hyphens only                                |
| **Frontmatter**    | Only `name` and `description`, max 1024 characters total                |
| **description**    | Starts with `Use when …`, describes triggers only – no workflow summary |
| **No README**      | No `README.md` inside skill folders                                     |
| **Flat structure** | `skills/skill-name/SKILL.md` – no deeper nesting                        |

---

## Folder Structure

```
lehnert-claude-skills/
├── .claude-plugin/
│   ├── marketplace.json         ← marketplace registry
│   └── plugin.json              ← repo-level plugin manifest
├── README.md
└── skills/
    ├── skill-shop-manager/
    │   ├── .claude-plugin/
    │   │   └── plugin.json      ← skill plugin manifest
    │   └── SKILL.md
    ├── brand-guidelines/        ← to be created
    │   └── SKILL.md
    └── ...
```

---

## Status Legend

| Icon           | Meaning                        |
| -------------- | ------------------------------ |
| ✅ Active      | Finished and ready to use      |
| 🚧 In Progress | Currently being developed      |
| 🔲 Planned     | Slot reserved, not yet created |

---

_Built with Claude Code · Maintained by Lehnert_
