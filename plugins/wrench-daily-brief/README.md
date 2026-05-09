# wrench-daily-brief

> Daily sales prioritization queue — surfaces who to call today, ranked by signal strength, with a why-today reason and opening line for each contact

## Install

```
/plugin install wrench-daily-brief@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install wrench-daily-brief@wrench-plugins`

## Usage

Trigger with any of:
- "who should I call today"
- "daily brief"
- "my pipeline for today"
- "morning brief"
- "who's hot today"
- "prioritize my leads"

Works standalone with a pasted contact list. With Wrench.ai connected, pulls live lead score movement and behavioral signals to build a signal-driven queue.

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna) — Wrench.ai's standards and skills repository.
