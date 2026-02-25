---
description: Create a branded keynote presentation
argument-hint: [topic or title]
allowed-tools: Read, Write, Edit, Bash
---

Create a keynote presentation on the topic: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/storytelling-framework/SKILL.md

Follow these steps:

1. **Understand the topic**: If the topic is vague, ask one clarifying question. Otherwise, proceed.

2. **Outline using IGNITE**: Structure the presentation using the IGNITE framework (Identify pain → Gauge stakes → Narrate turning point → Introduce solution → Trust through proof → Empower with action). Map each IGNITE stage to specific slides.

3. **Write the content**: For each slide, write:
   - A punchy headline (10 words max)
   - Supporting text or bullet points (minimal — this is a keynote, not a document)
   - Speaker notes (200-300 words with the full talking points, transitions, and delivery cues)

4. **Apply brand identity**: Use Pedro Valera Digital brand colors, typography, and visual style rules. Dark backgrounds, Montserrat headings, Inter body text, blue accents, green gradient for hero text.

5. **Create the file**: Build the .pptx presentation file using the pptx skill. Aim for 12-20 slides.

6. **Quality check**: Verify against the Content Quality Checklist from the storytelling framework. Ensure every slide has one clear idea, the narrative arc is compelling, and the CTA is singular and specific.
