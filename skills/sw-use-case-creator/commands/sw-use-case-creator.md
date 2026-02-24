Generate a complete use case document from requirements/user-stories.md, requirements/vision.md, or a provided description.

If the user provided a description as an argument, use it directly.
If no argument was given, check inputs in this order:
1. `requirements/user-stories.md` exists → use it as primary source
2. `requirements/vision.md` exists → use it as fallback
3. Neither exists → stop and respond:
   "Neither requirements/user-stories.md nor requirements/vision.md found. Run /sw-user-story-creator or /sw-idea-analyzer first, or provide a description: /sw-use-case-creator <your description>"

Then run the full sw-use-case-creator workflow silently:
1. Detect the user's language (default: German)
2. Identify actors and user goals from the input
3. Derive 5–10 use cases – one per significant user goal or epic
4. For each use case write: actors, preconditions, postconditions, main flow, alternative flows, exceptions, acceptance criteria linked to user story IDs
5. Write the complete document to `requirements/use-cases.md` – do NOT show file content in the chat
6. Print ONLY these lines:

✅ File created: requirements/use-cases.md
Open it in your editor or commit it.

One optional summary sentence max (e.g. "Generated 7 use cases covering all MVP features.") – no code blocks, no Markdown, no full output.

$ARGUMENTS
