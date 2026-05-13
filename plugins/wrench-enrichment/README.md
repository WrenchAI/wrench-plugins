# wrench-enrichment

**Build a structured enrichment profile** for any contact using the Wrench.ai enrichment methodology. Given a name, title, LinkedIn URL, email, or any fragment — the skill builds a complete profile: firmographic classification, behavioral signal inference, archetype mapping, data gap analysis, and a recommended outreach approach.

Works standalone with any data you have. Scales to API-powered enrichment across 183 variables when connected via MCP.

---

## Install

```
/plugin install wrench-enrichment@wrench-plugins
```

Or as part of the full marketplace:

```
/plugin marketplace add github:WrenchAI/wrench-plugins
```

---

## Usage

Trigger with any of:

```
enrich this contact
research this person
build a profile for [name]
qualify this lead
what do we know about [name] at [company]
fill in the gaps on [contact]
```

Provide whatever you have — a name and title, a LinkedIn URL, a company name, or an email address. The skill extracts and infers the rest.

---

## What You Get

- **Firmographic layer** — industry, stage, headcount band, ICP classification, confidence ratings
- **Contact layer** — seniority, function, budget authority, decision role
- **Archetype profile** — dominant buyer archetype with behavioral implication and signal explanation
- **Behavioral signal map** — what buying stage signals are present, absent, or unknown
- **Data gap map** — exactly what's missing, how to fill it, and the impact on outreach quality
- **Enrichment summary** — one-page profile ready to drop into a CRM or share with an AE

---

## With Wrench.ai Connected

When your Claude instance is connected to your Wrench.ai workspace via MCP, the skill calls the actual enrichment API:

- 183-variable enrichment from 110+ data sources
- Behavioral alignment scores per meta-measure category
- Archetype confirmed by model, not inferred from title
- SHAP-explained lead score with driving variables
- Enrichment updates continuously as the contact engages

**Setup:** [docs/setup-mcp.md](../../docs/setup-mcp.md)

---

## Learn More

[wrench.ai/enrichment](https://wrench.ai/enrichment)
