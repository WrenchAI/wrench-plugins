# contact-deep-enricher

> Deep B2B contact enrichment from public sources — before you reach out

Builds a complete pre-outreach profile for any B2B contact using public third-party data:
LinkedIn, Twitter/X, Crunchbase, company news, tech stack, hiring signals, and trigger events.
Writes structured enrichment to your Wrench.ai workspace, which propagates to connected CRMs.
Produces a brand-compliant HTML dossier artifact for the sales team.

No Wrench.ai internal enrichment API used — all data is sourced by Claude from public sources
and optional third-party APIs you bring yourself.

## Install

```
/plugin install contact-deep-enricher@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install contact-deep-enricher@wrench-plugins`

## Usage

```
Enrich this contact: Sarah Chen, VP of Marketing at Acme Corp, sarah@acme.com
```

Or:

```
Deep enrich: linkedin.com/in/sarahchen-acme — I'm reaching out next week
```

## Requirements

- **Wrench.ai API key** — workspace MCP key from [web.wrench.ai/api-key](https://web.wrench.ai/api-key)
- **Optional** (improves depth): Hunter.io, Crunchbase, or Apollo.io API key

## What it produces

1. **Wrench.ai write-back** — structured enrichment fields written to the contact record
   (propagates to HubSpot, Salesforce, or any connected CRM)
2. **HTML dossier** — brand-compliant intelligence brief with:
   - Verified LinkedIn + Twitter/X profile
   - Career arc, expertise topics, published content
   - Company funding stage, tech stack, hiring signals, recent news
   - Trigger events with outreach hooks (newest first)
   - 3–5 suggested talking points personalized to this contact

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna) — Wrench.ai's standards and skills repository.
