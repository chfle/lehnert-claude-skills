Generate a complete user story document from requirements/vision.md or a provided description.

If the user provided a description as an argument, use it directly.
If no argument was given, check for `requirements/vision.md`:
- If it exists → read it and use it as input
- If it does NOT exist → stop and respond:
  "`requirements/vision.md` not found. Run `/sw-idea-analyzer` first to generate it, or provide your idea directly: `/sw-user-story-creator <your description>`"

Then run the full sw-user-story-creator workflow silently:
1. Detect the user's language (default: German)
2. Extract project title, target audience, and core features from the input
3. Group stories into epics based on feature clusters
4. For each core MVP feature, write at least one user story in "As a [role], I want [goal] so that [benefit]" format
5. Add 3–5 acceptance criteria per story as a checklist
6. Mark each story as MVP or Nice-to-have
7. Write the complete document to `requirements/user-stories.md` – do NOT show file content in the chat
8. Print ONLY these lines:

✅ File created: requirements/user-stories.md
Open it in your editor or commit it.

One optional sentence max (e.g. "Generated 9 user stories across 3 epics.") – no code blocks, no Markdown, no full output.

Next: Run /sw-use-case-creator to expand stories into detailed use cases, or /sw-diagram-creator to visualize the architecture.

$ARGUMENTS
