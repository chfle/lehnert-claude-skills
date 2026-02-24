# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code **plugin marketplace** – a collection of personal skills installable via:

```bash
/plugin marketplace add chfle/lehnert-claude-skills
/plugin install <skill-name>@lehnert-claude-skills
```

No build system, no tests, no dependencies. The "source code" is Markdown.

## Structure

```
.claude-plugin/
  marketplace.json   ← marketplace registry (lists all 22 skills)
  plugin.json        ← repo-level plugin manifest
skills/
  <skill-name>/
    SKILL.md                        ← required, the skill content
    .claude-plugin/plugin.json      ← required only for active skills (strict: true)
```

## Rules for every skill

- File is always named exactly `SKILL.md`
- Folder name: kebab-case only
- YAML frontmatter: only `name` and `description` fields, max 1024 chars total
- `description` must start with `Use when …` and list concrete triggers – never summarize the workflow
- No `README.md` inside skill folders
- Flat: `skills/skill-name/SKILL.md` – no deeper nesting

## Adding a new skill

1. Create `skills/<skill-name>/SKILL.md` with frontmatter + content
2. If making it active (`strict: true`), add `skills/<skill-name>/.claude-plugin/plugin.json`
3. Update the `plugins` array in `.claude-plugin/marketplace.json` (change `strict: false` → remove it or set `strict: true`, bump `version` to `1.0.0`)
4. Update the skill table in both `README.md` and `skills/skill-shop-manager/SKILL.md`

## Keeping things in sync

Three places must always match when a skill changes status or is added:

| File                                 | What to update                              |
| ------------------------------------ | ------------------------------------------- |
| `.claude-plugin/marketplace.json`    | `plugins` array entry                       |
| `README.md`                          | Skill Overview table                        |
| `skills/skill-shop-manager/SKILL.md` | Skill Catalog table + Recommendations table |

## Marketplace schema reference

Active skill entry (`strict: true` – needs `.claude-plugin/plugin.json`):

```json
{
  "name": "skill-name",
  "source": "./skills/skill-name",
  "description": "One sentence.",
  "version": "1.0.0",
  "author": { "name": "Lehnert" },
  "category": "category-name",
  "keywords": ["tag1", "tag2"]
}
```

Planned skill placeholder (`strict: false` – no plugin.json needed):

```json
{
  "name": "skill-name",
  "source": "./skills/skill-name",
  "description": "Planned skill: skill-name",
  "version": "0.1.0",
  "author": { "name": "Lehnert" },
  "strict": false
}
```
