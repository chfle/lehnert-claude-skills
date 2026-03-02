# /docker-compose-writer

Generate or optimize a `docker-compose.yml` from a project description or existing stack.

## Usage

```
/docker-compose-writer <project description or paste existing compose>
```

## Examples

```
/docker-compose-writer Next.js app, PostgreSQL 16, Redis, Traefik with Let's Encrypt
/docker-compose-writer [paste existing docker-compose.yml to optimize]
/docker-compose-writer add a RabbitMQ service to my existing stack
/docker-compose-writer FastAPI backend, MongoDB, nginx, dev and prod split
```

## Behavior

If a description or existing compose content is provided as an argument, use it directly.
If no argument is given, ask: "Describe your stack (services, databases, reverse proxy) or paste an existing compose file to optimize."

Then run the full docker-compose-writer workflow:
1. Detect the mode: Generate from description / Optimize existing / Add a service
2. Ask at most one clarifying question if critical info is missing
3. Generate the complete docker-compose.yml: named networks/volumes, health checks, depends_on with service_healthy, pinned image tags, env vars via .env, restart policies, resource limits, cap_drop security
4. Print the docker-compose.yml in a fenced `yaml` code block
5. Print the .env.example with all required variables
6. List assumptions made
7. Show quick start commands
8. Suggest next steps

$ARGUMENTS
