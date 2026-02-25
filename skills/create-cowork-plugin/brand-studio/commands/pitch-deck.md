---
description: Create a branded pitch deck
argument-hint: [topic, product, or company]
allowed-tools: Read, Write, Edit, Bash
---

Create a branded pitch deck for: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/SKILL.md

Follow these steps:

1. **Understand the context**: Ask the user (if not already provided):
   - Who is the audience? (Investors, potential partners, clients?)
   - What is the specific ask? (Funding, partnership, sales?)

2. **Choose the style**: Ask the user using AskUserQuestion:
   - **Dark mode** — ~~primary-bg backgrounds with light text. Bold, modern, high-impact. Best for investor meetings and screen presentations.
   - **Light mode** — White/light backgrounds with dark text. Clean, professional, classic. Best for printed decks and email attachments.

3. **Structure with IGNITE + Pitch Logic**: Pitch decks always use IGNITE. Combine the storytelling arc with standard pitch deck structure:
   - Title Slide
   - Problem (IGNITE: Identify the Pain)
   - Market Opportunity (IGNITE: Gauge the Stakes)
   - Solution (IGNITE: Narrate Turning Point + Introduce Solution)
   - Product/Service walkthrough
   - Traction & Proof (IGNITE: Trust Through Proof)
   - Business Model
   - Competition / Positioning
   - Team (if applicable)
   - Financial Projections (if applicable)
   - The Ask (IGNITE: Empower with Action)
   - Contact / Thank You

3. **Write the content**: Every slide must:
   - Have a headline that tells the story even without a presenter
   - Include concrete data where possible (metrics, percentages, dollar figures)
   - Be visually clean — no text walls

5. **Apply brand identity**: ~~brand-name colors and style according to the chosen style mode (Dark or Light). Data visualizations in ~~accent-primary / ~~accent-secondary palette. Clean charts.

6. **Create the file**: Build the .pptx file. Aim for 10-15 slides.

7. **Quality check**: Read every slide headline in sequence — do they tell a compelling story on their own? Verify data consistency and ensure the ask is clear and specific.
