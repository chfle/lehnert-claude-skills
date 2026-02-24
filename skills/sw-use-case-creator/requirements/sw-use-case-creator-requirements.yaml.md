# SW Use Case Creator – Requirements Template

Optional structured input. The skill works from `requirements/user-stories.md`, `requirements/vision.md`, or free text too.

---

## Template

```yaml
project:
  title: ""                    # Project name (used in document header)
  source_file: "requirements/user-stories.md"  # or "requirements/vision.md" or "none"

output:
  file: "requirements/use-cases.md"
  language: "auto"             # auto | de | en | es | fr | ...

use_case_config:
  actors: []                   # e.g. ["Freelancer", "Admin"] – overrides auto-detection
  max_use_cases: 10            # cap to avoid over-generation
  include_alternative_flows: true
  include_exceptions: true
  link_to_user_stories: true   # adds Acceptance Criteria linked to US-XXX IDs
```

---

## Example

```yaml
project:
  title: "FreelanceTracker"
  source_file: "requirements/user-stories.md"

output:
  file: "requirements/use-cases.md"
  language: "de"

use_case_config:
  actors:
    - "Freelancer"
    - "Steuerberater"
  max_use_cases: 8
  include_alternative_flows: true
  include_exceptions: true
  link_to_user_stories: true
```
