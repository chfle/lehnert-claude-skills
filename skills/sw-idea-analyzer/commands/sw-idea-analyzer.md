Analyze a software idea using the sw-idea-analyzer skill.

If the user provided an idea as an argument, use it directly.
If no argument was given, ask: "What's your software idea? Describe it in a few sentences."

Then run the full sw-idea-analyzer framework silently:
1. Detect the user's language (default: German)
2. Run all 8 analysis sections internally (Idea Summary, Target Audience, Problem & Solution Fit, Core Features, Technical Complexity, Market & Competition, Top Risks, Next Steps)
3. Write the complete structured output to `requirements/vision.md` – do NOT print it to the console
4. Print only these lines:

✅ Analysis complete. Please review: `requirements/vision.md`

▶ Ready to visualize? Run `/sw-diagram-creator` to generate diagrams from your vision.

$ARGUMENTS
