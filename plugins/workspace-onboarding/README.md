# workspace-onboarding

> Fully populates a new Wrench.ai workspace before a client's first login — competitors, AI ingredients, seed contacts, AI creatives, and workspace agent — so every screen shows relevant data on day one.

## Install

```
/plugin install workspace-onboarding@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install workspace-onboarding@wrench-plugins`

## Usage

Trigger with any of:
- "set up the workspace"
- "populate the workspace"
- "get the workspace ready for [vertical]"
- "workspace onboarding"
- "prepare the demo"

Answer a few questions (which workspace, what vertical, client domain if known), and the skill walks through each step: audit → domain research → competitor creation → meta-measures → seed contacts → AI creatives → agent configuration → final report.

Requires a Wrench.ai workspace and MCP connection. Calls `wrench-report` (Competitive 360) and `wrench-metameasure-create` as sub-skills.

## What It Configures

1. **Competitors** — researched, classified by creative volume, created in Wrench.ai
2. **Meta-measures (AI ingredients)** — vertical-specific bundle for lead scoring
3. **Seed contacts** — 3 enriched contacts to demonstrate scoring on first login
4. **AI Creatives** — display ad and social post generated from competitor reference
5. **Workspace agent** — updated with vertical-specific instructions and favorite prompts
6. **Final report** — what was configured, what was skipped, and any manual steps remaining

## Category

**Platform** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins). Methodology from [wrench-dna](https://github.com/WrenchAI/wrench-dna).
