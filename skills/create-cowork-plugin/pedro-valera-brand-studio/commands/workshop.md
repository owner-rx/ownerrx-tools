---
description: Create a branded workshop presentation
argument-hint: [topic or title]
allowed-tools: Read, Write, Edit, Bash
---

Create a workshop presentation on the topic: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/storytelling-framework/SKILL.md

Follow these steps:

1. **Understand the topic**: Ask the user:
   - How long is the workshop? (This determines slide count)
   - What should participants be able to DO after the workshop?
   If the user already provided this context, skip asking.

2. **Structure with IGNITE + Interactivity**: Use the IGNITE framework as the narrative backbone, but weave in interactive elements:
   - Icebreaker activity after the opening
   - Exercise slides after each teaching section (visually distinct with blue #2563EB border/accent)
   - Reflection prompts between major sections
   - Group discussion slides where appropriate

3. **Write the content**: For each slide, write:
   - A clear headline
   - Supporting content (workshops can have more text than keynotes since participants reference slides)
   - Exercise instructions (be specific: what to do, how long, what to share back)
   - Speaker notes with facilitation tips (timing, how to debrief, what to watch for)

4. **Apply brand identity**: Use Pedro Valera Digital brand colors and style. Include section divider slides with colored backgrounds to separate modules. Mark exercise slides distinctly.

5. **Create the file**: Build the .pptx presentation file using the pptx skill. Aim for 15-30 slides depending on duration.

6. **Quality check**: Ensure learning objectives are clear, exercises are actionable, and the workshop builds toward a practical outcome the participant can implement immediately.
