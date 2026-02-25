---
description: Create a branded PDF guide or workbook
argument-hint: [topic or title]
allowed-tools: Read, Write, Edit, Bash
---

Create a branded PDF guide on the topic: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/storytelling-framework/SKILL.md

Follow these steps:

1. **Understand the topic**: If the topic is vague, ask:
   - Who is the specific audience?
   - Is this a guide (educational), workbook (interactive), or reference document?
   If the user already provided this context, skip asking.

2. **Outline using IGNITE**: Even PDFs follow the IGNITE narrative arc:
   - Introduction identifies the pain and sets stakes
   - Body narrates the turning point and introduces the solution
   - Evidence sections build trust through proof
   - Conclusion empowers with a clear action step

3. **Write the content**:
   - Cover page with title, subtitle, and Pedro Valera Digital branding
   - Table of contents
   - Sections with clear headings, body text, callout boxes, and visuals
   - If a workbook: include fill-in templates, checklists, or reflection questions
   - Summary with key takeaways
   - About/Contact page with CTA

4. **Apply brand identity**: Dark header/footer bars, #111827 callout boxes with #2563EB left borders, green gradient for pull quotes, Montserrat headings, Inter body text.

5. **Create the file**: Build the PDF using the pdf skill. Use Letter or A4 size with generous margins.

6. **Quality check**: Verify the document flows logically, every section adds value, and there is one clear CTA. Check brand consistency across all pages.
