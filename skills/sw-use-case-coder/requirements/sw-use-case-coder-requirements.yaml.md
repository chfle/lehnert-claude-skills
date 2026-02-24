# SW Use Case Coder – Requirements Reference

Optional structured input to guide code generation. The skill works from
`requirements/tech-stack.yaml` and `requirements/use-cases.md` automatically.
Use this template when you need to override defaults or add constraints.

---

## Template

```yaml
generation:
  uc_ids: []               # e.g. ["UC-001", "UC-003"] – or "all" / "all MVP"
  output_dir: "requirements/code"   # default, change only if needed

overrides:
  skip_tests: false         # true = skip test file generation
  skip_e2e: false           # true = skip E2E test generation
  skip_docker_snippet: false  # true = never generate docker-compose snippets
  flat_files: true          # true = all files in one folder (default)
                            # false = mirror src/ folder structure inside uc folder

style:
  comment_language: ""      # "en" | "de" | "auto" – language for inline comments
  todo_prefix: "TODO"       # prefix for acceptance criteria comments (e.g. "AC", "TODO")
```

---

## Example

```yaml
generation:
  uc_ids: ["UC-001", "UC-002", "UC-005"]
  output_dir: "requirements/code"

overrides:
  skip_tests: false
  skip_e2e: false
  skip_docker_snippet: false
  flat_files: true

style:
  comment_language: "de"
  todo_prefix: "AC"
```
