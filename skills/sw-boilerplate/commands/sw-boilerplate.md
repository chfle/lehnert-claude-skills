# /sw-boilerplate

Scaffolds the complete project boilerplate directly in the workspace root based on `requirements/tech-stack.yaml`. Runs all sub-skills in sequence.

## Usage

```
/sw-boilerplate
```

## Sub-skills executed (in order)

1. `sw-boilerplate-root` – root config files
2. `sw-boilerplate-structure` – source code skeleton
3. `sw-boilerplate-db` – database/ORM files (if DB in stack)
4. `sw-boilerplate-docker` – Docker infrastructure (if docker.services present)
5. `sw-boilerplate-ui` – UI foundation (if frontend in stack)

## Output

```
✅ Boilerplate created in root
To test: <dynamic command from tech-stack.yaml>
```

## Requires

`requirements/tech-stack.yaml` (run `/sw-tech-stack-planner` first)
