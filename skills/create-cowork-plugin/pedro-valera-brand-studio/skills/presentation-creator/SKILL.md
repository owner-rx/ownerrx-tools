---
name: presentation-creator
description: >
  Master skill for creating any Pedro Valera Digital branded content. This skill should be used
  when the user asks to "create a presentation", "make slides", "build a deck",
  "design a PDF", "make a one-pager", "create content", "prepare a talk",
  "build a keynote", "make a workshop deck", "create a pitch deck",
  "design a document", or any general request to produce branded deliverables.
  Use this as the primary orchestration skill that coordinates brand identity
  and storytelling framework into polished, professional outputs.
version: 0.1.0
---

# Pedro Valera Digital Presentation Creator — Master Skill

This is the central orchestration skill for creating any Pedro Valera Digital branded content. It unifies brand identity, storytelling psychology, and presentation design best practices into a single workflow.

## How This Skill Works

When creating any deliverable, always follow this sequence:

1. **Load brand rules** — Read `${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md` and applicable references
2. **Load storytelling framework** — Read `${CLAUDE_PLUGIN_ROOT}/skills/storytelling-framework/SKILL.md`
3. **Determine content type** — Match the request to the right format (keynote, workshop, PDF, pitch deck, one-pager)
4. **Apply the Master Creation Process** below
5. **Quality assurance** — Run the final checks

## Master Creation Process

### Step 1: Discovery (30 seconds)

Before creating anything, confirm:
- **Topic**: What is the subject matter?
- **Format**: Keynote, workshop, PDF, pitch deck, or one-pager?
- **Audience**: Who will see this? (Default: entrepreneurs & small business owners)
- **Goal**: What should the audience think, feel, or do after consuming this?
- **Length**: How many slides or pages? (Use format defaults if not specified)

If the user already provided this information, skip asking and proceed.

### Step 2: Narrative Architecture

Map the IGNITE framework to the chosen format:

| IGNITE Stage | Keynote | Workshop | PDF Guide | Pitch Deck | One-Pager |
|-------------|---------|----------|-----------|------------|-----------|
| **I**dentify Pain | Opening hook slide | Welcome + problem framing | Introduction | Problem slide | Hero headline |
| **G**auge Stakes | Stakes slide | Why this matters | Context section | Market opportunity | Supporting stat |
| **N**arrate Turning Point | "What if..." bridge | Icebreaker + key insight | Story/case study | Solution reveal | — (compressed) |
| **I**ntroduce Solution | Solution slides | Teaching sections | Core content | Product walkthrough | 3 key points |
| **T**rust Through Proof | Evidence slides | Exercises + results | Data/testimonials | Traction slide | Testimonial/metric |
| **E**mpower with Action | CTA slide | Next steps + resources | Summary + CTA | The ask | CTA footer |

### Step 3: Content Creation

Write all content following these universal rules:

**Headlines**:
- Lead with outcomes, not features
- Use active verbs and specific numbers
- Max 10 words for presentations, 8 words for one-pagers
- Ask: "Would an entrepreneur stop scrolling for this headline?"

**Body Text**:
- Use "you" more than "we" — the audience is the hero
- One idea per slide or section
- Back every claim with data or a story
- Plain language — no jargon, no buzzwords
- Action-oriented: every section drives toward a next step

**Data & Proof**:
- Always include at least one statistic, case study, or testimonial
- Visualize data when possible — charts over tables, icons over bullet points
- Source your data (even informally: "According to [source]...")

**Calls to Action**:
- ONE CTA per deliverable — never multiple
- Make it specific: "Book a 15-minute call" not "Get in touch"
- Make it low-friction: small first step, not a big commitment
- Restate the transformation: "In 30 days, you could be [outcome]"

### Step 4: Visual Design

Apply brand identity to every element:

**Color Application**:
- Background: #0A0F1C (primary) or #111827 (secondary/cards)
- Headlines: #FFFFFF (white)
- Body text: #D1D5DB (light gray)
- Key highlights & CTAs: #2563EB (bright blue)
- Hero text & value props: green gradient (#4ADE80 → #22C55E)
- Accent elements: use sparingly — less is more

**Typography**:
- Headlines: Montserrat Bold (700)
- Body: Inter Regular (400)
- Stats/numbers: Montserrat ExtraBold (800)
- Never more than 2 font families

**Layout**:
- Generous whitespace — when in doubt, remove elements
- Left-aligned text (center only for hero slides)
- Consistent margins and grid alignment
- Dark-first aesthetic throughout

### Step 5: Quality Assurance

Before delivering, verify every item:

**Narrative Check**:
- [ ] Opens with a hook that creates curiosity or names a pain point
- [ ] Follows IGNITE arc (even if some stages are compressed)
- [ ] Contains at least one story, example, or case study
- [ ] Has exactly ONE clear call to action
- [ ] Every section earns its place — no filler

**Brand Check**:
- [ ] Dark backgrounds throughout (#0A0F1C / #111827)
- [ ] Correct fonts (Montserrat headings, Inter body)
- [ ] Blue (#2563EB) used only for CTAs and key highlights
- [ ] Green gradient used only for hero text / value propositions
- [ ] Visual style is clean, spacious, and modern

**Audience Check**:
- [ ] Uses "you" language — audience is the hero
- [ ] Leads with outcomes, not features
- [ ] Respects their time — concise and direct
- [ ] Backed by data or social proof
- [ ] Tone is confident but approachable

## Format-Specific Guidelines

For detailed specifications on slide counts, section structures, and design rules for each format, read:
`${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md`

## Persuasion Techniques

For advanced persuasion techniques (contrast principle, anchoring, open loops, micro-commitments, peak-end rule, and more), read:
`${CLAUDE_PLUGIN_ROOT}/skills/storytelling-framework/references/advanced-persuasion.md`

## Quick-Start Templates

### Keynote in 60 Seconds
> Topic → Hook question → Scary stat → "But what if..." → 3-step solution → Case study → CTA

### Workshop in 60 Seconds
> Learning objective → Icebreaker → Teach → Exercise → Debrief → Repeat → Takeaways → Resources

### Pitch Deck in 60 Seconds
> Problem → Market size → Solution → How it works → Traction → Team → The ask

### One-Pager in 60 Seconds
> Pain headline → 3 benefits with icons → One killer metric → CTA
