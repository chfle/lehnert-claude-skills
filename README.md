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
/plugin install blog-writer@lehnert-claude-skills
/plugin install code-reviewer@lehnert-claude-skills
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

| #   | Skill                | Category | Description                                    | Status    |
| --- | -------------------- | -------- | ---------------------------------------------- | --------- |
| 01  | `skill-shop-manager` | 🏪 Meta  | Manages this shop, lists and recommends skills | ✅ Active |

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
