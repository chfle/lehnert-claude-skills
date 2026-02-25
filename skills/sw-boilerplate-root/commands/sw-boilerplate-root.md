# /sw-boilerplate-root

Creates root-level project config files directly in the workspace root based on `requirements/tech-stack.yaml`.

## Usage

```
/sw-boilerplate-root
```

## Creates

`package.json`, `tsconfig.json`, `pom.xml`, `build.gradle.kts`, `pyproject.toml`, `go.mod`, `.gitignore`, `.env.example`, and other root config files for the detected stack.

## Requires

`requirements/tech-stack.yaml` (run `/sw-tech-stack-planner` first)
