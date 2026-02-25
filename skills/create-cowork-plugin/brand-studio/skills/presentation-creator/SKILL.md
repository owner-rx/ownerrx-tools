---
name: presentation-creator
description: >
  Master skill for creating branded content. This skill should be used
  when the user asks to "create a presentation", "make slides", "build a deck",
  "design a PDF", "make a one-pager", "create content", "prepare a talk",
  "build a keynote", "make a workshop deck", "create a pitch deck",
  "design a document", "create a report", "build a training",
  or any general request to produce branded deliverables.
  Use this as the primary orchestration skill that coordinates brand identity
  and content frameworks into polished, professional outputs.
version: 0.1.0
---

# Presentation Creator — Master Skill

Central orchestration skill for creating branded content. Unifies brand identity, content frameworks, and design best practices into a single workflow.

## How This Skill Works

When creating any deliverable, always follow this sequence:

1. **Load brand rules** — Read `${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/SKILL.md` and applicable references
2. **Load content frameworks** — Read `${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/SKILL.md`
3. **Select framework** — Choose IGNITE, EDUCATE, or REPORT based on content type
4. **Determine content type** — Match the request to the right format (keynote, workshop, PDF, pitch deck, one-pager, report)
5. **Apply the Master Creation Process** below
6. **Quality assurance** — Run the final checks

## Master Creation Process

### Step 1: Discovery

Before creating anything, confirm:
- **Topic**: What is the subject matter?
- **Format**: Keynote, workshop, PDF, pitch deck, one-pager, or report?
- **Framework**: IGNITE (persuade), EDUCATE (teach), or REPORT (inform)?
- **Style**: Dark or Light mode? (see Style Selection below)
- **Audience**: Who will see this? (Default: ~~target-audience)
- **Goal**: What should the audience think, feel, or do after consuming this?
- **Length**: How many slides or pages? (Use format defaults if not specified)

If the user already provided this information, skip asking and proceed.

**Style Selection** — Always ask the user using AskUserQuestion before creating:

- **Dark mode** — ~~primary-bg backgrounds with light text. Bold, modern, high-contrast. Best for stage presentations, tech topics, and making visuals pop.
- **Light mode** — White/light backgrounds with dark text. Clean, professional, classic. Best for printed materials, corporate settings, and readability in bright rooms.

The user's choice determines how all brand colors are applied:
- **Dark mode**: ~~primary-bg as background, ~~text-primary for headings, ~~text-secondary for body
- **Light mode**: White (#FFFFFF) or very light neutral as background, ~~primary-bg for headings and text, ~~accent-primary for highlights. Invert the color roles — dark colors become text, light colors become backgrounds.

**Framework auto-selection** (if user doesn't specify):

| Content type | Default framework |
|---|---|
| Keynote, pitch deck, sales | IGNITE |
| Workshop, training, tutorial, onboarding | EDUCATE |
| Data review, quarterly report, dashboard, analysis | REPORT |
| PDF guide, one-pager | Ask user or infer from context |

### Step 2: Narrative Architecture

Map the selected framework to the chosen format:

**IGNITE → Format Mapping**:

| IGNITE Stage | Keynote | Workshop | PDF Guide | Pitch Deck | One-Pager |
|---|---|---|---|---|---|
| Identify Pain | Opening hook | Welcome + problem | Introduction | Problem slide | Hero headline |
| Gauge Stakes | Stakes slide | Why this matters | Context section | Market opportunity | Supporting stat |
| Narrate Turning Point | "What if..." bridge | Icebreaker + insight | Story/case study | Solution reveal | — (compressed) |
| Introduce Solution | Solution slides | Teaching sections | Core content | Product walkthrough | 3 key points |
| Trust Through Proof | Evidence slides | Exercises + results | Data/testimonials | Traction slide | Testimonial/metric |
| Empower with Action | CTA slide | Next steps + resources | Summary + CTA | The ask | CTA footer |

**EDUCATE → Format Mapping**:

| EDUCATE Stage | Workshop | PDF Guide | Keynote | One-Pager |
|---|---|---|---|---|
| Establish Baseline | Self-assessment | "Where are you now?" | Audience poll | — (skip) |
| Define Objectives | Learning goals slide | "What you'll learn" | Agenda slide | Headline |
| Unpack Concepts | Teaching slides | Content sections | Concept slides | 3 key points |
| Connect to Practice | Exercise slides | Worksheets | Demo/example | — (compressed) |
| Assess Understanding | Q&A / quiz | Review questions | Reflection | — (skip) |
| Transfer to Real World | Action items | Checklists | "Monday morning" | CTA |
| Extend Further | Resources slide | Next steps | Resources | Footer link |

**REPORT → Format Mapping**:

| REPORT Stage | PDF Report | Keynote | One-Pager | Pitch Deck |
|---|---|---|---|---|
| Research Context | Introduction | Context slide | — (skip) | Problem slide |
| Executive Summary | Page 1 summary | Key findings slide | Hero section | Solution slide |
| Present the Data | Charts + tables | Data slides | Key metric | Traction slide |
| Observe Patterns | Analysis sections | Insight slides | — (compressed) | Market slide |
| Recommend Actions | Recommendations | Action slides | 3 key points | Ask slide |
| Track Next Steps | KPI table | Next steps slide | CTA | Contact slide |

### Step 3: Content Creation

Write all content following these universal rules:

**Headlines**:
- Lead with outcomes, not features
- Use active verbs and specific numbers
- Max 10 words for presentations, 8 words for one-pagers

**Body Text**:
- Use "you" more than "we" — the audience is the hero
- One idea per slide or section
- Back every claim with data or a story
- Plain language — no jargon, no buzzwords
- Action-oriented: every section drives toward a next step

**Calls to Action**:
- ONE CTA per deliverable — never multiple
- Make it specific and low-friction
- Restate the transformation: "In 30 days, you could be [outcome]"

### Step 4: Visual Design

Apply brand identity to every element:

**Color Application**:
- Background: ~~primary-bg (primary) or ~~secondary-bg (cards/sections)
- Headlines: ~~text-primary
- Body text: ~~text-secondary
- Key highlights & CTAs: ~~accent-primary
- Hero text & value props: gradient (~~gradient-start → ~~accent-secondary)
- Accent elements: use sparingly

**Typography**:
- Headlines: ~~heading-font Bold (700)
- Body: ~~body-font Regular (400)
- Stats/numbers: ~~heading-font ExtraBold (800)
- Never more than 2 font families

**Layout**:
- Generous whitespace — when in doubt, remove elements
- Left-aligned text (center only for hero slides)
- Consistent margins and grid alignment
- ~~visual-style throughout

### Step 5: Quality Assurance

Before delivering, verify every item:

**Narrative Check**:
- [ ] Opens with a hook that creates curiosity or establishes relevance
- [ ] Follows the chosen framework arc consistently
- [ ] Contains at least one story, example, or case study
- [ ] Has exactly ONE clear call to action
- [ ] Every section earns its place — no filler

**Brand Check**:
- [ ] Correct background colors (~~primary-bg / ~~secondary-bg)
- [ ] Correct fonts (~~heading-font headings, ~~body-font body)
- [ ] ~~accent-primary used only for CTAs and key highlights
- [ ] Gradient used only for hero text / value propositions
- [ ] Visual style is consistent with ~~visual-style direction

**Audience Check**:
- [ ] Uses "you" language — audience is the hero
- [ ] Leads with outcomes, not features
- [ ] Respects their time — concise and direct
- [ ] Backed by data or social proof
- [ ] Tone matches: ~~tone

## Format-Specific Guidelines

Read `${CLAUDE_PLUGIN_ROOT}/skills/brand-identity/references/deliverable-specs.md` for slide counts, section structures, and design rules per format.

## Advanced Techniques

Load framework-specific references as needed:
- **IGNITE**: `${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/references/advanced-persuasion.md`
- **EDUCATE**: `${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/references/educational-patterns.md`
- **REPORT**: `${CLAUDE_PLUGIN_ROOT}/skills/content-frameworks/references/report-patterns.md`

## Quick-Start Templates

### Keynote (IGNITE) in 60 Seconds
> Topic → Hook question → Scary stat → "But what if..." → 3-step solution → Case study → CTA

### Workshop (EDUCATE) in 60 Seconds
> Learning objective → Baseline check → Teach → Exercise → Debrief → Repeat → Takeaways → Resources

### Pitch Deck (IGNITE) in 60 Seconds
> Problem → Market size → Solution → How it works → Traction → Team → The ask

### Report (REPORT) in 60 Seconds
> Context → Executive summary → Key charts → Pattern analysis → Recommendations → KPI tracker

### One-Pager in 60 Seconds
> Pain headline → 3 benefits with icons → One killer metric → CTA
