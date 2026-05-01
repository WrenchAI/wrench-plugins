# Wrench.ai Claude Code Plugins

> Official plugin marketplace for [Wrench.ai](https://wrench.ai) — B2B intelligence, revenue operations, and engineering quality skills for Claude Code.

---

## Install in 30 seconds

```
/plugin marketplace add github:WrenchAI/wrench-plugins
```

Then install any skill:

```
/plugin install wrench-charts@wrench-plugins
/plugin install feature-spec@wrench-plugins
/plugin install humanizer@wrench-plugins
```

Browse all available plugins:

```
/plugin list@wrench-plugins
```

---

## Available Skills

### Data & Visualization

| Skill | Install | What it does |
|---|---|---|
| `wrench-charts` | `/plugin install wrench-charts@wrench-plugins` | Brand-compliant Recharts components — chart type guide, tokens, tooltip patterns, mobile responsiveness |

### Engineering Quality

| Skill | Install | What it does |
|---|---|---|
| `feature-spec` | `/plugin install feature-spec@wrench-plugins` | Write complete feature specs with ACs, task breakdown, and Cypress test plan. Nothing ships without all three. |
| `bug-audit` | `/plugin install bug-audit@wrench-plugins` | Structured audit for functional bugs, UX bugs, test coverage gaps, and customer-surfaced issues. Produces HOLD / SHIP WITH KNOWN ISSUES / CLEAR TO SHIP. |
| `wrench-de-review` | `/plugin install wrench-de-review@wrench-plugins` | Data engineering PR review — idempotency, dedup, cardinality assertions, schema contracts, error handling |
| `wrench-ux-constitution` | `/plugin install wrench-ux-constitution@wrench-plugins` | UX governance — 11 principles, Six Pillars, Seven Empirical Gaps, pre-ship checklist |

### Sales & Outreach

| Skill | Install | What it does |
|---|---|---|
| `wrench-outreach` | `/plugin install wrench-outreach@wrench-plugins` | Write B2B outreach sequences (email + LinkedIn) — multi-step, persona-specific, non-corporate voice |
| `meeting-dossier` | `/plugin install meeting-dossier@wrench-plugins` | Pre-meeting intelligence brief from Wrench.ai enrichment data — lead score, personas, talking points, draft messages |
| `dossier-builder` | `/plugin install dossier-builder@wrench-plugins` | Contact intelligence dossier builder — pulls Wrench.ai signals, outputs a scannable HTML artifact |

### Content & Marketing

| Skill | Install | What it does |
|---|---|---|
| `humanizer` | `/plugin install humanizer@wrench-plugins` | Remove AI writing patterns — strips corporate filler, hedging, and statistical-middle language |
| `seo-audit` | `/plugin install seo-audit@wrench-plugins` | Comprehensive SEO audit — technical, on-page, Core Web Vitals, indexing, and structured data |
| `page-cro` | `/plugin install page-cro@wrench-plugins` | Conversion rate optimization for marketing pages — hero, CTA, social proof, and above-the-fold |

---

## About Wrench.ai

Wrench.ai is a B2B intelligence and personalization platform. We help revenue teams understand who they're talking to — lead scores, personas, behavioral affinities, and personalized dossiers — so every conversation is smarter.

**Outcomes:**
- 183% greater lead scoring accuracy than traditional CRM scores
- Up to 10× improvement in acquisition over traditional lists
- 12.5–25% SDR productivity improvement

**Links:** [wrench.ai](https://wrench.ai) · [Get a workspace](https://web.wrench.ai) · [API docs](https://api.v2.wrench.ai/docs)

---

## Advanced Installation

### Pin to a specific version

```
/plugin install wrench-charts@wrench-plugins --version 1.2.0
```

### Add to a project's settings

Add to your `.claude/settings.json` to auto-install for your whole team:

```json
{
  "extraKnownMarketplaces": [
    {
      "name": "wrench-plugins",
      "source": {
        "source": "github",
        "repo": "WrenchAI/wrench-plugins"
      }
    }
  ]
}
```

### Use with Claude.ai (non-Code)

Skills in this repo are written as Claude Code plugins. For Claude.ai web/app, copy the `SKILL.md` content into a Project instruction.

---

## Listing in Other Marketplaces

This repo is registered on:
- [Smithery](https://smithery.ai/server/wrench-plugins) — MCP registry
- [Official MCP Registry](https://registry.modelcontextprotocol.io) — modelcontextprotocol.io
- [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) — under review

---

## Sync & Updates

Skills are sourced from the [wrench-dna](https://github.com/WrenchAI/wrench-dna) repository. To update all skills to their latest versions, the Wrench.ai team runs:

```bash
bash scripts/sync-plugins.sh
```

Users get updates by running:

```
/plugin marketplace update wrench-plugins
```

---

## License

MIT — see [LICENSE](./LICENSE)
