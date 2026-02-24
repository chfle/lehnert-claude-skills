# /sw-use-case-coder

Generate production-ready code for one or more use cases.

## Usage

```
/sw-use-case-coder <uc-selection>
```

## Arguments

| Argument | Example | Meaning |
|----------|---------|---------|
| Single UC | `UC-001` | Implement one use case |
| Multiple UCs | `UC-001 UC-003` | Implement specific use cases |
| MVP scope | `all MVP` | Implement all MVP-tagged use cases |
| All | `all` | Implement every use case |

## Examples

```
/sw-use-case-coder UC-001
/sw-use-case-coder UC-001 UC-004
/sw-use-case-coder all MVP
/sw-use-case-coder all
```

## Output

Creates `requirements/code/<uc-slug>/` with all generated files.
Copy to `src/` when satisfied.

## Requires

- `requirements/tech-stack.yaml` (run `/sw-tech-stack-planner` first)
- `requirements/use-cases.md` (run `/sw-use-case-creator` first)
