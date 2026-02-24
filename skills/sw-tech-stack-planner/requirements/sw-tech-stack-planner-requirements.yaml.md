# SW Tech Stack Planner – Requirements Template

Optional structured input to guide stack selection. The skill works from requirements files or free text too.

---

## Template

```yaml
project:
  title: ""
  type: ""              # web-app | api | mobile | cli | fullstack
  team_size: 1          # affects complexity of tooling recommended
  language_preference: ""  # e.g. "TypeScript", "Python", "Java" – or "auto"

constraints:
  self_hosted: false    # true = prefer self-hosted solutions (Keycloak, Coolify)
  budget: ""            # hobby | startup | enterprise
  existing_stack: ""    # e.g. "we already use AWS" – respected in recommendations

preferences:
  docker_first: true    # always true by default
  testcontainers: true  # always true when supported
  monorepo: false       # true = suggest Turborepo / Nx
  microservices: false  # true = suggest service boundaries in docker-compose
```

---

## Example

```yaml
project:
  title: "FreelanceTracker"
  type: "web-app"
  team_size: 1
  language_preference: "TypeScript"

constraints:
  self_hosted: true
  budget: "startup"
  existing_stack: ""

preferences:
  docker_first: true
  testcontainers: true
  monorepo: false
  microservices: false
```
