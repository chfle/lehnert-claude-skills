---
name: sw-boilerplate-ui
description: Use when user wants to create UI foundation files (global styles, Tailwind config, shadcn/ui setup, layout components) based on requirements/tech-stack.yaml directly in the workspace root.
version: 1.0.0
author: Lehnert
---

# SW Boilerplate UI

## Overview

Creates UI foundation files directly in the workspace root based on `requirements/tech-stack.yaml`. Silent – nothing is printed in chat except the completion line.

If `frontend.framework` is "None" or absent in tech-stack.yaml, this skill does nothing and prints the completion line.

---

## Required Input

`requirements/tech-stack.yaml` must exist. If missing, stop:
> `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

---

## Files to Create

Read `frontend.framework`, `frontend.ui_library`, and `frontend.language` from tech-stack.yaml.

### Next.js + Tailwind CSS

| Path | Content |
|------|---------|
| `tailwind.config.ts` | Content paths for `app/**`, `components/**`, `lib/**`; theme extend with custom colors from stack if any |
| `postcss.config.js` | `tailwindcss` and `autoprefixer` plugins |
| `app/globals.css` | `@tailwind base/components/utilities` directives + CSS variables for theme |

### Next.js + shadcn/ui

All Tailwind files above, plus:

| Path | Content |
|------|---------|
| `components.json` | shadcn/ui config: style `default`, baseColor `neutral`, cssVariables `true`, path aliases |
| `lib/utils.ts` | `cn()` utility using `clsx` and `tailwind-merge` |
| `components/ui/.gitkeep` | Placeholder – shadcn components installed here via CLI |

### Next.js + Material UI (MUI)

| Path | Content |
|------|---------|
| `lib/theme.ts` | MUI `createTheme()` with primary/secondary colors |
| `components/ThemeProvider.tsx` | `'use client'` wrapper with MUI AppRouterCacheProvider + ThemeProvider |
| `app/layout.tsx` | Import ThemeProvider (update existing file) |

### React SPA (Vite)

| Path | Content |
|------|---------|
| `src/index.css` | CSS reset + base variables |
| `src/App.tsx` | Root component with Router provider if react-router is in stack |
| `src/main.tsx` | `ReactDOM.createRoot` mount |

If Tailwind: add `tailwind.config.ts`, `postcss.config.js`, update `src/index.css`.

### Vue 3

| Path | Content |
|------|---------|
| `src/main.ts` | `createApp(App).mount('#app')` |
| `src/App.vue` | Root component with `<RouterView />` |
| `src/assets/main.css` | Base styles |

### No frontend (API-only stacks)

Skip all files. Print completion line only.

---

## Output Rules

- Write all files silently to the workspace root.
- Never output file content, code snippets, or file lists in chat.
- At the end, print exactly one line:

```
✅ sw-boilerplate-ui completed
```
