# wrench-campaign

**Build a complete B2B campaign brief** from an audience description and objective. The skill walks through audience profiling, channel selection, message framework, sequence structure, and success benchmarks — then outputs a one-page brief ready to hand to an SDR, AE, or demand gen team.

Works standalone with any audience description. Scales to live persona distributions, behavioral scores, and competitor intelligence when connected via MCP.

---

## Install

```
/plugin install wrench-campaign@wrench-plugins
```

Or as part of the full marketplace:

```
/plugin marketplace add github:WrenchAI/wrench-plugins
```

---

## Usage

Trigger with any of:

```
build a campaign for [audience]
campaign brief for [product]
outreach strategy for [persona]
how should I reach [title] at [company type]
plan a campaign targeting [audience]
```

Provide: who you're reaching, what you're offering, and what success looks like. The skill builds the strategy from there.

---

## What You Get

- **Audience profile** — archetype classification, top meta-measures, behavioral implications
- **Channel selection** — 1–2 primary channels with explicit rationale and what to avoid
- **Message framework** — hook formula, value frame, proof type, CTA matched to buying stage
- **Sequence structure** — step-by-step timing and focus for email and/or LinkedIn
- **Benchmarks** — specific open/reply/conversion thresholds with pause conditions
- **Launch checklist** — pre-send verification steps before the campaign runs

---

## With Wrench.ai Connected

When your Claude instance is connected to your Wrench.ai workspace via MCP, the brief draws on live workspace data:

- Actual persona distribution across your contact database — validate the archetype before you build the sequence
- Meta-measure rankings per contact — so message variants are targeted to real behavioral scores, not assumed archetypes
- Competitor ad creative intelligence — see what competitors are running on Meta and LinkedIn right now, and counter it in your brief
- Campaign benchmarks from your workspace history — your actual open and reply rates, not generic industry averages

**Setup:** [docs/setup-mcp.md](../../docs/setup-mcp.md)

---

## Learn More

[wrench.ai/campaigns](https://wrench.ai/campaigns)
