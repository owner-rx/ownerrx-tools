---
description: Create a branded PDF guide or workbook
argument-hint: [topic or title]
allowed-tools: Read, Write, Edit, Bash
---

Create a branded PDF guide on the topic: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/SKILL.md

Follow these steps:

1. **Understand the topic**: If the topic is vague, ask:
   - Who is the specific audience?
   - Is this a guide (educational), workbook (interactive), or reference document?
   If the user already provided this context, skip asking.

2. **Choose the style**: Ask the user using AskUserQuestion:
   - **Dark mode** — ~~primary-bg backgrounds with light text. Bold, modern, digital-first. Best for on-screen reading and PDF sharing.
   - **Light mode** — White/light backgrounds with dark text. Clean, classic, print-friendly. Best for printed guides and professional documents.

3. **Choose the framework**: Ask or infer:
   - Educational guide or workbook → EDUCATE
   - Sales/marketing guide → IGNITE
   - Data report or analysis → REPORT

3. **Outline using the chosen framework**: Map the framework stages to document sections:
   - Introduction sets context and hooks the reader
   - Body delivers value through the chosen framework arc
   - Conclusion drives to action

4. **Write the content**:
   - Cover page with title, subtitle, and ~~brand-name branding
   - Table of contents
   - Sections with clear headings, body text, callout boxes, and visuals
   - If a workbook: include fill-in templates, checklists, or reflection questions
   - Summary with key takeaways
   - About/Contact page with CTA

6. **Apply brand identity**: Apply colors according to the chosen style mode (Dark or Light). See brand-identity SKILL.md for color application rules per mode. Use ~~heading-font headings, ~~body-font body text, ~~accent-primary for callout borders.

7. **Create the file**: Build the PDF. Use Letter or A4 size with generous margins.

8. **Quality check**: Verify the document flows logically, every section adds value, and there is one clear CTA. Check brand consistency across all pages.
