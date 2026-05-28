# wrench-campaign-manager-daily

> Run a full B2B campaign manager morning workflow — anomaly detection, creative scoring, persona mapping, and AI recommendations

## What It Does

Simulates a complete daily routine for a demand gen lead or campaign manager using Wrench.ai analytics and creative intelligence:

1. **Campaign overview** — pulls active campaigns, lead source breakdown, budget utilization
2. **Lead score health** — checks KPIs, trend direction, behavioral and demographic predictors
3. **Anomaly detection** — flags campaigns with >25% CTR or conversion change, classifies each into one of five action types
4. **Driving variables** — identifies what's actually predicting conversion and maps it to creative implications
5. **Persona-creative mapping** — scores top 3 recent creatives and matches each to its best-fit persona
6. **AI recommendations** — asks the Wrench.ai agent for three specific campaign strategy changes
7. **Discovery step** — tries one new workflow variant from your Use Case Library and scores it

Each run scores itself 0–60 across six quality dimensions and posts results to Notion + Slack.

## Install

```
/plugin install wrench-campaign-manager-daily@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install wrench-campaign-manager-daily@wrench-plugins`

## Requirements

- Wrench.ai MCP connected (`x-api-key` from web.wrench.ai/api-key)
- Notion MCP connected (for session logs and Use Case Library)
- Slack MCP connected (for daily digest)
- ICP Lab databases set up via `wrench-icp-lab-setup`

## Category

**Marketing** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna) — Wrench.ai's standards and skills repository.
