---
name: brand-identity
description: >
  This skill should be used when the user asks to "create a presentation",
  "make a slide deck", "design a PDF", "build a pitch deck", "create a one-pager",
  "make a workshop", "create a keynote", "build a document", or any request
  involving branded content creation. Also use when the user mentions "brand colors",
  "brand style", "brand guidelines", "on-brand", or asks about visual identity.
version: 0.1.0
---

# ~~brand-name Brand Identity System

All content created for ~~brand-name must follow these brand guidelines to maintain a consistent, professional identity across every deliverable.

## Brand Color Palette

### Primary Colors

| Role | Hex Code | Usage |
|------|----------|-------|
| Background (Primary) | ~~primary-bg | Primary backgrounds, slide backgrounds, dark sections |
| Background (Secondary) | ~~secondary-bg | Card backgrounds, secondary sections, containers |
| Accent (Primary) | ~~accent-primary | Buttons, CTAs, links, key highlights, interactive elements |
| Accent (Secondary) | ~~accent-secondary | Success indicators, growth metrics, secondary highlights |
| Gradient Start | ~~gradient-start | Gradient text start, decorative elements |
| Text (Primary) | ~~text-primary | Headings, titles, primary text |
| Text (Secondary) | ~~text-secondary | Body text, descriptions, secondary information |
| Text (Tertiary) | ~~text-tertiary | Captions, footnotes, subtle text |

### Gradient Definitions

- **Hero Gradient (text)**: linear-gradient from ~~gradient-start to ~~accent-secondary — use for impactful headlines and key phrases
- **Background Gradient**: subtle radial gradient from ~~secondary-bg center to ~~primary-bg edges — adds depth to backgrounds
- **CTA Gradient**: ~~accent-primary darkened 10% — for buttons and call-to-action elements

### Color Application by Style Mode

**Dark Mode** (~~primary-bg backgrounds):
- ~~primary-bg as the base background
- ~~text-primary for headings and titles
- ~~text-secondary for body text
- ~~accent-primary for CTAs and key highlights
- Gradient text (~~gradient-start → ~~accent-secondary) for hero headlines
- Image overlays using ~~primary-bg at 60-80% opacity

**Light Mode** (white/light backgrounds):
- #FFFFFF or a very light neutral as the base background
- ~~primary-bg for headings and titles (dark text on light bg)
- A darkened neutral for body text
- ~~accent-primary for CTAs, highlights, and interactive elements
- ~~accent-secondary for decorative accents and success indicators
- Image overlays using white at 40-60% opacity

**Both Modes**:
- Reserve ~~accent-primary for interactive elements and key highlights only — do not overuse
- Maintain a contrast ratio of at least 4.5:1 for all text
- **Always ask the user** whether they prefer Dark or Light mode before creating any deliverable

## Typography

### Font Stack

| Role | Font | Weight | Size Guidelines |
|------|------|--------|----------------|
| Headlines / Titles | ~~heading-font | Bold (700) | 36-60pt presentations, 24-36pt documents |
| Subheadings | ~~heading-font | SemiBold (600) | 24-32pt presentations, 18-24pt documents |
| Body Text | ~~body-font | Regular (400) | 18-22pt presentations, 11-12pt documents |
| Captions / Labels | ~~body-font | Medium (500) | 14-16pt presentations, 9-10pt documents |
| Accent Text / Stats | ~~heading-font | ExtraBold (800) | For big numbers, key statistics |

### Typography Rules

- Headlines: Title Case for standard headings
- Keep line spacing at 1.4x–1.6x for body text
- Maximum 2 font families per deliverable (~~heading-font + ~~body-font)
- Never use decorative or script fonts
- For presentations: limit text to 6 words per line, 6 lines per slide (the 6x6 rule)

## Visual Style

### Layout Principles

- **Clean and spacious**: generous whitespace, avoid clutter
- **Left-aligned text**: default alignment for readability (center-align only for hero slides)
- **Grid-based layouts**: use consistent margins and alignment guides
- **Design direction**: ~~visual-style

### Graphic Elements

- Use subtle geometric shapes (circles, lines, rounded rectangles) as decorative accents
- Gradient overlays on images: semi-transparent ~~primary-bg at 60-80% opacity
- Icon style: outlined/linear icons, not filled — modern and clean
- Border radius: 8-12px for cards and containers (rounded but not pill-shaped)
- Subtle glow effects around key elements using ~~accent-primary at low opacity

### Image Treatment

- All images should have an overlay using ~~primary-bg to maintain brand consistency
- Prefer professional photography or clean illustrations relevant to the business
- Avoid clip art, stock photos that look staged, or overly generic imagery

## Content Tone

~~tone

- **Empowering**: position the audience as capable decision-makers
- **Action-oriented**: every section should drive toward a clear next step
- **Data-informed**: back claims with statistics, metrics, and real outcomes

## Target Audience

Default audience: ~~target-audience

## Deliverable-Specific Guidelines

Read `references/deliverable-specs.md` for detailed specifications for each content type (presentations, PDFs, one-pagers, pitch decks).
