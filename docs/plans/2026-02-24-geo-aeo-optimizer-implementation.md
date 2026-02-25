# GEO/AEO Optimizer Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a Claude Code skill that audits webpages for AI search visibility, scores across 6 categories, compares against competitors, rewrites weak sections, and produces an action plan.

**Architecture:** Two Python scripts handle deterministic extraction and optional competitor discovery. Two reference files guide Claude's scoring and rewriting. SKILL.md orchestrates the full workflow. All files live in `geo-aeo-optimizer/` inside the `ownerrx-skills` repo.

**Tech Stack:** Python 3 (beautifulsoup4, requests), optional openai package for competitor discovery. Claude Code skill format (SKILL.md + scripts/ + references/).

---

### Task 1: Repo Initialization

**Files:**
- Create: `README.md`
- Create: `LICENSE`
- Create: `geo-aeo-optimizer/` (directory)

**Step 1: Initialize repo with README and LICENSE**

```markdown
# OwnerRx Skills

Open source skills for Claude Code by [OwnerRx](https://ownerrx.com).

## Skills

### geo-aeo-optimizer

Audit any webpage for AI search visibility (GEO/AEO). Scores content across 6 research-backed categories, compares against competitors, rewrites weak sections, and produces a prioritized action plan.

## License

MIT
```

LICENSE should be standard MIT license with `Copyright (c) 2026 OwnerRx`.

**Step 2: Create skill directory structure**

```bash
mkdir -p geo-aeo-optimizer/scripts
mkdir -p geo-aeo-optimizer/references
```

**Step 3: Commit**

```bash
git add README.md LICENSE geo-aeo-optimizer/
git commit -m "chore: initialize repo with skill directory structure"
```

---

### Task 2: analyze_page.py — Core Extraction (Meta + Headings)

**Files:**
- Create: `geo-aeo-optimizer/scripts/analyze_page.py`
- Create: `geo-aeo-optimizer/scripts/test_analyze_page.py`

**Step 1: Write the failing test for meta extraction**

```python
"""Tests for analyze_page.py"""
import json
import pytest
from analyze_page import analyze_html

SAMPLE_HTML = """<!DOCTYPE html>
<html>
<head>
    <title>Best Project Management Tools for Small Teams | Acme PM</title>
    <meta name="description" content="Compare the top 5 project management tools for teams under 20 people.">
    <meta property="og:title" content="Best PM Tools for Small Teams">
    <meta property="og:description" content="Compare top 5 PM tools">
    <link rel="canonical" href="https://acme.com/pm-tools">
    <meta name="article:published_time" content="2026-01-15">
    <meta name="article:modified_time" content="2026-02-10">
    <script type="application/ld+json">
    {"@context": "https://schema.org", "@type": "Article", "headline": "Best PM Tools"}
    </script>
</head>
<body>
    <h1>Best Project Management Tools for Small Teams in 2026</h1>
    <p>Managing a small team is hard. According to a 2025 PMI study, 67% of small teams fail to deliver projects on time.</p>
    <h2>Why This Matters</h2>
    <p>Research shows that productivity tools matter.</p>
    <h2>Top 5 Tools Compared</h2>
    <table><tr><td>Tool</td><td>Price</td></tr></table>
    <ul><li>Asana</li><li>Monday.com</li></ul>
    <h3>1. Asana</h3>
    <p>"Asana transformed how our 12-person team collaborates," says Jane Smith, CEO of TechStartup Inc.</p>
    <p>Asana serves over 130,000 paying customers as of 2025.</p>
    <h3>2. Monday.com</h3>
    <p>Some people like Monday.com. Experts say it's good for visual learners.</p>
</body>
</html>"""


def test_meta_extraction():
    result = analyze_html(SAMPLE_HTML, "https://acme.com/pm-tools")
    meta = result["meta"]
    assert meta["title"] == "Best Project Management Tools for Small Teams | Acme PM"
    assert "top 5 project management" in meta["description"].lower()
    assert meta["canonical"] == "https://acme.com/pm-tools"
    assert meta["published_date"] == "2026-01-15"
    assert meta["modified_date"] == "2026-02-10"
    assert meta["url_word_count"] == 2  # "pm" and "tools"
    assert meta["url_is_semantic"] is True


def test_heading_extraction():
    result = analyze_html(SAMPLE_HTML, "https://acme.com/pm-tools")
    headings = result["headings"]
    assert len(headings["h1"]) == 1
    assert "Project Management" in headings["h1"][0]
    assert len(headings["h2"]) == 2
    assert len(headings["h3"]) == 2
    assert headings["hierarchy_valid"] is True
```

**Step 2: Run test to verify it fails**

```bash
cd geo-aeo-optimizer/scripts && python3 -m pytest test_analyze_page.py -v
```

Expected: FAIL with `ModuleNotFoundError: No module named 'analyze_page'`

**Step 3: Write minimal implementation — meta and headings extraction**

```python
#!/usr/bin/env python3
"""
GEO/AEO Page Analyzer
Extracts structured data from HTML for AI search visibility scoring.
"""

import json
import re
import sys
from urllib.parse import urlparse
from bs4 import BeautifulSoup


def analyze_html(html: str, url: str = "") -> dict:
    """Analyze HTML content and return structured extraction JSON."""
    soup = BeautifulSoup(html, "html.parser")
    result = {
        "url": url,
        "meta": extract_meta(soup, url),
        "headings": extract_headings(soup),
        "content": {},
        "authority": {},
        "technical": {},
        "anti_patterns": {},
    }
    return result


def extract_meta(soup: BeautifulSoup, url: str) -> dict:
    """Extract meta tags, title, dates, URL structure."""
    title_tag = soup.find("title")
    title = title_tag.text.strip() if title_tag else ""

    desc_tag = soup.find("meta", attrs={"name": "description"})
    description = desc_tag.get("content", "") if desc_tag else ""

    canonical_tag = soup.find("link", attrs={"rel": "canonical"})
    canonical = canonical_tag.get("href", "") if canonical_tag else ""

    pub_date = ""
    mod_date = ""
    for attr in ["article:published_time", "publication_date", "date"]:
        tag = soup.find("meta", attrs={"name": attr}) or soup.find("meta", attrs={"property": attr})
        if tag:
            pub_date = tag.get("content", "")
            break

    for attr in ["article:modified_time", "last-modified", "updated_time"]:
        tag = soup.find("meta", attrs={"name": attr}) or soup.find("meta", attrs={"property": attr})
        if tag:
            mod_date = tag.get("content", "")
            break

    # URL semantic analysis
    parsed = urlparse(url)
    path_words = [w for w in re.split(r"[/\-_.]", parsed.path) if w and not w.isdigit()]
    url_word_count = len(path_words)
    url_is_semantic = 1 <= url_word_count <= 7

    return {
        "title": title,
        "description": description,
        "canonical": canonical,
        "published_date": pub_date,
        "modified_date": mod_date,
        "url_word_count": url_word_count,
        "url_is_semantic": url_is_semantic,
    }


def extract_headings(soup: BeautifulSoup) -> dict:
    """Extract heading hierarchy."""
    h1s = [h.get_text(strip=True) for h in soup.find_all("h1")]
    h2s = [h.get_text(strip=True) for h in soup.find_all("h2")]
    h3s = [h.get_text(strip=True) for h in soup.find_all("h3")]

    # Check hierarchy: should have exactly 1 H1, H2s before H3s make sense
    hierarchy_valid = len(h1s) == 1

    return {
        "h1": h1s,
        "h2": h2s,
        "h3": h3s,
        "hierarchy_valid": hierarchy_valid,
    }
```

**Step 4: Run tests to verify they pass**

```bash
cd geo-aeo-optimizer/scripts && python3 -m pytest test_analyze_page.py -v
```

Expected: 2 PASS

**Step 5: Commit**

```bash
git add geo-aeo-optimizer/scripts/analyze_page.py geo-aeo-optimizer/scripts/test_analyze_page.py
git commit -m "feat: add page analyzer with meta and heading extraction"
```

---

### Task 3: analyze_page.py — Content Extraction (Sections, Stats, Quotes)

**Files:**
- Modify: `geo-aeo-optimizer/scripts/analyze_page.py`
- Modify: `geo-aeo-optimizer/scripts/test_analyze_page.py`

**Step 1: Write failing tests for content and authority extraction**

Add to `test_analyze_page.py`:

```python
def test_content_extraction():
    result = analyze_html(SAMPLE_HTML, "https://acme.com/pm-tools")
    content = result["content"]
    assert content["word_count"] > 50
    assert content["paragraph_count"] >= 4
    assert content["list_count"] >= 1
    assert content["table_count"] >= 1
    assert content["faq_detected"] is False
    assert len(content["sections"]) >= 2


def test_authority_extraction():
    result = analyze_html(SAMPLE_HTML, "https://acme.com/pm-tools")
    auth = result["authority"]
    assert auth["statistics_count"] >= 2  # "67%", "130,000"
    assert auth["citation_count"] >= 1  # "2025 PMI study"
    assert auth["quote_count"] >= 1  # Jane Smith quote
    assert "PMI" in auth["named_entities"] or "TechStartup Inc" in auth["named_entities"]
    assert len(auth["vague_claims"]) >= 1  # "Research shows", "Experts say"


def test_section_level_signals():
    result = analyze_html(SAMPLE_HTML, "https://acme.com/pm-tools")
    sections = result["content"]["sections"]
    # Find the section with "Why This Matters"
    why_section = [s for s in sections if "Why This Matters" in s["heading"]]
    assert len(why_section) == 1
    assert why_section[0]["has_stats"] is False
    assert why_section[0]["has_citations"] is False
```

**Step 2: Run tests to verify they fail**

```bash
cd geo-aeo-optimizer/scripts && python3 -m pytest test_analyze_page.py -v
```

Expected: 3 new FAIL

**Step 3: Implement content and authority extraction**

Add to `analyze_page.py`:

```python
# Regex patterns for detection
STAT_PATTERN = re.compile(r"\b\d[\d,]*\.?\d*\s*(%|percent|million|billion|thousand)\b|\b\d[\d,]{2,}\b", re.IGNORECASE)
QUOTE_PATTERN = re.compile(r'["\u201c][^"\u201d]{20,}["\u201d]')
CITATION_PATTERN = re.compile(
    r"(?:according to|per|cited by|reported by|published (?:in|by)|"
    r"\d{4}\s+\w+\s+(?:study|report|survey|research|analysis))",
    re.IGNORECASE,
)
VAGUE_PATTERN = re.compile(
    r"\b(?:research shows|studies show|experts say|industry-leading|"
    r"cutting-edge|world-class|best-in-class|state-of-the-art|"
    r"some people|many believe|it is said)\b",
    re.IGNORECASE,
)
ENTITY_PATTERN = re.compile(
    r"\b(?:[A-Z][a-z]+(?:\s+[A-Z][a-z]+)+)\b"  # Multi-word proper nouns
    r"|\b(?:[A-Z]{2,})\b"  # Acronyms like PMI, CEO
)
FAQ_INDICATORS = re.compile(
    r"(?:frequently asked|faq|common questions|q\s*&\s*a|q:)", re.IGNORECASE
)


def extract_content(soup: BeautifulSoup) -> dict:
    """Extract content structure and per-section signals."""
    body = soup.find("body") or soup
    paragraphs = body.find_all("p")
    all_text = body.get_text(separator=" ", strip=True)
    words = all_text.split()

    lists = body.find_all(["ul", "ol"])
    tables = body.find_all("table")
    faq_detected = bool(FAQ_INDICATORS.search(all_text))

    # Build sections from headings
    sections = build_sections(body)

    return {
        "word_count": len(words),
        "paragraph_count": len(paragraphs),
        "avg_paragraph_length": (
            sum(len(p.get_text().split()) for p in paragraphs) // max(len(paragraphs), 1)
        ),
        "list_count": len(lists),
        "table_count": len(tables),
        "faq_detected": faq_detected,
        "sections": sections,
    }


def build_sections(body) -> list:
    """Split page into sections based on H2/H3 headings."""
    sections = []
    current_heading = "(intro)"
    current_text_parts = []

    for element in body.descendants:
        if element.name in ("h2", "h3"):
            if current_text_parts:
                text = " ".join(current_text_parts)
                sections.append(make_section(current_heading, text))
            current_heading = element.get_text(strip=True)
            current_text_parts = []
        elif element.name == "p":
            current_text_parts.append(element.get_text(strip=True))

    # Don't forget the last section
    if current_text_parts:
        text = " ".join(current_text_parts)
        sections.append(make_section(current_heading, text))

    return sections


def make_section(heading: str, text: str) -> dict:
    """Create a section dict with signal detection."""
    return {
        "heading": heading,
        "text": text,
        "word_count": len(text.split()),
        "has_stats": bool(STAT_PATTERN.search(text)),
        "has_citations": bool(CITATION_PATTERN.search(text)),
        "has_quotes": bool(QUOTE_PATTERN.search(text)),
    }


def extract_authority(soup: BeautifulSoup) -> dict:
    """Extract authority signals from page content."""
    body = soup.find("body") or soup
    text = body.get_text(separator=" ", strip=True)

    stats = STAT_PATTERN.findall(text)
    citations = CITATION_PATTERN.findall(text)
    quotes = QUOTE_PATTERN.findall(text)
    entities = list(set(ENTITY_PATTERN.findall(text)))
    vague = [m.group() for m in VAGUE_PATTERN.finditer(text)]

    return {
        "statistics_count": len(stats),
        "citation_count": len(citations),
        "quote_count": len(quotes),
        "named_entities": entities,
        "vague_claims": vague,
    }
```

Update `analyze_html()` to call these:

```python
def analyze_html(html: str, url: str = "") -> dict:
    soup = BeautifulSoup(html, "html.parser")
    return {
        "url": url,
        "meta": extract_meta(soup, url),
        "headings": extract_headings(soup),
        "content": extract_content(soup),
        "authority": extract_authority(soup),
        "technical": {},
        "anti_patterns": {},
    }
```

**Step 4: Run tests to verify they pass**

```bash
cd geo-aeo-optimizer/scripts && python3 -m pytest test_analyze_page.py -v
```

Expected: All PASS. Some regex patterns may need tuning — adjust patterns if specific assertions fail, especially `named_entities` matching.

**Step 5: Commit**

```bash
git add geo-aeo-optimizer/scripts/
git commit -m "feat: add content and authority signal extraction"
```

---

### Task 4: analyze_page.py — Technical & Anti-pattern Extraction

**Files:**
- Modify: `geo-aeo-optimizer/scripts/analyze_page.py`
- Modify: `geo-aeo-optimizer/scripts/test_analyze_page.py`

**Step 1: Write failing tests**

Add to `test_analyze_page.py`:

```python
def test_technical_extraction():
    result = analyze_html(SAMPLE_HTML, "https://acme.com/pm-tools")
    tech = result["technical"]
    assert "Article" in tech["schema_types"]
    assert tech["meta_tags_present"]  # at least description, og:title
    assert "description" in tech["meta_tags_present"]


def test_anti_pattern_detection():
    result = analyze_html(SAMPLE_HTML, "https://acme.com/pm-tools")
    anti = result["anti_patterns"]
    assert isinstance(anti["keyword_density_flags"], list)
    assert isinstance(anti["thin_sections"], list)
    assert isinstance(anti["wall_of_text_sections"], list)
    assert isinstance(anti["unsourced_claims"], list)
    # "Research shows" and "Experts say" should be flagged
    assert len(anti["unsourced_claims"]) >= 1


def test_ssr_detection_with_content():
    """Page with real content should be detected as SSR."""
    result = analyze_html(SAMPLE_HTML, "https://acme.com/pm-tools")
    assert result["technical"]["is_ssr"] is True


def test_ssr_detection_empty_body():
    """Page with only a script tag should be detected as client-rendered."""
    empty_html = """<html><head><title>My App</title></head>
    <body><script src="app.js"></script></body></html>"""
    result = analyze_html(empty_html, "https://example.com")
    assert result["technical"]["is_ssr"] is False
```

**Step 2: Run tests to verify they fail**

```bash
cd geo-aeo-optimizer/scripts && python3 -m pytest test_analyze_page.py -v
```

Expected: 4 new FAIL

**Step 3: Implement technical and anti-pattern extraction**

Add to `analyze_page.py`:

```python
# Known AI crawler bot names
AI_BOTS = ["GPTBot", "ChatGPT-User", "ClaudeBot", "Claude-Web",
           "PerplexityBot", "Google-Extended", "Amazonbot", "Bytespider", "CCBot"]

EXPECTED_META = ["description", "og:title", "og:description", "og:image"]
EXPECTED_SCHEMA = ["Article", "FAQPage", "HowTo", "BreadcrumbList",
                   "Organization", "WebPage", "Service"]


def extract_technical(soup: BeautifulSoup, robots_txt: str = "") -> dict:
    """Extract technical crawlability signals."""
    # Schema markup
    json_ld_scripts = soup.find_all("script", type="application/ld+json")
    schema_types = []
    for script in json_ld_scripts:
        try:
            data = json.loads(script.string)
            if isinstance(data, dict) and "@type" in data:
                schema_types.append(data["@type"])
            elif isinstance(data, list):
                for item in data:
                    if isinstance(item, dict) and "@type" in item:
                        schema_types.append(item["@type"])
        except (json.JSONDecodeError, TypeError):
            continue

    schema_missing = [s for s in EXPECTED_SCHEMA if s not in schema_types]

    # Meta tags present/missing
    meta_present = []
    meta_missing = []
    if soup.find("meta", attrs={"name": "description"}):
        meta_present.append("description")
    for og in ["og:title", "og:description", "og:image"]:
        if soup.find("meta", attrs={"property": og}):
            meta_present.append(og)
    meta_missing = [m for m in EXPECTED_META if m not in meta_present]

    # AI bot access from robots.txt
    ai_bots_blocked = []
    ai_bots_allowed = []
    if robots_txt:
        for bot in AI_BOTS:
            if re.search(rf"User-agent:\s*{bot}.*?Disallow:\s*/\s*$", robots_txt,
                         re.MULTILINE | re.DOTALL):
                ai_bots_blocked.append(bot)
            else:
                ai_bots_allowed.append(bot)
    else:
        ai_bots_allowed = list(AI_BOTS)  # Assume allowed if no robots.txt provided

    # SSR detection: does the body have meaningful text content?
    body = soup.find("body") or soup
    body_text = body.get_text(separator=" ", strip=True)
    # Strip out script/style content for word count
    for tag in body.find_all(["script", "style"]):
        tag.decompose()
    clean_text = body.get_text(separator=" ", strip=True)
    is_ssr = len(clean_text.split()) > 20

    return {
        "schema_types": schema_types,
        "schema_missing": schema_missing,
        "ai_bots_blocked": ai_bots_blocked,
        "ai_bots_allowed": ai_bots_allowed,
        "meta_tags_present": meta_present,
        "meta_tags_missing": meta_missing,
        "is_ssr": is_ssr,
    }


def extract_anti_patterns(soup: BeautifulSoup, content_data: dict) -> dict:
    """Detect GEO/AEO anti-patterns."""
    body = soup.find("body") or soup
    text = body.get_text(separator=" ", strip=True)

    # Keyword stuffing: any single word appearing > 3% of total words
    words = text.lower().split()
    total = max(len(words), 1)
    from collections import Counter
    word_counts = Counter(words)
    # Filter out common stop words
    stop_words = {"the", "a", "an", "is", "are", "was", "were", "be", "been",
                  "to", "of", "in", "for", "on", "with", "at", "by", "from",
                  "and", "or", "but", "not", "that", "this", "it", "as", "if"}
    keyword_flags = [
        w for w, c in word_counts.items()
        if c / total > 0.03 and w not in stop_words and len(w) > 3
    ]

    # Thin sections (< 30 words under a heading)
    thin = [s["heading"] for s in content_data.get("sections", [])
            if s["word_count"] < 30 and s["heading"] != "(intro)"]

    # Wall of text sections (> 300 words with no sub-headings)
    walls = [s["heading"] for s in content_data.get("sections", [])
             if s["word_count"] > 300]

    # Unsourced claims
    unsourced = [m.group() for m in VAGUE_PATTERN.finditer(text)]

    return {
        "keyword_density_flags": keyword_flags,
        "thin_sections": thin,
        "wall_of_text_sections": walls,
        "unsourced_claims": unsourced,
    }
```

Update `analyze_html()` to wire everything together:

```python
def analyze_html(html: str, url: str = "", robots_txt: str = "") -> dict:
    soup = BeautifulSoup(html, "html.parser")
    # Make a copy for technical SSR check (which decomposes script/style tags)
    soup_tech = BeautifulSoup(html, "html.parser")
    content = extract_content(soup)
    return {
        "url": url,
        "meta": extract_meta(soup, url),
        "headings": extract_headings(soup),
        "content": content,
        "authority": extract_authority(soup),
        "technical": extract_technical(soup_tech, robots_txt),
        "anti_patterns": extract_anti_patterns(soup, content),
    }
```

**Step 4: Run tests to verify they pass**

```bash
cd geo-aeo-optimizer/scripts && python3 -m pytest test_analyze_page.py -v
```

Expected: All PASS

**Step 5: Commit**

```bash
git add geo-aeo-optimizer/scripts/
git commit -m "feat: add technical crawlability and anti-pattern detection"
```

---

### Task 5: analyze_page.py — CLI Entry Point

**Files:**
- Modify: `geo-aeo-optimizer/scripts/analyze_page.py`
- Modify: `geo-aeo-optimizer/scripts/test_analyze_page.py`

**Step 1: Write failing test for CLI**

```python
def test_cli_with_html_file(tmp_path):
    """Test CLI reads from HTML file."""
    html_file = tmp_path / "test.html"
    html_file.write_text(SAMPLE_HTML)
    import subprocess
    result = subprocess.run(
        ["python3", "analyze_page.py", "--file", str(html_file)],
        capture_output=True, text=True,
        cwd=str(Path(__file__).parent),
    )
    assert result.returncode == 0
    data = json.loads(result.stdout)
    assert "meta" in data
    assert "content" in data
    assert "authority" in data
```

**Step 2: Run test to verify it fails**

**Step 3: Add CLI entry point to `analyze_page.py`**

```python
def main():
    """CLI entry point: analyze a local HTML file or URL."""
    import argparse
    parser = argparse.ArgumentParser(description="GEO/AEO Page Analyzer")
    parser.add_argument("--file", help="Path to local HTML file")
    parser.add_argument("--url", help="URL (expects HTML via stdin if no --file)")
    parser.add_argument("--robots", help="Path to robots.txt file", default="")
    args = parser.parse_args()

    robots_txt = ""
    if args.robots:
        with open(args.robots) as f:
            robots_txt = f.read()

    if args.file:
        with open(args.file) as f:
            html = f.read()
        url = args.url or ""
    else:
        html = sys.stdin.read()
        url = args.url or ""

    result = analyze_html(html, url, robots_txt)
    print(json.dumps(result, indent=2))


if __name__ == "__main__":
    main()
```

**Step 4: Run tests**

**Step 5: Commit**

```bash
git add geo-aeo-optimizer/scripts/
git commit -m "feat: add CLI entry point to page analyzer"
```

---

### Task 6: discover_competitors.py

**Files:**
- Create: `geo-aeo-optimizer/scripts/discover_competitors.py`
- Create: `geo-aeo-optimizer/scripts/test_discover_competitors.py`

**Step 1: Write failing test for prompt generation (no API call needed)**

```python
"""Tests for discover_competitors.py — prompt generation only (no API calls)."""
import json
import pytest
from discover_competitors import generate_prompts, parse_competitor_mentions

SAMPLE_EXTRACTION = {
    "url": "https://acme.com/pm-tools",
    "meta": {
        "title": "Best Project Management Tools for Small Teams | Acme PM",
        "description": "Compare the top 5 project management tools for teams under 20 people.",
    },
    "headings": {"h1": ["Best Project Management Tools for Small Teams in 2026"]},
}


def test_generate_prompts():
    prompts = generate_prompts(SAMPLE_EXTRACTION)
    assert len(prompts) >= 3
    assert len(prompts) <= 5
    # Prompts should be natural questions, not keyword queries
    assert any("?" in p for p in prompts)


def test_parse_competitor_mentions():
    fake_responses = [
        "I'd recommend Asana for small teams. Monday.com is also great. ClickUp offers a free tier.",
        "For project management, Asana and Monday.com are the top choices. Trello is more basic.",
        "The best tools are Asana, Monday.com, and Notion for small teams.",
    ]
    result = parse_competitor_mentions(fake_responses, "Acme PM")
    assert len(result["competitors"]) >= 2
    # Asana should be top (mentioned in all 3)
    assert result["competitors"][0]["name"] == "Asana"
    assert result["competitors"][0]["mentions"] == 3
    assert result["user_mentioned"] is False
```

**Step 2: Run test to verify it fails**

**Step 3: Implement discover_competitors.py**

```python
#!/usr/bin/env python3
"""
GEO/AEO Competitor Discovery
Queries LLMs to find which brands AI recommends in your niche.
"""

import json
import os
import re
import sys
from collections import Counter


def generate_prompts(extraction: dict) -> list[str]:
    """Generate natural customer prompts from page extraction data."""
    title = extraction.get("meta", {}).get("title", "")
    desc = extraction.get("meta", {}).get("description", "")
    h1 = (extraction.get("headings", {}).get("h1", [None]) or [None])[0] or ""

    # Infer the niche from title + description + H1
    context = f"{title} {desc} {h1}".strip()

    # Build prompts that a customer would actually ask
    prompts = [
        f"What are the best alternatives for someone looking at {context[:80]}?",
        f"Can you recommend the top companies or tools for {desc[:80]}?",
        f"I need help choosing a solution for {h1[:80] if h1 else desc[:80]}. What should I consider?",
        f"Who are the market leaders in {desc[:60] if desc else title[:60]}?",
    ]

    # Add a specific recommendation prompt
    if h1:
        prompts.append(f"What would you recommend for {h1[:80]}?")

    return prompts[:5]


def parse_competitor_mentions(responses: list[str], user_brand: str) -> dict:
    """Parse LLM responses to extract mentioned brands and rank them."""
    # Extract capitalized brand-like names (2+ chars, not common words)
    stop_words = {"The", "This", "That", "These", "Those", "What", "When",
                  "Where", "Which", "While", "With", "Would", "Could", "Should",
                  "Here", "There", "They", "Their", "Also", "Some", "Many",
                  "Most", "More", "Best", "Good", "Great", "Free", "Other"}

    brand_counter = Counter()
    for response in responses:
        # Find capitalized words/phrases that look like brand names
        brands = re.findall(r"\b([A-Z][a-zA-Z]+(?:\.[a-zA-Z]+)?)\b", response)
        seen_in_response = set()
        for brand in brands:
            if brand not in stop_words and len(brand) > 2:
                if brand not in seen_in_response:
                    brand_counter[brand] += 1
                    seen_in_response.add(brand)

    # Check if user brand was mentioned
    user_mentioned = False
    user_count = 0
    user_lower = user_brand.lower()
    for response in responses:
        if user_lower in response.lower():
            user_mentioned = True
            user_count += 1

    # Build ranked competitor list (exclude user brand)
    competitors = [
        {"name": name, "url": "", "mentions": count}
        for name, count in brand_counter.most_common(10)
        if name.lower() != user_lower
    ][:3]

    return {
        "competitors": competitors,
        "user_mentioned": user_mentioned,
        "user_mention_count": user_count,
    }


def discover(extraction: dict, user_brand: str, api_key: str) -> dict:
    """Run full competitor discovery via OpenAI API."""
    try:
        from openai import OpenAI
    except ImportError:
        return {"error": "openai package not installed. Run: pip install openai"}

    client = OpenAI(api_key=api_key)
    prompts = generate_prompts(extraction)

    responses = []
    for prompt in prompts:
        response = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[{"role": "user", "content": prompt}],
            max_tokens=500,
        )
        responses.append(response.choices[0].message.content)

    result = parse_competitor_mentions(responses, user_brand)
    result["inferred_niche"] = extraction.get("meta", {}).get("description", "")
    result["prompts_used"] = prompts
    return result


def main():
    """CLI: reads extraction JSON from stdin, prints competitor JSON."""
    import argparse
    parser = argparse.ArgumentParser(description="GEO/AEO Competitor Discovery")
    parser.add_argument("--brand", required=True, help="Your brand name")
    parser.add_argument("--api-key", help="OpenAI API key (or set OPENAI_API_KEY env)")
    args = parser.parse_args()

    api_key = args.api_key or os.environ.get("OPENAI_API_KEY", "")
    if not api_key:
        print(json.dumps({"error": "No API key provided"}))
        sys.exit(1)

    extraction = json.load(sys.stdin)
    result = discover(extraction, args.brand, api_key)
    print(json.dumps(result, indent=2))


if __name__ == "__main__":
    main()
```

**Step 4: Run tests**

```bash
cd geo-aeo-optimizer/scripts && python3 -m pytest test_discover_competitors.py -v
```

Expected: All PASS (tests only exercise prompt generation and response parsing, no API calls)

**Step 5: Commit**

```bash
git add geo-aeo-optimizer/scripts/discover_competitors.py geo-aeo-optimizer/scripts/test_discover_competitors.py
git commit -m "feat: add competitor discovery with prompt generation and response parsing"
```

---

### Task 7: scoring-rubric.md Reference File

**Files:**
- Create: `geo-aeo-optimizer/references/scoring-rubric.md`

**Step 1: Write the scoring rubric**

This is the reference file Claude reads to assign consistent scores. Contains specific thresholds for each category based on the research.

```markdown
# GEO/AEO Scoring Rubric

Use this rubric to score each page from the `analyze_page.py` extraction JSON. Each category is 0-100. Apply the composite weights at the end.

## 1. Authority Signals (Weight: 30%)

Score based on `authority` section of extraction JSON.

| Score Range | Criteria |
|---|---|
| 0-20 | 0 statistics, 0 citations, 0 quotes |
| 21-40 | 1-2 statistics OR 1 citation, no quotes |
| 41-60 | 3-5 statistics, 1-2 citations, 0-1 quotes |
| 61-80 | 6-10 statistics, 3-5 citations, 2+ quotes, some named entities |
| 81-100 | 10+ statistics with cited sources, 5+ citations, 3+ expert quotes, strong entity density |

**Deductions:** -10 for each vague claim ("research shows", "experts say") without a specific source nearby.

## 2. Content Structure (Weight: 20%)

Score based on `headings` and `content` sections.

| Score Range | Criteria |
|---|---|
| 0-20 | No headings or 1 heading, no lists/tables, no FAQ |
| 21-40 | Some H2s but no H3s, no lists or tables, long paragraphs |
| 41-60 | Valid H1→H2→H3 hierarchy, some lists/tables, avg paragraph < 100 words |
| 61-80 | Clean hierarchy, 2+ lists, 1+ table, FAQ detected, sections self-contained |
| 81-100 | Answer-first pattern detected, comprehensive FAQ, tables for comparisons, all sections extractable and self-contained |

**Deductions:** -15 if `hierarchy_valid` is false (missing H1 or multiple H1s).

## 3. Entity Clarity (Weight: 20%)

Score based on `authority.named_entities` and `authority.vague_claims`.

| Score Range | Criteria |
|---|---|
| 0-20 | 0-1 named entities, many vague claims |
| 21-40 | 2-4 named entities, some vague claims remain |
| 41-60 | 5-8 named entities, few vague claims, some sections still generic |
| 61-80 | 8-15 named entities, rare vague claims, most claims attributed |
| 81-100 | 15+ named entities (people, orgs, frameworks), zero vague claims, every factual statement attributed |

**Key signal:** Ratio of named_entities to vague_claims. Higher is better.

## 4. Technical Crawlability (Weight: 15%)

Score based on `technical` section.

| Score Range | Criteria |
|---|---|
| 0 | `is_ssr` is false — STOP. Nothing else matters. Page is invisible to AI. |
| 1-20 | SSR true but no schema, missing meta tags, AI bots blocked |
| 21-40 | SSR true, has description meta, no schema markup |
| 41-60 | SSR true, description + OG tags, 1 schema type, no AI bots blocked |
| 61-80 | SSR true, all expected meta tags, 2+ schema types, semantic URL |
| 81-100 | SSR true, complete meta tags, 3+ schema types (including FAQPage), all AI bots allowed, semantic URL 5-7 words |

**Critical:** If `is_ssr` is false, this category scores 0 AND a prominent warning is added to the report.

## 5. Freshness (Weight: 10%)

Score based on `meta.published_date` and `meta.modified_date`.

| Score Range | Criteria |
|---|---|
| 0-20 | No dates detectable |
| 21-40 | Published date only, older than 12 months |
| 41-60 | Published date within 12 months, no modified date |
| 61-80 | Published within 6 months OR modified within 3 months |
| 81-100 | Published or modified within 30 days, clear recency signals in content |

## 6. Anti-patterns (Weight: 5%)

Score based on `anti_patterns` section. This is an inverted score — fewer anti-patterns = higher score.

| Score Range | Criteria |
|---|---|
| 0-20 | 3+ keyword density flags, 3+ wall-of-text sections, many unsourced claims |
| 21-40 | 2 keyword flags or 2+ walls of text |
| 41-60 | 1 keyword flag or 1-2 thin sections |
| 61-80 | No keyword flags, 0-1 thin sections, few unsourced claims |
| 81-100 | Zero flags across all anti-pattern categories |

## Composite Score

```
composite = (authority * 0.30) + (structure * 0.20) + (entity * 0.20) + (technical * 0.15) + (freshness * 0.10) + (anti_patterns * 0.05)
```

## Score Interpretation

| Range | Label | Meaning |
|---|---|---|
| 0-25 | Critical | AI engines unlikely to cite this page |
| 26-50 | Weak | Some signals present but major gaps |
| 51-75 | Competitive | Solid foundation, targeted improvements move the needle |
| 76-100 | Strong | Maintain freshness and monitor competitors |
```

**Step 2: Commit**

```bash
git add geo-aeo-optimizer/references/scoring-rubric.md
git commit -m "docs: add scoring rubric reference for consistent page scoring"
```

---

### Task 8: princeton-techniques.md Reference File

**Files:**
- Create: `geo-aeo-optimizer/references/princeton-techniques.md`

**Step 1: Write the Princeton techniques reference**

```markdown
# Princeton GEO Techniques Reference

Source: "GEO: Generative Engine Optimization" (Aggarwal et al., KDD 2024)
Paper: https://arxiv.org/abs/2311.09735

Use this reference when rewriting the user's weakest-scoring sections. Apply the TOP 3 techniques (proven 30-40% visibility boost). Avoid the ineffective techniques.

## Effective Techniques (USE THESE)

### 1. Statistics Addition (+30-40% visibility)

Replace qualitative claims with quantitative data. Every factual assertion should have a number attached.

**Before:** "Many small businesses struggle with project management."
**After:** "According to a 2025 PMI survey, 67% of businesses with under 50 employees report missing project deadlines at least quarterly."

**Rules:**
- Use specific numbers, not ranges ("43%" not "40-50%")
- Cite the source inline ("according to [source]")
- Prefer recent data (within 2 years)
- If exact data isn't available, note that the user should find/add their own data with a placeholder

### 2. Citation Addition (+30-40% visibility)

Add inline citations to authoritative sources for every factual claim.

**Before:** "AI adoption is accelerating in small businesses."
**After:** "AI adoption among SMBs grew 78% year-over-year in 2025 (McKinsey Global Survey, 2025), with customer service and marketing automation leading adoption categories."

**Rules:**
- Cite specific studies, reports, or organizations by name
- Include the year of the source
- Prefer: academic papers, industry reports (McKinsey, Gartner, Forrester), government data, named surveys
- Avoid: "a study shows", "research indicates" (these are the anti-pattern)

### 3. Quotation Addition (+30-40% visibility)

Add direct quotes from named experts, customers, or industry figures.

**Before:** "Our customers love the product."
**After:** "'Since implementing [tool], our team reduced project delivery time by 34%,' says Maria Chen, Operations Director at ScaleUp Corp."

**Rules:**
- Name the person and their title/organization
- Make quotes specific and data-rich when possible
- If rewriting for the user, use placeholder names/quotes and tell them to replace with real ones
- Real quotes > fabricated ones. Mark any placeholder quotes clearly.

## Marginally Effective Techniques

### 4. Entity Specificity

Replace generic terms with specific named entities (people, organizations, products, frameworks).

**Before:** "A leading tech company developed this approach."
**After:** "Anthropic's research team, led by Dario Amodei, developed this approach in their 2024 Constitutional AI paper."

### 5. Fluency Optimization

Improve readability. Short sentences. Active voice. One idea per paragraph.

## Ineffective Techniques (AVOID THESE)

### 6. Keyword Stuffing (-10% visibility)

The Princeton study found this HURTS. Do not increase keyword density beyond natural usage. AI engines penalize obvious keyword repetition.

### 7. Authoritative Tone Alone

Simply making text sound more confident ("We definitively state...") without backing claims with data does NOT improve AI visibility. Confidence without evidence is penalized.

### 8. Generic Persuasive Writing

Marketing language ("best-in-class", "industry-leading", "cutting-edge") actively hurts AI citation probability. Replace with specific, verifiable claims.

## Rewriting Process

When rewriting a section:

1. Identify what's weak (no stats? no citations? vague claims?)
2. Apply the TOP 3 techniques to fix it
3. Show the original and rewritten version side-by-side
4. Explain what changed and why (educates the business owner)
5. Mark any placeholder data/quotes that the owner needs to replace with real information
6. Preserve the owner's voice and tone — optimize the substance, not the style
```

**Step 2: Commit**

```bash
git add geo-aeo-optimizer/references/princeton-techniques.md
git commit -m "docs: add Princeton GEO techniques reference for content rewrites"
```

---

### Task 9: SKILL.md — Main Skill File

**Files:**
- Create: `geo-aeo-optimizer/SKILL.md`

**Step 1: Write SKILL.md**

```markdown
---
name: geo-aeo-optimizer
description: Audit any webpage for AI search visibility (GEO/AEO). Scores content across 6 research-backed categories, compares against competitors, rewrites weak sections, and delivers a prioritized action plan. Use when users want to optimize their website for AI citations in ChatGPT, Perplexity, Gemini, Google AI Overviews, or Claude. Triggers on "GEO", "AEO", "AI search optimization", "AI visibility", "get cited by AI", "optimize for ChatGPT", "AI search audit", or "generative engine optimization".
---

# GEO/AEO Optimizer

Audit webpages for AI search visibility. Score against 6 research-backed categories. Compare against competitors. Rewrite weak sections. Deliver a prioritized action plan.

Based on Princeton's GEO research (KDD 2024) which proved specific content techniques boost AI citation visibility by 30-40%.

## Workflow

### Step 1: Collect Input

Ask the user for:
1. **Their URL** (primary) — or ask them to paste their page content as fallback
2. **Competitor URLs** — "Provide 2-3 competitor URLs, OR I can discover your top AI competitors automatically (requires an OpenAI API key)."

If the user wants auto-discovery and provides an API key, run competitor discovery (Step 1b). Otherwise proceed to Step 2.

### Step 1b: Competitor Discovery (Optional)

Run `scripts/discover_competitors.py`:
1. Use WebFetch to get the user's page HTML
2. Pass the HTML to `scripts/analyze_page.py --file` to get extraction JSON
3. Pipe extraction JSON to `scripts/discover_competitors.py --brand "User Brand" --api-key KEY`
4. Present discovered competitors to the user for confirmation
5. Report whether the user's brand was mentioned by AI at all

### Step 2: Fetch and Analyze Pages

For each URL (user + competitors):
1. Use WebFetch to get page HTML
2. Use WebFetch to get `{domain}/robots.txt`
3. Save HTML to a temp file
4. Run: `python3 scripts/analyze_page.py --file /tmp/page.html --url {url} --robots /tmp/robots.txt`
5. Capture the JSON output

### Step 3: Score All Pages

Read `references/scoring-rubric.md` for the detailed rubric.

For each page's extraction JSON, assign a 0-100 score per category using the rubric thresholds. Calculate the composite score using the weights:
- Authority Signals: 30%
- Content Structure: 20%
- Entity Clarity: 20%
- Technical Crawlability: 15%
- Freshness: 10%
- Anti-patterns: 5%

**Critical check:** If `technical.is_ssr` is false for the user's page, flag this immediately as a show-stopper. The page is invisible to AI crawlers and must be fixed before any content optimization matters.

### Step 4: Generate Report Card

Present a side-by-side comparison table:

```
                    You     Comp1    Comp2    Comp3
Authority Signals   32      71       65       58
Content Structure   55      68       72       61
Entity Clarity      28      64       59       53
Tech Crawlability   70      85       80       75
Freshness           45      80       70       65
Anti-patterns       80      90       85       85
─────────────────────────────────────────────────
COMPOSITE           45      74       70       64
```

### Step 5: Generate Gap Analysis

For each category where the user scores lower than the top competitor:
- State the gap with specific numbers
- Reference specific extraction data (e.g., "You have 2 statistics, Competitor A has 14")
- Explain why this matters using the research basis

Skip categories where the user is competitive (within 10 points of the leader).

### Step 6: Rewrite Weakest Sections

Read `references/princeton-techniques.md` for rewriting methods.

1. Identify the 3-5 lowest-scoring sections from `content.sections`
2. For each section, apply the top 3 Princeton techniques:
   - Statistics Addition
   - Citation Addition
   - Quotation Addition
3. Present as: Original → Optimized → What Changed
4. Mark any placeholder data/quotes the owner needs to replace
5. Preserve the owner's voice and tone

### Step 7: Generate Action Plan

Produce a prioritized checklist grouped by impact:

**High Impact (this week):** Actions based on the biggest scoring gaps, things that can be done immediately (add statistics, add citations, add FAQ section).

**Medium Impact (this month):** Structural changes (add schema markup, fix heading hierarchy, improve meta tags).

**Low Impact (when you get to it):** Minor optimizations (break up long sections, improve URL structure).

Write instructions for non-technical people. Use plain language. If something requires a developer, say so.

## Dependencies

Scripts require: `pip install beautifulsoup4 requests`

Optional (for competitor discovery): `pip install openai`
```

**Step 2: Commit**

```bash
git add geo-aeo-optimizer/SKILL.md
git commit -m "feat: add SKILL.md with complete GEO/AEO audit workflow"
```

---

### Task 10: Integration Test — Run Against a Real Page

**Files:**
- Modify: `geo-aeo-optimizer/scripts/test_analyze_page.py`

**Step 1: Add an integration test with a more complex HTML fixture**

Add a test that exercises the full pipeline with a realistic page:

```python
COMPLEX_HTML = """<!DOCTYPE html>
<html>
<head>
    <title>10 AI Tools Every Small Business Needs in 2026 | SmartBiz</title>
    <meta name="description" content="Comprehensive guide to the 10 best AI tools for small businesses in 2026, with pricing, features, and ROI data.">
    <meta property="og:title" content="10 AI Tools for Small Businesses">
    <meta property="og:description" content="Best AI tools guide 2026">
    <meta property="og:image" content="https://smartbiz.com/og-ai-tools.jpg">
    <link rel="canonical" href="https://smartbiz.com/ai-tools-2026">
    <meta name="article:published_time" content="2026-02-01">
    <meta name="article:modified_time" content="2026-02-20">
    <script type="application/ld+json">
    [
        {"@context": "https://schema.org", "@type": "Article", "headline": "10 AI Tools"},
        {"@context": "https://schema.org", "@type": "FAQPage", "mainEntity": []}
    ]
    </script>
</head>
<body>
    <h1>10 AI Tools Every Small Business Needs in 2026</h1>
    <p>Small businesses that adopt AI tools see an average 23% increase in productivity, according to a 2025 McKinsey Global Survey. Here are the tools that deliver the highest ROI.</p>
    <h2>Why AI Matters for Small Business</h2>
    <p>According to Gartner's 2025 SMB Technology Report, 64% of small businesses plan to increase AI spending in 2026. "The businesses that adopt AI early will have an insurmountable advantage in 3-5 years," says Dr. Sarah Mitchell, Director of AI Research at Stanford's HAI.</p>
    <h2>Top 10 Tools</h2>
    <table><tr><th>Tool</th><th>Price</th><th>Best For</th></tr>
    <tr><td>Claude</td><td>$20/mo</td><td>Writing &amp; Analysis</td></tr>
    <tr><td>Jasper</td><td>$49/mo</td><td>Marketing Content</td></tr></table>
    <h3>1. Claude by Anthropic</h3>
    <p>Anthropic's Claude processes 200K tokens of context, making it ideal for analyzing business documents. In our testing with 50 small businesses, Claude reduced report generation time by 67%.</p>
    <h3>2. Jasper</h3>
    <p>Jasper is a good tool. Many businesses use it. It's industry-leading in content generation.</p>
    <h2>Frequently Asked Questions</h2>
    <p><strong>Q: How much should a small business budget for AI tools?</strong></p>
    <p>A: Based on Forrester's 2025 analysis, small businesses should allocate 5-8% of their technology budget to AI tools, with an expected ROI of 150-300% within the first year.</p>
    <p><strong>Q: Do I need technical skills?</strong></p>
    <p>A: No. According to a 2025 Salesforce survey, 78% of AI tools now require zero coding knowledge.</p>
</body>
</html>"""


def test_full_pipeline_complex():
    """Integration test: full extraction on a realistic page."""
    result = analyze_html(COMPLEX_HTML, "https://smartbiz.com/ai-tools-2026")

    # Meta
    assert result["meta"]["url_is_semantic"] is True
    assert result["meta"]["published_date"] == "2026-02-01"

    # Content
    assert result["content"]["table_count"] >= 1
    assert result["content"]["faq_detected"] is True
    assert result["content"]["word_count"] > 150

    # Authority — should find multiple signals
    assert result["authority"]["statistics_count"] >= 4  # 23%, 64%, 67%, 200K, 50, 78%, 150-300%
    assert result["authority"]["citation_count"] >= 3  # McKinsey, Gartner, Forrester
    assert result["authority"]["quote_count"] >= 1  # Dr. Sarah Mitchell
    assert len(result["authority"]["named_entities"]) >= 3

    # Technical
    assert "Article" in result["technical"]["schema_types"]
    assert "FAQPage" in result["technical"]["schema_types"]
    assert result["technical"]["is_ssr"] is True

    # Anti-patterns: Jasper section has vague claims
    assert len(result["anti_patterns"]["unsourced_claims"]) >= 1

    # This page should score well overall (Claude does the scoring, but extraction should be rich)
    print(json.dumps(result, indent=2, default=str))
```

**Step 2: Run all tests**

```bash
cd geo-aeo-optimizer/scripts && python3 -m pytest test_analyze_page.py -v
```

Expected: All PASS. If any fail, tune regex patterns.

**Step 3: Commit**

```bash
git add geo-aeo-optimizer/scripts/test_analyze_page.py
git commit -m "test: add integration test with realistic page fixture"
```

---

### Task 11: requirements.txt and Final Polish

**Files:**
- Create: `geo-aeo-optimizer/requirements.txt`
- Verify all files present

**Step 1: Create requirements.txt**

```
beautifulsoup4>=4.12.0
requests>=2.31.0
```

**Step 2: Verify complete file structure**

```bash
find geo-aeo-optimizer/ -type f | sort
```

Expected:
```
geo-aeo-optimizer/SKILL.md
geo-aeo-optimizer/references/princeton-techniques.md
geo-aeo-optimizer/references/scoring-rubric.md
geo-aeo-optimizer/requirements.txt
geo-aeo-optimizer/scripts/analyze_page.py
geo-aeo-optimizer/scripts/discover_competitors.py
geo-aeo-optimizer/scripts/test_analyze_page.py
geo-aeo-optimizer/scripts/test_discover_competitors.py
```

**Step 3: Run full test suite one final time**

```bash
cd geo-aeo-optimizer/scripts && pip install beautifulsoup4 requests && python3 -m pytest -v
```

Expected: All tests PASS.

**Step 4: Commit and push**

```bash
git add .
git commit -m "feat: complete geo-aeo-optimizer skill v1"
git push -u origin main
```

---

## Task Summary

| Task | What | Files |
|---|---|---|
| 1 | Repo init | README.md, LICENSE |
| 2 | Meta + heading extraction | analyze_page.py, tests |
| 3 | Content + authority extraction | analyze_page.py, tests |
| 4 | Technical + anti-pattern extraction | analyze_page.py, tests |
| 5 | CLI entry point | analyze_page.py, tests |
| 6 | Competitor discovery | discover_competitors.py, tests |
| 7 | Scoring rubric reference | scoring-rubric.md |
| 8 | Princeton techniques reference | princeton-techniques.md |
| 9 | SKILL.md | SKILL.md |
| 10 | Integration test | tests |
| 11 | Requirements + final push | requirements.txt |
