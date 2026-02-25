---
description: Create a branded keynote presentation
argument-hint: [topic or title]
allowed-tools: Read, Write, Edit, Bash
---

Create a keynote presentation on the topic: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/SKILL.md

Follow these steps:

1. **Understand the topic**: If the topic is vague, ask one clarifying question. Otherwise, proceed.

2. **Choose the style**: Ask the user using AskUserQuestion:
   - **Dark mode** — ~~primary-bg backgrounds with light text. Bold, modern, high-contrast. Best for stage presentations and making visuals pop.
   - **Light mode** — White/light backgrounds with dark text. Clean, classic, professional. Best for printed handouts and bright rooms.

3. **Choose the framework**: Default to IGNITE for keynotes (persuasion). If the user requests a teaching-focused keynote, use EDUCATE. If data-driven, use REPORT.

3. **Outline the presentation**: Structure the presentation using the chosen framework. Map each stage to specific slides.

4. **Write the content**: For each slide, write:
   - A punchy headline (10 words max)
   - Supporting text or bullet points (minimal — this is a keynote, not a document)
   - Speaker notes (200-300 words with full talking points, transitions, and delivery cues)

6. **Apply brand identity**: Use ~~brand-name brand colors, typography, and visual style rules according to the chosen style mode (Dark or Light). See brand-identity SKILL.md for color application rules per mode.

7. **Create the file**: Build the .pptx presentation file. Aim for 12-20 slides.

8. **Quality check**: Verify against the Content Quality Checklist from the content frameworks skill. Ensure every slide has one clear idea, the narrative arc is compelling, and the CTA is singular and specific.
