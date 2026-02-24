---
name: sw-diagram-creator
description: Use when user wants to create, generate, or visualize a diagram from an idea, requirement, sw-idea-analyzer output, or any description – supports Mermaid flowchart, sequence, class, ERD, C4, gantt, mindmap, PlantUML, and ASCII art fallback.
version: 1.0.0
author: Lehnert
---

# SW Diagram Creator

## Overview

Generates ready-to-copy diagrams from any software description, idea, or structured input. Automatically selects the best diagram type and format. Always outputs clean, fenced code blocks — no setup required on the user's side.

**Language detection:** Read the user's input language and respond entirely in that language. Default to German if undetectable.

---

## Diagram Type Selection

Use this table to pick the best diagram type from the user's description:

| Input describes… | Best diagram type |
|------------------|-------------------|
| User flows, logic, decision trees | Mermaid `flowchart` |
| API calls, service interactions, login flows | Mermaid `sequenceDiagram` |
| Database tables and their relations | Mermaid `erDiagram` |
| Classes, objects, inheritance, domain model | Mermaid `classDiagram` or PlantUML |
| System architecture, services, containers | Mermaid `C4Context` / `C4Container` |
| Project timeline, sprints, milestones | Mermaid `gantt` |
| Concepts, features, brainstorming map | Mermaid `mindmap` |
| Plain text environment / no renderer | ASCII art |

**When in doubt:** Default to `flowchart`. It renders everywhere and covers most use cases.

---

## Supported Formats

### Mermaid (default)

Always output as:
```
```mermaid
[diagram code]
```
```

Supported types:
- `flowchart TD` / `flowchart LR` – process flows, user journeys, decision trees
- `sequenceDiagram` – request/response chains, API interactions, auth flows
- `erDiagram` – database schemas, entity relationships, foreign keys
- `classDiagram` – OOP models, domain objects, inheritance
- `C4Context` / `C4Container` – system context and container architecture (C4 model)
- `gantt` – timelines, sprint plans, release schedules
- `mindmap` – concept maps, feature lists, brainstorms

### PlantUML

Use when:
- User explicitly requests PlantUML
- Class or sequence diagram needs advanced features (notes, stereotypes, actors, grouping)

Output as:
```
```plantuml
@startuml
[diagram code]
@enduml
```
```

### ASCII Art (fallback)

Use when:
- User is in a plain-text environment (terminal, email, Markdown without renderer)
- User explicitly asks for ASCII
- A quick inline sketch is sufficient

Output as plain fenced code block (` ``` `).

---

## Output Rules

1. **Never print the diagram or explanation to the console.** All output goes to files only.
2. Do not show any diagram code, code blocks, or descriptions in the chat.
3. The only console output allowed is the final confirmation line.
4. If the input is ambiguous, ask **one** clarifying question before generating – do not guess silently.
5. After saving, offer 1–2 alternative diagram types in the confirmation line only.

---

## Input Types

The skill handles three input types – detect automatically:

| Input type | How to handle |
|------------|---------------|
| Free-text description | Infer entities, relationships, and flow from the text |
| `sw-idea-analyzer` output | Extract features (→ flowchart/ERD), services (→ C4), timeline (→ gantt) |
| YAML requirements (from requirements file) | Use `diagram.type` and `source.content` fields directly |

---

## Workflow

1. **Check for `requirements/vision.md`**
   - If the file exists → read it and use it as the primary input source
   - If the file does NOT exist and the user provided no other input → stop and respond:

     > `requirements/vision.md` not found. Please run `/sw-idea-analyzer` first to generate it, or describe your system directly as an argument:
     > `/sw-diagram-creator <your description>`

   - If the file does NOT exist but the user provided a description → continue with that description
2. Detect input language → all output in that language
3. Identify input type (vision.md / free-text / YAML)
4. Select diagram type using the table above (or use `diagram.type` from YAML)
5. Select format: Mermaid unless user specifies otherwise
6. Generate the diagram internally
7. Save the diagram file to `requirements/diagrams/` (see File Output below)
8. Print confirmation line only

---

## File Output

After generating every diagram, always save it to `requirements/diagrams/` in the repository root.

**Steps:**
1. If `requirements/diagrams/` does not exist, create it: `mkdir -p requirements/diagrams`
2. Slugify the diagram title: lowercase, spaces → hyphens, remove special chars
   - Example: "User Login Flow" → `user-login-flow`
3. Save with the correct extension:
   - Mermaid → `requirements/diagrams/<slug>.mmd`
   - PlantUML → `requirements/diagrams/<slug>.puml`
   - ASCII → `requirements/diagrams/<slug>.txt`
4. File content = the raw diagram code only (no fences, no explanation)
5. Print only this single line to the console:

> ✅ Diagram saved: `requirements/diagrams/<slug>.<ext>` — also consider: [1–2 alternative diagram types]

**Slug examples:**

| Title | Filename |
|-------|----------|
| User Login Flow | `requirements/diagrams/user-login-flow.mmd` |
| Freelancer ERD | `requirements/diagrams/freelancer-erd.mmd` |
| Auth Sequence | `requirements/diagrams/auth-sequence.puml` |

---

## Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| Generating without explanation | Always explain what the diagram shows |
| Using wrong direction (`TD` vs `LR`) | `TD` for hierarchies, `LR` for flows/pipelines |
| Forgetting `@startuml` / `@enduml` in PlantUML | Always wrap PlantUML output |
| Generating all possible diagrams unprompted | Generate one, offer alternatives |
| Mixing Mermaid syntax with PlantUML | Never mix – pick one format per output |

---

## Next Skill

At the end of every diagram, suggest the most logical next step:

> **Suggested next skill:**
> - Have features laid out visually? → Use **`user-story-writer`** to turn them into stories
> - Need to plan the build? → Use **`project-planner`** to schedule the work
> - Starting from scratch? → Use **`sw-idea-analyzer`** first to analyze your idea before diagramming
