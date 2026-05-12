# content-template-builder

> Builds conversion-optimized B2B outreach templates calibrated to buyer archetype, deal stage, and vertical. Produces a MASTER base template plus archetype-specific variants — email and LinkedIn — ready for any rep to customize and send.

## Install

```
/plugin install content-template-builder@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install content-template-builder@wrench-plugins`

## Usage

Trigger with any of:
- "create a template"
- "build an email template"
- "make a template for [ICP]"
- "build outreach for [vertical]"
- "I keep writing the same email — help me template it"
- "make a template from this" (paste an existing message)

Describe what the template is for — deal stage, buyer type, channel — and the skill builds a MASTER base plus up to 5 archetype variants (Executive, Analyst, Champion, Skeptic, Relationship Builder), each with a one-liner on why it works and what to personalize before sending.

With Wrench.ai connected, pulls persona distribution from your workspace to prioritize which archetype variant to build most carefully.

## What It Produces

- **MASTER template** — archetype-neutral base that works for any contact in the ICP
- **Archetype variants** — 3–5 versions calibrated to specific buyer communication styles
- **LinkedIn adaptation** — shorter version optimized for connection notes and InMail
- **Usage guide** — personalization checklist and archetype routing cheat sheet

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins). Methodology from [wrench-dna](https://github.com/WrenchAI/wrench-dna).
