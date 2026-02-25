---
description: Create a branded one-pager
argument-hint: [topic, product, or service]
allowed-tools: Read, Write, Edit, Bash
---

Create a branded one-pager for: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/SKILL.md

Follow these steps:

1. **Understand the context**: Ask the user (if not already provided):
   - What is this one-pager for? (Product overview, event summary, executive brief, service offering, data snapshot?)
   - Who will read it?

2. **Choose the style**: Ask the user using AskUserQuestion:
   - **Dark mode** — ~~primary-bg background with light text. Bold, modern, eye-catching. Best for digital sharing and screen viewing.
   - **Light mode** — White/light background with dark text. Clean, professional, print-ready. Best for handouts and email attachments.

3. **Choose the framework**: Ask or infer:
   - Sales/marketing one-pager → IGNITE (compressed)
   - Data snapshot or KPI summary → REPORT (compressed)
   - Training summary or quick-reference → EDUCATE (compressed)

3. **Apply the framework in compressed form**: Even a single page follows the narrative arc:
   - Header: Hook headline that establishes relevance
   - Body: Core value + proof in 3 key points
   - Footer: Single CTA

4. **Write the content**:
   - **Header**: Logo, title (max 8 words), date or version
   - **Hero section**: One powerful headline + 1-2 sentence hook
   - **3 Key Points**: Icon-worthy points with 2-3 sentence descriptions each
   - **Supporting data**: One compelling metric, chart, or testimonial
   - **CTA**: Clear next step with contact information
   - **Footer**: Brand details, website, social

6. **Apply brand identity**: Apply ~~brand-name colors according to the chosen style mode (Dark or Light). ~~heading-font headings, ~~body-font body. Two or three-column layout for easy scanning. Every element must earn its space.

7. **Create the file**: Build as a PDF. Single page, Letter or A4.

8. **Quality check**: Can someone understand the key message in under 30 seconds? Is the CTA unmissable? Is every word essential?
