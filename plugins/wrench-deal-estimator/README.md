# wrench-deal-estimator

> Deal scoping and pricing estimator — builds a structured line-item estimate, investment range, and proposal-ready rationale from a brief description of what the client needs. Works standalone or Wrench.ai-signal-informed.

## Install

```
/plugin install wrench-deal-estimator@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install wrench-deal-estimator@wrench-plugins`

## Usage

Trigger with any of:
- "estimate this deal"
- "what would this cost"
- "put together a quote"
- "scope this engagement"
- "build the pricing"
- "what's the investment range"

Describe what the client needs — deliverables, timeline, engagement type. The skill selects the right pricing model, anchors on value before cost, builds a line-item range, and produces a proposal-ready rationale paragraph.

With Wrench.ai connected, uses the buyer's intent score to calibrate pricing posture — anchor high for high-intent buyers, suggest a pilot for low-intent ones.

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins). Methodology from [wrench-dna](https://github.com/WrenchAI/wrench-dna).
