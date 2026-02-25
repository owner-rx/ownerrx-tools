# OwnerRx Tools

Skills and plugins for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) and [Claude Cowork](https://cowork.anthropic.com). Built for the OwnerRx AI Cohort.

## Installation

Install a specific skill using the Vercel skills CLI:

```bash
npx skills add https://github.com/owner-rx/ownerrx-tools --skill skill-name
```

For example:

```bash
npx skills add https://github.com/owner-rx/ownerrx-tools --skill linkedin-writer
```

Alternatively, copy any skill folder manually into your `~/.claude/skills/` directory.

## Skills

| Skill | What it does |
|---|---|
| **brand-copywriter** | Writes marketing copy using proven frameworks — ads, landing pages, sales pages, email sequences, product descriptions |
| **competitor-intel** | Analyzes competitors via web research: verified metrics, leverage strategies, predicted next moves |
| **create-cowork-plugin** | Guides you through creating a new Cowork plugin from scratch — scaffolds structure, skills, and commands. Includes a real-world example (brand-studio) |
| **cro-optimization** | Audits landing pages against CRO principles and delivers actionable conversion optimization recommendations |
| **go-to-market-plan** | Asks diagnostic questions and delivers 3 tailored go-to-market strategies based on your stage, product, and market |
| **lead-magnet-generator** | Creates viral lead magnet posts that drive comments and DMs — quick and detailed format versions |
| **linkedin-writer** | Creates high-performing LinkedIn posts using proven formats, templates, and voice matching |
| **marketing-ideas** | Matches your business against 170+ proven marketing strategies to surface the best ideas for you |
| **outreach-specialist** | Crafts high-converting cold outreach messages and email sequences that book calls |
| **prd-generator** | Turns rough product ideas into structured PRDs (Product Requirements Documents) optimized for AI coding tools |
| **pricing-strategist** | Builds comprehensive pricing strategies — tiers, price points, model recommendations — through interactive questions |
| **product-hunt-launch-plan** | Creates a step-by-step Product Hunt launch plan designed to rank #1 |
| **skill-creator** | Guides you through creating effective Claude skills — structure, frontmatter, references, and best practices |
| **sop-creator** | Creates detailed Standard Operating Procedures for repeatable business processes |
| **strategic-planning** | Analyzes your business context and delivers the 3 highest-impact next moves for growth |
| **viral-hook-creator** | Creates attention-grabbing hooks using proven psychological patterns and trigger words |
| **x-writer** | Creates viral X (Twitter) posts using proven formats, templates, and creator voice matching |

## Plugins

| Plugin | What it does |
|---|---|
| **brand-studio.plugin** | Customizable branded content creation — keynotes, workshops, PDF guides, pitch decks, one-pagers. Includes 3 content frameworks (IGNITE, EDUCATE, REPORT) and dark/light mode selection. Customize with your own brand colors, fonts, and voice. |

### Installing the plugin

1. Open Claude Cowork
2. Go to Settings > Plugins
3. Click "Install from file"
4. Select `plugins/brand-studio.plugin`
5. Run `/cowork-plugin-customizer` to personalize it with your brand

## How skills work

Each skill follows the same structure:

```
skill-name/
├── SKILL.md              # Main skill file (knowledge + instructions)
└── references/           # Supporting data loaded on demand
    ├── templates.md
    └── examples.md
```

- **SKILL.md** is loaded when the skill is triggered
- **references/** files are loaded only when needed, keeping context efficient

## License

These tools are shared with OwnerRx AI Cohort participants for personal and business use.
