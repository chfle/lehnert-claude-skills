# SW Idea Analyzer – Requirements Template

Paste this YAML when triggering the skill to give Claude structured input. All fields are optional – the skill works from free text too.

---

## Template

```yaml
idea:
  title: ""                  # Short name for the idea
  description: ""            # What the software does, in 1-3 sentences
  target_audience: ""        # Who is this for?
  problem_statement: ""      # What problem does it solve?

constraints:
  budget: ""                 # e.g. "Bootstrap", "< 10k EUR", "VC-backed"
  timeline: ""               # e.g. "3 months to MVP", "6 weeks"
  team_size: 0               # Number of people building it

preferences:
  output_language: "auto"    # auto | de | en | es | fr | ...
  depth: "standard"          # quick | standard | deep
  focus: []                  # feasibility | features | risks | market | tech-stack
```

---

## Example

```yaml
idea:
  title: "TaskMate – Smart Team Task Manager"
  description: >
    A task management app that uses AI to auto-prioritize tasks
    based on deadlines and team workload. Integrates with Slack and GitHub.
  target_audience: "Small to medium teams (5–50 people)"
  problem_statement: >
    Teams waste hours per week manually sorting, re-prioritizing, and
    communicating task status across tools.

constraints:
  budget: "Bootstrap / < 10k EUR"
  timeline: "3 months to MVP"
  team_size: 2

preferences:
  output_language: "auto"
  depth: "deep"
  focus:
    - feasibility
    - features
    - market
```
