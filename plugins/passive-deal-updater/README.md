# passive-deal-updater

> Passive CRM intelligence automation — builds an n8n workflow that nightly pulls signals from email, calendar, Slack, and CRM activity, synthesizes them with AI, and writes structured deal notes + next steps back to your CRM automatically.

## Install

```
/plugin install passive-deal-updater@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install passive-deal-updater@wrench-plugins`

## Usage

Trigger with any of:
- "build the deal updater"
- "update my deals automatically"
- "passive CRM update"
- "nightly deal intelligence"
- "build the deal enrichment workflow"
- "check playbook completeness on my pipeline"

Answer a few questions about your stack (CRM, email, calendar, Slack), and the skill generates complete n8n node configurations you can paste in — including schedule trigger, CRM query, signal aggregation, Claude API call, and write-back logic.

Works with Notion, HubSpot, Gmail, Google Calendar, and Slack. Adapts to your specific CRM schema.

## What It Builds

An n8n workflow that runs nightly and:
1. Queries your CRM for deals not updated in 24+ hours
2. Searches email, calendar, and Slack for recent deal signals
3. Sends signals to Claude for synthesis
4. Writes structured notes + next steps back to the deal record
5. Optionally sends a Slack DM summary of what was processed

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins). Methodology from [wrench-dna](https://github.com/WrenchAI/wrench-dna).
