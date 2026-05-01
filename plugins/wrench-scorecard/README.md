# wrench-scorecard

**Score and prioritize any contact list** using the Wrench.ai scoring methodology. Paste in a CRM export, a list of names and titles, or even a rough description of your pipeline — and get a ranked scorecard with tier classification, scoring rationale, and a recommended next action per contact.

Works standalone with any data you have. Scales to the full 183-variable Wrench.ai model when connected via MCP.

---

## Install

```
/plugin install wrench-scorecard@wrench-plugins
```

Or as part of the full marketplace:

```
/plugin marketplace add github:WrenchAI/wrench-plugins
```

---

## Usage

Trigger with any of:

```
score these leads
score this list
prioritize my contacts
rank my pipeline
which contacts should I call first
```

Paste your contact list in any format — CSV, table, freeform. The skill extracts what it can and scores on four dimensions: ICP Fit, Company Signals, Behavioral Signals, and Timing.

---

## What You Get

- **Scored table** — every contact ranked 0–100 with tier: Hot / Warm / Nurture / Qualify
- **Contact spotlights** — top 5 contacts with WHY THEY RANK, OPEN WITH, and WATCH FOR
- **List health summary** — distribution across tiers, biggest opportunity, biggest risk
- **Data gap flags** — what's missing and how to fill it to improve score accuracy

---

## Scoring Dimensions

| Dimension | Max Points | What It Measures |
|-----------|-----------|-----------------|
| ICP Fit | 30 | Title seniority, budget authority, function alignment |
| Company Signals | 25 | Size, stage, industry fit, growth signals |
| Behavioral Signals | 25 | Engagement recency, intent signals, buying stage indicators |
| Timing | 20 | Deal stage, trigger events, competitive context |

---

## With Wrench.ai Connected

When your Claude instance is connected to your Wrench.ai workspace via MCP, the skill replaces manual scoring with the actual 183-variable predictive model:

- SHAP-explained driving variables per contact — the model tells you exactly why each contact scores where it does
- Scores compared against your workspace baseline — see how this list stacks up against your historical pipeline
- Behavioral alignment data from 110+ enrichment sources — not just what you know, but what the data shows

**Setup:** [docs/setup-mcp.md](../../docs/setup-mcp.md)

---

## Learn More

[wrench.ai/lead-scoring](https://wrench.ai/lead-scoring)
