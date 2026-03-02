Generate or optimize a docker-compose.yml using the docker-compose-writer skill.

If the user provided a project description or existing compose content as an argument, use it directly.
If no argument was given, ask: "Describe your stack — what services do you need? (e.g. 'Next.js app, PostgreSQL, Redis, nginx reverse proxy') Or paste an existing compose file to optimize it."

Then run the full docker-compose-writer workflow:
1. Detect the mode: Generate from description / Optimize existing / Add a service
2. Ask at most ONE clarifying question if critical info is missing (env target, DB type, reverse proxy)
3. Generate the complete docker-compose.yml following all quality standards:
   - Named networks and volumes
   - Health checks with depends_on condition: service_healthy
   - Environment variables via env_file / ${ENV_VAR} — never hardcoded
   - Pinned image tags (never `latest` in prod)
   - Restart policies and resource limits for production
   - Security: cap_drop, read-only mounts, non-root users
4. Print the docker-compose.yml in a fenced yaml code block
5. Print the .env.example with all required variables
6. List any assumptions made
7. Show quick start commands (cp .env.example .env → docker compose up -d)
8. Suggest next steps

$ARGUMENTS
