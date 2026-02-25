# search-visibility-optimizer Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build the search-visibility-optimizer skill that replaces geo-aeo-optimizer and seo-audit with a single, research-backed tool that extracts, scores, and generates fixes for both traditional SEO and AI search visibility.

**Architecture:** Three-phase workflow (Extract → Score → Fix) with 4 Python scripts, 4 reference docs, 1 SKILL.md, and full test coverage. MCP-aware (Lighthouse, GSC) with graceful degradation.

**Tech Stack:** Python 3, BeautifulSoup4, requests, pytest. Optional: openai (competitor discovery).

**Working directory:** `/Users/alanpentz/Documents/GitHub/ownerrx-skills`

---

### Task 1: Create directory structure and foundation files

**Files:**
- Create: `search-visibility-optimizer/`
- Create: `search-visibility-optimizer/scripts/`
- Create: `search-visibility-optimizer/scripts/tests/`
- Create: `search-visibility-optimizer/references/`
- Create: `search-visibility-optimizer/requirements.txt`
- Copy: `geo-aeo-optimizer/scripts/discover_competitors.py` → `search-visibility-optimizer/scripts/discover_competitors.py`
- Copy: `geo-aeo-optimizer/scripts/test_discover_competitors.py` → `search-visibility-optimizer/scripts/tests/test_discover_competitors.py`
- Copy: `geo-aeo-optimizer/references/princeton-techniques.md` → `search-visibility-optimizer/references/princeton-techniques.md`

**Step 1: Create directories**

```bash
mkdir -p /Users/alanpentz/Documents/GitHub/ownerrx-skills/search-visibility-optimizer/scripts/tests
mkdir -p /Users/alanpentz/Documents/GitHub/ownerrx-skills/search-visibility-optimizer/references
```

**Step 2: Write requirements.txt**

```
beautifulsoup4>=4.12.0
requests>=2.31.0
```

**Step 3: Copy unchanged files**

```bash
cp geo-aeo-optimizer/scripts/discover_competitors.py search-visibility-optimizer/scripts/
cp geo-aeo-optimizer/scripts/test_discover_competitors.py search-visibility-optimizer/scripts/tests/
cp geo-aeo-optimizer/references/princeton-techniques.md search-visibility-optimizer/references/
```

**Step 4: Fix the import path in copied test file**

The test imports `from discover_competitors import ...`. Add a conftest.py to handle the path:

```python
# search-visibility-optimizer/scripts/tests/conftest.py
import sys
from pathlib import Path
sys.path.insert(0, str(Path(__file__).parent.parent))
```

**Step 5: Run copied tests to verify**

```bash
cd /Users/alanpentz/Documents/GitHub/ownerrx-skills/search-visibility-optimizer/scripts/tests
python3 -m pytest test_discover_competitors.py -v
```
Expected: All 6 tests pass.

**Step 6: Commit**

```bash
git add search-visibility-optimizer/
git commit -m "feat: scaffold search-visibility-optimizer with foundation files"
```

---

### Task 2: Write reference documents (scoring-rubric.md, geo16-mapping.md, eeat-checklist.md)

These are Markdown reference files that Claude reads during scoring and fix generation. They contain no code.

**Files:**
- Create: `search-visibility-optimizer/references/scoring-rubric.md`
- Create: `search-visibility-optimizer/references/geo16-mapping.md`
- Create: `search-visibility-optimizer/references/eeat-checklist.md`

**Step 1: Write scoring-rubric.md**

Expand the existing rubric from `geo-aeo-optimizer/references/scoring-rubric.md` to match the new 6-category framework from the design doc. Key changes:
- Rename "Technical Crawlability" → "Technical Foundation" (weight 15% → 20%)
- Rename "Entity Clarity" weight from 20% → 15%
- Remove "Anti-patterns" as standalone category (fold deductions into each category)
- Add new "AI Crawlability" category (10%)
- Add E-E-A-T sub-scores under Authority
- Add answer-first detection under Content Structure
- Add llms.txt, training vs citation bot distinction under AI Crawlability
- Update composite formula: `(authority * 0.30) + (structure * 0.20) + (technical * 0.20) + (entity * 0.15) + (ai_crawlability * 0.10) + (freshness * 0.05)`

Use the same table format as the existing rubric. Each category: Score Range | Criteria table, sub-score breakdowns, deductions.

**Step 2: Write geo16-mapping.md**

Map the 16 GEO-16 pillars (from arxiv 2509.10762) to the 6 top-level categories. Include:
- The 6 core GEO-16 principles: People-First Answers, Structured Data, Provenance, Freshness, Risk Management, RAG Fit
- Empirical findings: G >= 0.70 yields 4.2x citation improvement
- By-engine breakdown: Brave (78% citation rate), Google AI Overviews (72%), Perplexity (45%)
- Which pillars map to which of our 6 categories

**Step 3: Write eeat-checklist.md**

Platform-specific E-E-A-T signals. Include:
- ChatGPT: values academic credentials
- Claude: values methodology transparency
- Perplexity: rewards freshness + credentials
- Gemini: leverages Google ecosystem signals
- Schema properties to check: Person (`sameAs`, `knowsAbout`, `honorificSuffix`, `alumniOf`), Organization (`sameAs`), Article (`author`, `datePublished`, `dateModified`)
- 82% of AI citations include structured data (Qwairy 2025)
- 40% more citations with visible credentials

**Step 4: Commit**

```bash
git add search-visibility-optimizer/references/
git commit -m "feat: add expanded scoring rubric, GEO-16 mapping, and E-E-A-T checklist"
```

---

### Task 3: Build expanded analyze_page.py with tests

Expand the existing `geo-aeo-optimizer/scripts/analyze_page.py` with new extraction capabilities. This is the core data extraction script.

**Files:**
- Create: `search-visibility-optimizer/scripts/analyze_page.py`
- Create: `search-visibility-optimizer/scripts/tests/test_analyze_page.py`

**Starting point:** Copy from `geo-aeo-optimizer/scripts/analyze_page.py` (334 lines, 12 functions, all tested).

**New extractions to add:**

1. **E-E-A-T signals** — new `extract_eeat()` function:
   - Detect author bio patterns (look for "About the Author", "Written by", `rel="author"`, Person schema in JSON-LD)
   - Extract Person schema fields if present: `sameAs`, `knowsAbout`, `honorificSuffix`, `alumniOf`
   - Detect Organization schema fields: `sameAs` links count
   - Check for `datePublished`, `dateModified` in Article schema (not just meta tags)
   - Return: `{"author_bio_detected": bool, "person_schema": {...}, "org_schema": {...}, "credentials_visible": bool}`

2. **llms.txt detection** — add to `analyze_html()`:
   - New `--llms-txt` CLI arg for path to llms.txt file
   - Parse llms.txt: count entries, check if well-structured (has title, description, URLs)
   - Return: `{"llms_txt_exists": bool, "llms_txt_entries": int, "llms_txt_has_descriptions": bool}`

3. **AI crawler classification** — expand `extract_technical()`:
   - Split AI_BOTS into two lists: `TRAINING_BOTS = ["GPTBot", "ClaudeBot", "Google-Extended", "CCBot"]` and `CITATION_BOTS = ["ChatGPT-User", "PerplexityBot", "Claude-Web", "Amazonbot", "Bytespider"]`
   - Return separate blocked/allowed for each category
   - Return: `{"training_bots_blocked": [...], "training_bots_allowed": [...], "citation_bots_blocked": [...], "citation_bots_allowed": [...]}`

4. **Answer-first pattern detection** — expand `make_section()`:
   - Check if first sentence of each section is a direct answer (ends with period, under 30 words, contains a factual claim — has a number or named entity)
   - Return: `{"answer_first": bool}` per section

5. **X-Robots-Tag detection** — add to extraction:
   - New `--headers-json` CLI arg for HTTP response headers
   - Check for `X-Robots-Tag` header containing `noindex` or `nofollow`
   - Return: `{"x_robots_tag": str | null}`

**Step 1: Write failing tests for new extractions**

Write tests in `test_analyze_page.py`. Include all existing tests from `geo-aeo-optimizer/scripts/test_analyze_page.py` plus new ones:

```python
def test_eeat_person_schema_detected():
    html = """...<script type="application/ld+json">{"@type":"Person","name":"Jane Smith","sameAs":["https://linkedin.com/in/janesmith"],"knowsAbout":["AI","Business"]}</script>..."""
    result = analyze_html(html, "https://example.com")
    assert result["eeat"]["person_schema"]["name"] == "Jane Smith"
    assert len(result["eeat"]["person_schema"]["sameAs"]) == 1

def test_eeat_author_bio_detected():
    html = """...<h2>About the Author</h2><p>Jane Smith is a certified business coach with 15 years of experience.</p>..."""
    result = analyze_html(html, "https://example.com")
    assert result["eeat"]["author_bio_detected"] is True

def test_ai_crawler_classification():
    robots = "User-agent: GPTBot\nDisallow: /\n\nUser-agent: ChatGPT-User\nAllow: /"
    result = analyze_html(SAMPLE_HTML, "https://example.com", robots_txt=robots)
    assert "GPTBot" in result["technical"]["training_bots_blocked"]
    assert "ChatGPT-User" in result["technical"]["citation_bots_allowed"]

def test_answer_first_detection():
    html = """...<h2>What is GEO?</h2><p>GEO is a set of techniques that increase AI citation probability by 30-40%.</p>..."""
    result = analyze_html(html, "https://example.com")
    sections = result["content"]["sections"]
    geo_section = [s for s in sections if "GEO" in s["heading"]]
    assert geo_section[0]["answer_first"] is True

def test_llms_txt_parsing():
    result = analyze_html(SAMPLE_HTML, "https://example.com", llms_txt="# My Site\n> Description\n\n- [Page 1](https://example.com/page1): A useful page\n- [Page 2](https://example.com/page2): Another page\n")
    assert result["llms_txt"]["exists"] is True
    assert result["llms_txt"]["entries"] == 2
```

**Step 2: Run tests to verify they fail**

```bash
cd /Users/alanpentz/Documents/GitHub/ownerrx-skills/search-visibility-optimizer/scripts/tests
python3 -m pytest test_analyze_page.py -v
```
Expected: New tests FAIL, old tests pass.

**Step 3: Implement the new extractions**

Add the 5 new extraction functions/expansions to `analyze_page.py`. Keep the existing functions unchanged — only add new ones and expand the `analyze_html()` return dict.

**Step 4: Run all tests**

```bash
python3 -m pytest test_analyze_page.py -v
```
Expected: All tests pass (old + new).

**Step 5: Commit**

```bash
git add search-visibility-optimizer/scripts/analyze_page.py search-visibility-optimizer/scripts/tests/test_analyze_page.py
git commit -m "feat: expand analyze_page with E-E-A-T, llms.txt, AI crawler classification"
```

---

### Task 4: Build technical_checker.py with tests

New script absorbing the real code from `~/.claude/skills/seo-audit/scripts/technical_seo_checker.py`. Converts from class-based to function-based to match the project's style. Adds INP awareness.

**Files:**
- Create: `search-visibility-optimizer/scripts/technical_checker.py`
- Create: `search-visibility-optimizer/scripts/tests/test_technical_checker.py`

**What to absorb from seo-audit's TechnicalSEOChecker:**
- `check_ssl()` → `check_ssl(url)` — HTTPS, cert validity, HTTP→HTTPS redirect, mixed content
- `check_redirects()` → `check_redirects(url)` — redirect chain depth, final URL
- `check_security_headers()` → `check_security_headers(url)` — X-Frame-Options, HSTS, CSP, X-Content-Type-Options
- `check_page_speed_factors()` → `check_page_speed(url)` — response time, page size, compression, cache headers, ETag
- `check_sitemap()` → `check_sitemap(url)` — sitemap existence, URL count, valid XML, lastmod

**New additions:**
- `check_cwv_heuristic(html, response_time)` — estimates CWV from HTML analysis when Lighthouse MCP is unavailable:
  - LCP heuristic: check for hero image size, preload hints, critical CSS
  - INP heuristic: count JS script tags, check for defer/async, estimate interactive weight
  - CLS heuristic: check images without explicit width/height, check for injected content patterns
  - Return: `{"lcp_estimate": "good"|"needs_improvement"|"poor", "inp_risk": "low"|"medium"|"high", "cls_risk": "low"|"medium"|"high", "source": "heuristic"}`
  - Include correct 2024 thresholds: LCP <2.5s, INP <200ms, CLS <0.1

**Architecture:** All functions take a URL string and return a dict. A `check_all(url)` function runs everything and returns the combined result. CLI entry point: `python3 technical_checker.py <url>`.

**Step 1: Write failing tests**

Test against a mock HTTP server or use `responses` library to mock requests. For heuristic CWV tests, pass HTML directly.

```python
def test_cwv_heuristic_good_page():
    html = """<html><head><link rel="preload" as="image" href="hero.webp"><style>...</style></head>
    <body><img src="hero.webp" width="800" height="600"><script defer src="app.js"></script></body></html>"""
    result = check_cwv_heuristic(html, response_time=0.5)
    assert result["source"] == "heuristic"
    assert result["cls_risk"] == "low"  # images have dimensions

def test_cwv_heuristic_poor_page():
    html = """<html><body><img src="huge.jpg"><img src="banner.png"><script src="a.js"></script><script src="b.js"></script><script src="c.js"></script></body></html>"""
    result = check_cwv_heuristic(html, response_time=3.0)
    assert result["inp_risk"] in ("medium", "high")  # 3 sync scripts
    assert result["cls_risk"] in ("medium", "high")  # images without dimensions
```

**Step 2: Run tests to verify they fail**

```bash
python3 -m pytest test_technical_checker.py -v
```

**Step 3: Implement technical_checker.py**

Convert TechnicalSEOChecker methods to standalone functions. Add `check_cwv_heuristic()`. Use `requests` for HTTP checks with 10s timeout. Handle errors gracefully (return `{"error": str}` on failure, never crash).

**Step 4: Run all tests**

```bash
python3 -m pytest test_technical_checker.py -v
```

**Step 5: Commit**

```bash
git add search-visibility-optimizer/scripts/technical_checker.py search-visibility-optimizer/scripts/tests/test_technical_checker.py
git commit -m "feat: add technical_checker with SSL, redirects, headers, CWV heuristics"
```

---

### Task 5: Build fix_generator.py with tests

New script. Takes the extraction JSON from analyze_page.py and the scoring results, then generates ready-to-use fixes.

**Files:**
- Create: `search-visibility-optimizer/scripts/fix_generator.py`
- Create: `search-visibility-optimizer/scripts/tests/test_fix_generator.py`

**Functions to implement:**

1. `generate_schema_fixes(extraction)` → Returns list of JSON-LD schema objects to add:
   - If FAQPage schema missing but FAQ content detected → generate FAQPage JSON-LD from page content
   - If Organization schema missing → generate template with placeholders
   - If Person schema missing but author bio detected → generate Person JSON-LD with placeholders for `sameAs`, `knowsAbout`
   - If Article schema missing → generate with `datePublished`, `dateModified`, `author`
   - If BreadcrumbList missing → generate from URL path

2. `generate_llms_txt(extraction, sitemap_urls)` → Returns llms.txt content:
   - Header with site title and description
   - List of pages with title and description from meta tags
   - If sitemap_urls provided, include all pages; otherwise use just the analyzed URL

3. `generate_robots_fixes(extraction)` → Returns corrected robots.txt content:
   - If citation bots blocked → add `Allow: /` for each
   - Include comments explaining training vs citation bot distinction
   - Preserve existing rules where possible

4. `generate_meta_fixes(extraction)` → Returns Next.js metadata export code:
   - Fix title length if too long/short (target 50-60 chars)
   - Fix description length if too long/short (target 150-160 chars)
   - Add missing OG tags
   - Return as a TypeScript `metadata` object string

5. `identify_weak_sections(extraction, scores)` → Returns the 3-5 weakest sections:
   - Score each section by: has_stats, has_citations, has_quotes, word_count, answer_first
   - Return sorted by weakness

**Step 1: Write failing tests**

```python
def test_generate_faq_schema():
    extraction = {"content": {"faq_detected": True, "sections": [...]}, "technical": {"schema_types": ["Article"]}}
    fixes = generate_schema_fixes(extraction)
    faq_fix = [f for f in fixes if f["schema"]["@type"] == "FAQPage"]
    assert len(faq_fix) == 1

def test_generate_llms_txt():
    extraction = {"url": "https://example.com/tools", "meta": {"title": "Our Tools", "description": "Best tools guide"}}
    content = generate_llms_txt(extraction, [])
    assert "# " in content  # Has markdown header
    assert "example.com" in content

def test_generate_robots_fixes_unblocks_citation_bots():
    extraction = {"technical": {"citation_bots_blocked": ["PerplexityBot"], "training_bots_blocked": []}}
    result = generate_robots_fixes(extraction)
    assert "PerplexityBot" in result
    assert "Allow" in result

def test_identify_weak_sections():
    extraction = {"content": {"sections": [
        {"heading": "Strong", "has_stats": True, "has_citations": True, "has_quotes": True, "word_count": 100, "answer_first": True},
        {"heading": "Weak", "has_stats": False, "has_citations": False, "has_quotes": False, "word_count": 50, "answer_first": False},
    ]}}
    weak = identify_weak_sections(extraction, {})
    assert weak[0]["heading"] == "Weak"
```

**Step 2: Run tests to verify they fail**

```bash
python3 -m pytest test_fix_generator.py -v
```

**Step 3: Implement fix_generator.py**

Each function takes extraction JSON dict and returns generated fix content. CLI entry point: reads extraction JSON from stdin, outputs fixes as JSON.

**Step 4: Run all tests**

```bash
python3 -m pytest test_fix_generator.py -v
```

**Step 5: Commit**

```bash
git add search-visibility-optimizer/scripts/fix_generator.py search-visibility-optimizer/scripts/tests/test_fix_generator.py
git commit -m "feat: add fix_generator for schema, llms.txt, robots.txt, meta fixes"
```

---

### Task 6: Write SKILL.md

The skill definition file that Claude reads and follows.

**Files:**
- Create: `search-visibility-optimizer/SKILL.md`

**Step 1: Write SKILL.md**

Use the design doc's SKILL.md Workflow section as the structure. Key sections:

**Frontmatter:**
```yaml
---
name: search-visibility-optimizer
description: Audit any webpage for search engine and AI search visibility. Scores across 6 research-backed categories (Authority, Content Structure, Entity Clarity, Technical Foundation, AI Crawlability, Freshness), compares against competitors, and generates ready-to-use fixes (schema markup, llms.txt, robots.txt, content rewrites, Next.js metadata). Based on Princeton GEO (2024), GEO-16 (2025), and E-E-A-T research. Triggers on "SEO audit", "GEO", "AEO", "AI search optimization", "search visibility", "optimize for AI", "get cited by AI", "site audit", "page audit", or "search optimization".
---
```

**Workflow: 7 steps from the design doc.** Each step references specific scripts and reference files. Step 2 (MCP detection) tells Claude to check for Lighthouse MCP tools (`run_audit`, `get_core_web_vitals`) and GSC MCP tools in its available tool list.

Include the exact CLI commands for each script. Include the report card template. Include the fix presentation format.

**Step 2: Commit**

```bash
git add search-visibility-optimizer/SKILL.md
git commit -m "feat: add SKILL.md with 7-step workflow"
```

---

### Task 7: Update README.md and clean up

**Files:**
- Modify: `README.md`

**Step 1: Update README.md**

Replace the geo-aeo-optimizer description with search-visibility-optimizer. Keep the same format. Add:
- New skill name and description
- The 6 scoring categories with weights
- Research basis (Princeton GEO, GEO-16, E-E-A-T)
- MCP integration note (Lighthouse, GSC)
- Note that geo-aeo-optimizer is deprecated

**Step 2: Run full test suite**

```bash
cd /Users/alanpentz/Documents/GitHub/ownerrx-skills/search-visibility-optimizer/scripts/tests
python3 -m pytest -v
```
Expected: All tests pass across all test files.

**Step 3: Commit**

```bash
git add README.md
git commit -m "docs: update README for search-visibility-optimizer, deprecate geo-aeo-optimizer"
```

**Step 4: Push to GitHub**

```bash
git push origin main
```
