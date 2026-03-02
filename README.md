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
/plugin install sw-user-story-creator@lehnert-claude-skills
/plugin install sw-use-case-creator@lehnert-claude-skills
/plugin install sw-tech-stack-planner@lehnert-claude-skills
/plugin install sw-use-case-coder@lehnert-claude-skills
/plugin install sw-boilerplate@lehnert-claude-skills
/plugin install sw-boilerplate-root@lehnert-claude-skills
/plugin install sw-boilerplate-structure@lehnert-claude-skills
/plugin install sw-boilerplate-db@lehnert-claude-skills
/plugin install sw-boilerplate-docker@lehnert-claude-skills
/plugin install sw-boilerplate-ui@lehnert-claude-skills
/plugin install sw-diagram-creator@lehnert-claude-skills
/plugin install linux-shell-scriptor@lehnert-claude-skills
/plugin install linux-config-auditor@lehnert-claude-skills
/plugin install docker-compose-writer@lehnert-claude-skills
/plugin install linux-security-hardener@lehnert-claude-skills
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
| `sw-tech-stack-planner` | Slash command | `/sw-tech-stack-planner` |
| `sw-use-case-coder` | Slash command | `/sw-use-case-coder` |
| `sw-boilerplate` | Slash command | `/sw-boilerplate` |
| `sw-boilerplate-root` | Slash command | `/sw-boilerplate-root` |
| `sw-boilerplate-structure` | Slash command | `/sw-boilerplate-structure` |
| `sw-boilerplate-db` | Slash command | `/sw-boilerplate-db` |
| `sw-boilerplate-docker` | Slash command | `/sw-boilerplate-docker` |
| `sw-boilerplate-ui` | Slash command | `/sw-boilerplate-ui` |
| `linux-shell-scriptor` | Slash command | `/linux-shell-scriptor` |
| `linux-config-auditor` | Slash command | `/linux-config-auditor` |
| `docker-compose-writer` | Slash command | `/docker-compose-writer` |
| `linux-security-hardener` | Slash command | `/linux-security-hardener` |

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
| `sw-tech-stack-planner` | Recommends the best modern Docker-first tech stack and generates requirements/tech-stack.yaml with full rationale | ✅ Active |

### 💻 Software Development

| Skill | Description | Status |
|-------|-------------|--------|
| `sw-use-case-coder` | Generates production-ready code for selected use cases from tech-stack.yaml and use-cases.md | ✅ Active |
| `sw-boilerplate` | Orchestrates all boilerplate sub-skills to scaffold a complete project from tech-stack.yaml into root | ✅ Active |
| `sw-boilerplate-root` | Creates root config files (package.json, tsconfig.json, pom.xml etc.) from tech-stack.yaml | ✅ Active |
| `sw-boilerplate-structure` | Creates source code skeleton (entry points, modules, controllers) from tech-stack.yaml | ✅ Active |
| `sw-boilerplate-db` | Creates database and ORM files (Prisma schema, Flyway migration, SQLAlchemy) from tech-stack.yaml | ✅ Active |
| `sw-boilerplate-docker` | Creates Docker infrastructure files (docker-compose.yml, Dockerfiles, Traefik) from tech-stack.yaml | ✅ Active |
| `sw-boilerplate-ui` | Creates UI foundation files (Tailwind, shadcn/ui, globals.css, layout) from tech-stack.yaml | ✅ Active |

### 🐧 Linux & Sysadmin

| Skill | Description | Status |
|-------|-------------|--------|
| `linux-shell-scriptor` | Generates production-ready bash scripts for backups, monitoring, deployment, cron jobs, and any sysadmin task from a plain description | ✅ Active |
| `linux-config-auditor` | Audits nginx, Apache, sshd, systemd units, iptables, fail2ban, sudoers, and sysctl configs for security issues, misconfigs, and performance gaps | ✅ Active |
| `docker-compose-writer` | Generates or optimizes docker-compose.yml from a project description or existing stack — health checks, named volumes, env management, security hardening | ✅ Active |
| `linux-security-hardener` | Generates a complete server hardening plan with SSH lockdown, firewall, fail2ban, sysctl, auditd, and a ready-to-run harden.sh script | ✅ Active |

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
