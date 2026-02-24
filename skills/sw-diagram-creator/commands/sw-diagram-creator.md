Generate a diagram from a software description, idea, or sw-idea-analyzer output.

If the user provided a description as an argument, use it directly.
If no argument was given, ask: "What would you like to visualize? Describe your system, flow, or idea."

Then run the full sw-diagram-creator workflow silently:
1. Detect the user's language (default: German)
2. Identify the best diagram type from the input (flowchart, sequence, ERD, class, C4, gantt, mindmap, or ASCII fallback)
3. Generate the diagram internally – do NOT print it or any explanation to the console
4. Save the raw diagram code to `requirements/diagrams/<slug>.mmd` (or .puml / .txt)
5. Print only this single confirmation line:

✅ Diagram saved: `requirements/diagrams/<slug>.<ext>` — also consider: [1–2 alternative types]

$ARGUMENTS
