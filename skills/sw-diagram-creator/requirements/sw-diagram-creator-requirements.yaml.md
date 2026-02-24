# SW Diagram Creator – Requirements Template

Paste this YAML when triggering the skill for structured input. All fields optional — free text works too.

---

## Template

```yaml
diagram:
  type: ""          # flowchart | sequence | class | erd | c4 | gantt | mindmap | ascii
  format: "mermaid" # mermaid | plantuml | ascii
  title: ""         # optional title shown in diagram
  direction: ""     # TD (top-down) | LR (left-right) | Mermaid only, optional

source:
  type: "text"      # text | sw-idea-analyzer-output | requirements
  content: |
    # paste your description or sw-idea-analyzer output here

preferences:
  language: "auto"     # auto | de | en | es | fr | ...
  detail_level: "standard"  # minimal | standard | detailed
  theme: "default"     # default | dark | forest | neutral  (Mermaid only)
  offer_alternatives: true  # suggest other diagram types after output
```

---

## Example

```yaml
diagram:
  type: "erd"
  format: "mermaid"
  title: "FreelanceTracker – Database Schema"
  direction: ""

source:
  type: "sw-idea-analyzer-output"
  content: |
    Core Features:
    1. Time tracking (start/stop per project/client)
    2. Invoice generator (PDF from time entries)
    3. Client management (name, rate, address)
    4. Payment status (open / paid / overdue)
    5. Export (PDF, CSV)

preferences:
  language: "de"
  detail_level: "detailed"
  theme: "default"
  offer_alternatives: true
```
