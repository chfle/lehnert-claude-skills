---
name: sw-idea-analyzer
description: Use when user shares a software idea, app concept, startup idea, or product vision and wants it analyzed for feasibility, broken down into features, assessed for risks, or needs a structured evaluation before starting development.
version: 1.0.0
author: Lehnert
---

# SW Idea Analyzer

## Overview

Analyzes any software or product idea with a structured framework: feasibility, target audience, core features, technical complexity, risks, and MVP scope. Pure planning and analysis – no code generation, no external calls.

**Language detection:** Read the user's input language and respond entirely in that language. If the language cannot be determined, default to German.

---

## When to Use

- User describes an app, tool, platform, or startup idea
- User wants to know if an idea is worth pursuing
- User needs help defining MVP scope before development starts
- User wants risks and challenges surfaced early
- User asks "is this a good idea?" or "what would I need to build X?"

## When NOT to Use

- User already has a spec and wants user stories → use `user-story-writer`
- User wants a project timeline → use `project-planner`
- User wants market research only → use `research-assistant`

---

## Analysis Framework

Run all eight sections in order. Adjust depth based on detail in user's input.

### 1. Idea Summary
One sentence restatement of the core idea in plain language. No jargon.

### 2. Target Audience

| Segment | Description | Primary Pain Point |
|---------|-------------|-------------------|
| Primary | ... | ... |
| Secondary | ... | ... |

### 3. Problem & Solution Fit
- **Core problem:** What frustration or inefficiency does this solve?
- **Solution approach:** How does the idea address it?
- **Differentiation:** What makes this different from existing tools?

### 4. Core Features (MVP)

Top 5 must-have features for a usable first version:

| # | Feature | Why It's Core |
|---|---------|---------------|
| 1 | | |
| 2 | | |
| 3 | | |
| 4 | | |
| 5 | | |

Features explicitly **out of MVP scope:** list at least 3 things to defer.

### 5. Technical Complexity

Rate overall complexity: 🟢 Simple / 🟡 Medium / 🔴 Complex

Assess each dimension:

| Dimension | Rating | Notes |
|-----------|--------|-------|
| Authentication & permissions | | |
| External integrations / APIs | | |
| Real-time requirements | | |
| AI / ML components | | |
| Data volume & storage | | |
| Mobile / cross-platform | | |

### 6. Market & Competition

List 2–3 existing alternatives and explain the differentiation angle:

| Competitor / Alternative | Weakness | Your Angle |
|--------------------------|----------|------------|
| | | |

### 7. Top Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| | High / Med / Low | High / Med / Low | |
| | | | |
| | | | |

### 8. Next Steps

3–5 concrete, immediately actionable steps the user can take after this analysis. Be specific, not generic.

---

## Output Rules

- **Never print the analysis to the console.** All output goes to files only.
- Do not show tables, sections, or any analysis content in the chat.
- The only console output allowed is the final confirmation line.
- Always use the user's input language in the file content.
- Use tables for structured data (features, risks, competition) inside the file.
- Never generate code.
- Never make API calls or use external tools.

---

## File Output

After completing the full analysis, write everything to `requirements/vision.md`. Print nothing to the console except the confirmation.

**Steps:**
1. If `requirements/` does not exist, create it first: `mkdir -p requirements`
2. Write the complete structured analysis (all 8 sections, clean Markdown) to `requirements/vision.md`
3. Print only this single line to the console:

> ✅ Analysis complete. Please review: `requirements/vision.md`

**`requirements/vision.md` structure:**
```markdown
# Vision: <Idea Title>

## Idea Summary
...

## Target Audience
| Segment | Description | Primary Pain Point |
...

## Problem & Solution Fit
...

## Core Features (MVP)
| # | Feature | Why It's Core |
...

## Technical Complexity
...

## Market & Competition
...

## Top Risks
...

## Next Steps
...

## Suggested Next Skill
...
```

---

## Next Skill

At the end of every analysis, suggest the most logical next step:

> **Suggested next skill:**
> - Got clear features? → Use **`user-story-writer`** to turn them into actionable stories
> - Need a timeline? → Use **`project-planner`** to scope the work
> - Need market validation? → Use **`research-assistant`** for deeper competitor research
> - Building a product? → Use **`brand-guidelines`** to define the identity early
