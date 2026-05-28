# wrench-salesperson-daily

> Run a full B2B salesperson morning workflow — inbox triage, prospect enrichment, CRM hygiene, and personalized outreach drafts

## What It Does

Simulates a complete daily routine for a B2B AE or SDR using Wrench.ai intelligence:

1. **Inbox triage** — scans Gmail for prospect replies in the last 24 hours
2. **Prospect enrichment** — pulls lead score, personality profile, and driving factors for each hot reply
3. **Agent consultation** — asks the Wrench.ai agent for specific talking points per prospect
4. **CRM hygiene** — re-enriches stale HubSpot contacts, checks for job changes
5. **Outreach drafts** — writes personalized emails matched to each prospect's communication style, saves to Gmail
6. **Discovery step** — tries one new workflow variant from your Use Case Library and scores it

Each run scores itself 0–60 across six quality dimensions and posts results to Notion + Slack.

## Install

```
/plugin install wrench-salesperson-daily@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install wrench-salesperson-daily@wrench-plugins`

## Requirements

- Wrench.ai MCP connected (`x-api-key` from web.wrench.ai/api-key)
- Gmail MCP connected
- HubSpot MCP connected
- Notion MCP connected (for session logs)
- ICP Lab databases set up via `wrench-icp-lab-setup`

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna) — Wrench.ai's standards and skills repository.
