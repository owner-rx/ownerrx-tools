# search-visibility-optimizer Design

## Goal

A single Claude Code skill that audits any webpage for traditional search engine and AI search visibility, scores it against a research-backed framework, compares it against competitors, and generates ready-to-use fixes.

Replaces two existing skills: `geo-aeo-optimizer` (working but limited to Princeton 2024 research) and `seo-audit` (mostly stubs). Combines the best of both with new research from GEO-16 (2025) and E-E-A-T citation studies.

## Architecture

Three-phase workflow: Extract, Score, Fix.

**Extract** fetches target and competitor pages, detects available MCP servers, and collects data from all sources — HTML analysis, Lighthouse MCP, Google Search Console MCP.

**Score** applies a merged 6-category framework with sub-scores derived from Princeton GEO, GEO-16, and E-E-A-T research. Produces a report card with competitor comparison.

**Fix** generates the actual corrections: rewritten content sections, missing schema markup as JSON-LD, llms.txt files, robots.txt corrections, Next.js metadata exports. Output is ready to paste or commit.

## Scoring Framework

Six top-level categories. Each contains research-backed sub-scores.

### 1. Authority & Citability (30%) — Princeton GEO + E-E-A-T

- Statistics count (with and without inline sources)
- Citation count (named sources vs. "research shows")
- Expert quotations (named and titled people)
- E-E-A-T signals: author bio, credentials, Person schema with `sameAs`/`knowsAbout`, publication history
- Earned media indicators: external high-authority mentions, Wikipedia/Wikidata entity presence

### 2. Content Structure (20%) — GEO-16 + Princeton

- Heading hierarchy (single H1, logical H2/H3)
- Answer-first pattern (does each section open with a direct answer?)
- Self-contained paragraphs (each extractable by AI chunking)
- FAQ presence and schema
- Lists and tables (32% of AI citations are listicles)
- Content length per section (no walls, no thin sections)

### 3. Entity Clarity (15%) — GEO-16 + E-E-A-T

- Named entities vs. vague claims ratio
- Brand entity definition (Organization schema, `sameAs` to LinkedIn/Wikidata)
- Author entity definition (Person schema with credentials)
- Product/Service entities explicitly named

### 4. Technical Foundation (20%) — Traditional SEO + GEO-16

- SSR detection (show-stopper if false)
- Core Web Vitals: LCP, INP, CLS (Lighthouse MCP when available, heuristic otherwise)
- SSL, redirects, security headers
- Schema markup completeness (Article, FAQPage, Organization, BreadcrumbList, HowTo)
- Meta tags (title, description, OG, Twitter cards, canonical)

### 5. AI Crawlability (10%) — New category

- robots.txt: training bots (GPTBot, ClaudeBot) vs. citation bots (ChatGPT-User, PerplexityBot) with distinct recommendations
- llms.txt: present? well-structured? includes best content?
- AI bot response headers (X-Robots-Tag)
- Sitemap accessibility

### 6. Freshness (5%) — GEO-16

- `datePublished` / `dateModified` in schema
- `Last-Modified` HTTP header / ETag
- Content recency signals in text
- Sitemap lastmod dates

**Composite:** weighted sum of all six categories. Anti-patterns (keyword stuffing, unsourced claims) applied as deductions within each relevant category rather than scored separately.

## MCP Detection & Graceful Degradation

The skill detects which MCP servers are configured at runtime and adapts.

### Tier 1 — Always available (no MCP needed)

- `analyze_page.py` via WebFetch + HTML parsing
- `technical_checker.py` via HTTP requests
- `discover_competitors.py` via OpenAI API (when user provides key)
- llms.txt and robots.txt fetched directly

### Tier 2 — Free MCP integrations

- **Lighthouse MCP** — replaces heuristic CWV scores with measured LCP, INP, CLS. Adds performance score, unused JS detection, resource analysis.
- **Google Search Console MCP** — actual search performance data, indexing status, click/impression data.

Detection: the SKILL.md workflow instructs Claude to check its available tools for Lighthouse and GSC MCP tools. The report notes which scores used measured data vs. heuristics.

## Fix Generation

Three categories of output.

### Code fixes

- Missing schema markup as JSON-LD components (FAQSchema, ArticleSchema, Person schema with placeholder credentials)
- Next.js `metadata` export corrections (title length, description, OG tags)
- `llms.txt` file generated from sitemap + page analysis
- `robots.txt` corrections (allow citation bots, optionally block training bots with trade-off explanation)

### Content rewrites

- Identifies 3-5 weakest-scoring sections
- For each: Original text, Rewritten text, What changed and why
- Applies Princeton techniques: statistics addition, citation addition, quotation addition
- Marks placeholders clearly ("REPLACE: add real customer quote here")
- Preserves the owner's voice

### Action plan

- Prioritized checklist for items the skill cannot auto-generate
- Grouped by High Impact (this week) / Medium (this month) / Low (later)
- Plain language, with "Ask your developer to..." when technical
- Links E-E-A-T gaps to specific actions

The skill presents all fixes. It does not auto-commit or auto-edit files.

## File Structure

```
ownerrx-skills/
├── search-visibility-optimizer/
│   ├── SKILL.md
│   ├── requirements.txt
│   ├── scripts/
│   │   ├── analyze_page.py
│   │   ├── technical_checker.py
│   │   ├── discover_competitors.py
│   │   ├── fix_generator.py
│   │   └── tests/
│   │       ├── test_analyze_page.py
│   │       ├── test_technical_checker.py
│   │       ├── test_discover_competitors.py
│   │       └── test_fix_generator.py
│   └── references/
│       ├── scoring-rubric.md
│       ├── princeton-techniques.md
│       ├── geo16-mapping.md
│       └── eeat-checklist.md
├── geo-aeo-optimizer/              # Deprecated after verification
└── README.md
```

### Script responsibilities

**analyze_page.py** — Expanded from current `geo-aeo-optimizer` version. Adds E-E-A-T extraction (author bios, credentials, Person schema), llms.txt detection, AI crawler analysis (training vs. citation bot distinction), answer-first pattern detection, earned media signal detection.

**technical_checker.py** — Absorbed from `seo-audit`. Checks SSL configuration, redirect chains, security headers, HTTP response time, compression, cache headers. Uses corrected Core Web Vitals thresholds (INP replaces FID).

**discover_competitors.py** — Unchanged from current. Queries LLMs to find which brands AI recommends in the user's niche.

**fix_generator.py** — New. Takes the scoring JSON and generates: schema JSON-LD components, llms.txt content, robots.txt corrections, content rewrites using Princeton techniques.

### Reference documents

**scoring-rubric.md** — Expanded to 6 categories with sub-scores, weights, and deductions. Maps each sub-score to its research basis.

**princeton-techniques.md** — Unchanged. Statistics addition, citation addition, quotation addition.

**geo16-mapping.md** — New. Maps the 16 GEO-16 pillars to the 6 top-level categories. Documents the empirical findings: metadata + semantic HTML + structured data predict citations; G >= 0.70 yields 4.2x citation improvement.

**eeat-checklist.md** — New. E-E-A-T signals by platform. ChatGPT values academic credentials. Claude values methodology transparency. Perplexity rewards freshness + credentials. Gemini leverages Google ecosystem signals. Lists specific schema properties to check.

### Dependencies

`beautifulsoup4` and `requests` (same as current). `openai` optional for competitor discovery.

## SKILL.md Workflow

### Step 1: Collect Input

Ask for target URL (or pasted content as fallback). Ask for competitor URLs or offer auto-discovery.

### Step 2: Detect MCP Capabilities

Check available tools for Lighthouse and GSC MCP. Report findings to user.

### Step 3: Extract Data

For each URL: WebFetch HTML, fetch robots.txt, fetch llms.txt. Run `analyze_page.py`. Run `technical_checker.py`. If Lighthouse MCP available, run performance audit. If GSC MCP available, pull search performance. Merge results into one extraction JSON per page.

### Step 4: Score

Read `references/scoring-rubric.md`. Score each page across 6 categories with sub-scores. Calculate composite. Compare target vs. competitors.

### Step 5: Generate Report Card

Side-by-side comparison table. Sub-score breakdown below each category. Flag show-stoppers (SSR false, AI bots blocked).

### Step 6: Generate Fixes

Run `fix_generator.py`. Rewrite weakest content sections. Generate missing schema, llms.txt, robots.txt. Present as "Original → Fixed → Why."

### Step 7: Action Plan

Prioritized checklist of everything the skill cannot auto-generate. Plain language. High/Medium/Low impact grouping.

## What This Replaces

- **geo-aeo-optimizer** — all functionality absorbed and expanded. Deprecate after new skill is verified.
- **seo-audit** (`~/.claude/skills/seo-audit/`) — delete. Its only real code (`technical_seo_checker.py`) is absorbed into `technical_checker.py`. The rest was stubs.

## Research Basis

- Princeton GEO (KDD 2024, arxiv 2311.09735) — statistics +25.9%, citations +24.9%, quotations +27.8% visibility improvement
- GEO-16 Framework (September 2025, arxiv 2509.10762) — 16-pillar scoring, G >= 0.70 yields 4.2x citations, metadata + structured data strongest predictors
- GEO Follow-up (2025, arxiv 2509.08919) — AI search biased toward earned media over brand-owned content
- E-E-A-T Citation Research (Qwairy 2025) — 40% more citations with visible credentials, 82% of AI citations include structured data, platform-specific credential weighting
