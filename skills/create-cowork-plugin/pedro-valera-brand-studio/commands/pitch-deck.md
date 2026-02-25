---
description: Create a branded pitch deck
argument-hint: [topic, product, or company]
allowed-tools: Read, Write, Edit, Bash
---

Create a branded pitch deck for: $ARGUMENTS

Before starting, read these skill files for guidance:
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md
- @${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md
- @${CLAUDE_PLUGIN_ROOT}/skills/storytelling-framework/SKILL.md

Follow these steps:

1. **Understand the context**: Ask the user (if not already provided):
   - Who is the audience? (Investors, potential partners, clients?)
   - What is the specific ask? (Funding, partnership, sales?)

2. **Structure with IGNITE + Pitch Logic**: Combine the IGNITE storytelling arc with standard pitch deck structure:
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

4. **Apply brand identity**: Pedro Valera Digital colors and style. Data visualizations in blue/green palette. Clean charts, alternating row shading for tables.

5. **Create the file**: Build the .pptx file using the pptx skill. Aim for 10-15 slides.

6. **Quality check**: Read every slide headline in sequence — do they tell a compelling story on their own? Verify data consistency and ensure the ask is clear and specific.
