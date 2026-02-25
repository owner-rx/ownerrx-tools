---
description: Create a branded workshop presentation
argument-hint: [topic or title]
allowed-tools: Read, Write, Edit, Bash
---

Create a workshop presentation on the topic: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/SKILL.md

Follow these steps:

1. **Understand the topic**: Ask the user:
   - How long is the workshop? (This determines slide count)
   - What should participants be able to DO after the workshop?
   If the user already provided this context, skip asking.

2. **Choose the style**: Ask the user using AskUserQuestion:
   - **Dark mode** — ~~primary-bg backgrounds with light text. Bold, modern, high-contrast. Best for projected presentations in dim rooms.
   - **Light mode** — White/light backgrounds with dark text. Clean, classic, professional. Best for printed handouts and bright rooms.

3. **Choose the framework**: Default to EDUCATE for workshops (teaching). If the workshop is sales-oriented (e.g., a pitch workshop), use IGNITE.

3. **Structure with the framework + interactivity**: Use the chosen framework as the narrative backbone, and weave in interactive elements:
   - Icebreaker activity after the opening
   - Exercise slides after each teaching section (visually distinct with ~~accent-primary border/accent)
   - Reflection prompts between major sections
   - Group discussion slides where appropriate

4. **Write the content**: For each slide, write:
   - A clear headline
   - Supporting content (workshops can have more text than keynotes since participants reference slides)
   - Exercise instructions (be specific: what to do, how long, what to share back)
   - Speaker notes with facilitation tips (timing, how to debrief, what to watch for)

6. **Apply brand identity**: Use ~~brand-name brand colors and style according to the chosen style mode (Dark or Light). Include section divider slides with colored backgrounds to separate modules. Mark exercise slides distinctly with ~~accent-primary.

7. **Create the file**: Build the .pptx presentation file. Aim for 15-30 slides depending on duration.

8. **Quality check**: Ensure learning objectives are clear, exercises are actionable, and the workshop builds toward a practical outcome the participant can implement immediately.
