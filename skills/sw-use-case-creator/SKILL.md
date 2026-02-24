---
name: sw-use-case-creator
description: Use when user wants to create use cases, detailed system behavior specs, actor flows, or a use case document from user-stories.md, vision.md, or any software description – generates requirements/use-cases.md silently.
version: 1.0.0
author: Lehnert
---

# SW Use Case Creator

## Overview

Reads `requirements/user-stories.md` (from `sw-user-story-creator`) or falls back to `requirements/vision.md` and produces a complete, professional use case document at `requirements/use-cases.md`. Operates silently – no file content is shown in the chat.

**Language detection:** Read the user's input language and respond entirely in that language. Default to German if undetectable.

---

## Required Input – Check in Order

1. **`requirements/user-stories.md` exists** → primary source. Extract epics, roles, goals, and acceptance criteria.
2. **`requirements/user-stories.md` missing, `requirements/vision.md` exists** → use vision.md to derive use cases from features and target audience.
3. **Both missing, user provided description** → use the description as input.
4. **All missing, no input given** → stop and respond:

   > Neither `requirements/user-stories.md` nor `requirements/vision.md` found.
   > Run `/sw-user-story-creator` or `/sw-idea-analyzer` first, or provide your description directly:
   > `/sw-use-case-creator <your description>`

---

## Use Case Format

Every use case follows this exact structure:

```
## UC-XXX: <Use Case Name>

**Actors:** <Primary Actor>, <Secondary Actor (if any)>
**Preconditions:** <What must be true before this use case can start>
**Postconditions:** <What is true after successful completion>

### Main Flow
1. Step one
2. Step two
3. ...

### Alternative Flows
- **A1 – <Name>:** <Trigger condition> → <Steps>
- **A2 – <Name>:** <Trigger condition> → <Steps>

### Exceptions
- **E1 – <Name>:** <Error condition> → <System response>

### Acceptance Criteria
- [ ] Linked to US-XXX: <criterion>
- [ ] Linked to US-XXX: <criterion>
```

**Rules:**
- One use case per significant user goal (not per button or field)
- Actors = specific roles from target audience, never "the system" as primary actor
- Main flow = happy path only, numbered steps
- Alternative flows = valid but non-default paths
- Exceptions = error states the system must handle
- Acceptance criteria = link back to user story IDs where possible

---

## Deriving Use Cases from Input

| Source | How to derive use cases |
|--------|------------------------|
| `user-stories.md` | One use case per epic or per high-level user goal; group related stories |
| `vision.md` | One use case per core MVP feature |
| Free text | Identify actor + goal pairs; each pair = one use case |

Aim for 5–10 use cases for a typical MVP. Do not create a use case for every micro-interaction.

---

## Output Rules

- **Never output the file content in the chat.** All content goes to the file only.
- Do not show any use case text, flows, or Markdown in the chat response.
- The only chat output allowed is the confirmation lines at the end.
- Use the user's language throughout the file content.
- Never truncate – include every flow, every exception.

---

## File Output

After generating all use cases, write to `requirements/use-cases.md` in the repository root.

**Steps:**
1. If `requirements/` does not exist: `mkdir -p requirements`
2. Write the complete document to `requirements/use-cases.md`
3. Print **only** these lines to the chat – nothing else:

> ✅ File created: `requirements/use-cases.md`
> Open it in your editor or commit it.

One optional summary sentence max (e.g. "Generated 7 use cases covering all MVP features."). No code blocks, no Markdown content, no full output.

**`requirements/use-cases.md` structure:**

```markdown
# Use Cases: <Project Title>

> Generated: <YYYY-MM-DD>
> Source: requirements/user-stories.md

---

## UC-001: <Use Case Name>

**Actors:** ...
**Preconditions:** ...
**Postconditions:** ...

### Main Flow
1. ...

### Alternative Flows
- **A1 – ...:** ...

### Exceptions
- **E1 – ...:** ...

### Acceptance Criteria
- [ ] ...

---

## UC-002: <Use Case Name>
...
```

---

## Next Skill

> ▶ **Visualize the flows?** → Run `/sw-diagram-creator` to generate sequence or flowchart diagrams from your use cases.
