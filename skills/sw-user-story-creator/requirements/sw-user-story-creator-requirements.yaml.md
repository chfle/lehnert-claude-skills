# SW User Story Creator – Requirements Template

Optional structured input. The skill works from `requirements/vision.md` or free text too.

---

## Template

```yaml
project:
  title: ""              # Project name (used in document header)
  vision_file: "requirements/vision.md"  # path to vision file, or "none"

output:
  file: "requirements/user-stories.md"
  language: "auto"       # auto | de | en | es | fr | ...

story_config:
  roles: []              # e.g. ["Freelancer", "Admin", "Guest"] – overrides auto-detection
  include_nice_to_have: true
  min_acceptance_criteria: 3

epics: []                # optional: pre-define epics to structure output
                         # e.g. ["Authentication", "Dashboard", "Billing"]
```

---

## Example

```yaml
project:
  title: "FreelanceTracker"
  vision_file: "requirements/vision.md"

output:
  file: "requirements/user-stories.md"
  language: "de"

story_config:
  roles:
    - "Freelancer"
    - "Steuerberater"
  include_nice_to_have: true
  min_acceptance_criteria: 4

epics:
  - "Zeiterfassung"
  - "Rechnungsstellung"
  - "Kundenverwaltung"
  - "Zahlungsstatus"
  - "Export & Einstellungen"
```
