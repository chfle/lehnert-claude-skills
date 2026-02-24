Generate a diagram from a software description, idea, or sw-idea-analyzer output.

If the user provided a description as an argument, use it directly.
If no argument was given, ask: "What would you like to visualize? Describe your system, flow, or idea."

Then run the full sw-diagram-creator workflow:
1. Detect the user's language and respond in that language (default: German)
2. Identify the best diagram type from the input (flowchart, sequence, ERD, class, C4, gantt, mindmap, or ASCII fallback)
3. Generate the diagram in a ready-to-copy fenced code block (```mermaid, ```plantuml, or plain ```)
4. Add a 2–3 sentence explanation of what the diagram shows
5. Suggest 1–2 alternative diagram types the user might also want
6. Suggest the next logical skill at the end

$ARGUMENTS
