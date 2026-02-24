Recommend the best modern tech stack for the project and generate requirements/tech-stack.yaml.

If the user provided a description as an argument, use it directly.
If no argument was given, read all available requirements files in this order:
1. requirements/vision.md
2. requirements/user-stories.md
3. requirements/use-cases.md

If none exist, stop and respond:
"No requirements files found. Run /sw-idea-analyzer first, or describe your project: /sw-tech-stack-planner <your description>"

Then run the full sw-tech-stack-planner workflow silently:
1. Detect the user's language (default: German)
2. Analyze project type, complexity, and feature scope from all available inputs
3. Select the best modern stack: Docker-first, PostgreSQL/Redis in containers, Testcontainers for integration tests
4. Write the complete tech-stack.yaml to requirements/tech-stack.yaml – do NOT show content in the chat
5. Print ONLY these lines:

✅ File created: requirements/tech-stack.yaml
This is now the official tech stack for all following coding skills.

One optional summary sentence max (e.g. "Recommended Next.js 15 + NestJS + PostgreSQL + Docker stack with Testcontainers.") – no YAML, no code blocks, no full output.

$ARGUMENTS
