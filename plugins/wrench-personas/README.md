# wrench-personas

**Population-level persona engine** for any contact list or audience. The skill segments your population into named clusters, places each on the technology adoption curve, builds an OCEAN psychographic fingerprint per segment, surfaces SHAP-weighted meta-measure priorities, and produces a structured message recipe for each group — with draft cold outreach, nurture, and sales opener per cluster.

One run produces everything a sales or marketing team needs to execute a persona-matched campaign without guessing.

Works standalone with any data you paste in. With a Wrench.ai workspace connected via MCP, every step runs against ML-clustered segments, real OCEAN scores, adoption curve labels, and SHAP-explained behavioral drivers from the platform's patented hypertargeting model.

---

## Install

```
/plugin install wrench-personas@wrench-plugins
```

Or as part of the full marketplace:

```
/plugin marketplace add github:WrenchAI/wrench-plugins
```

---

## Usage

Trigger with any of:

```
segment this list
run personas on this
adoption curve analysis for [audience]
who is in this population?
build persona recipes for [list]
hypertarget [audience description]
```

Paste your contact list in any format, describe the population, or connect to your Wrench.ai workspace for ML-clustered segments.

---

## What You Get

For each persona cluster:

- **Named segment** with size, composition, and primary behavioral signal
- **Adoption curve placement** — Innovator / Early Adopter / Early Majority / Late Majority / Laggard — with stated reasoning
- **OCEAN fingerprint** — Big Five psychographic profile per cluster with dominant trait and persuasion implication
- **SHAP meta-measure rankings** — the behavioral signals that most drive conversion for this segment, weighted by predictive contribution
- **Lead score profile** — score range, average, and tier distribution per cluster
- **Message recipe** — structured formula: hook formula, value frame, proof type, CTA design, channel, cadence
- **Three draft messages** — cold outreach, nurture, and sales call opener per cluster

Plus a **population intelligence summary**: adoption curve distribution across the whole list, sequencing priority, cross-cluster patterns, whitespace gaps, and list health assessment.

---

## The Adoption Curve

The skill maps every persona cluster to one of five adoption stages from the technology diffusion model (Rogers, 1962 — foundational to all B2B go-to-market strategy):

| Stage | % of Market | What Moves Them |
|-------|------------|----------------|
| Innovators | ~2.5% | First-mover framing, exclusivity, vision |
| Early Adopters | ~13.5% | Peer influence, competitive signal, directional data |
| Early Majority | ~34% | Social proof, implementation roadmap, named ROI |
| Late Majority | ~34% | Authority backing, risk mitigation, consensus |
| Laggards | ~16% | Compliance framing, consequence of inaction |

The adoption stage determines the message type, proof requirement, CTA style, and sequence cadence for each cluster.

---

## OCEAN Psychographic Fingerprinting

Each cluster receives a Big Five personality profile:

| Dimension | What It Drives |
|-----------|---------------|
| **O — Openness** | Novelty vs. familiarity; innovator vs. laggard signal |
| **C — Conscientiousness** | Process depth vs. speed; detail requirement |
| **E — Extraversion** | Social proof weight; relationship vs. data-driven |
| **A — Agreeableness** | Consensus need; trust-building vs. direct challenge |
| **N — Neuroticism** | Risk sensitivity; guarantee requirement; cadence spacing |

The fingerprint directly drives the hook formula, language pattern, and proof type in the message recipe.

---

## SHAP-Weighted Meta-Measures

SHAP (SHapley Additive exPlanations) quantifies the marginal contribution of each behavioral signal (meta-measure) to predicted conversion. The skill ranks the 12 meta-measures by SHAP weight per cluster and builds the message recipe around the top 3.

With Wrench.ai connected, SHAP weights come from the actual lead scoring model rather than stage-default rankings.

---

## With Wrench.ai Connected

When connected to your Wrench.ai workspace via MCP:

- **ML-clustered personas** from the platform's persona service (4–8 segments, not 2–5 inferred)
- **Actual OCEAN scores** per contact from SageMaker-hosted psychographic models
- **Adoption curve labels** from the platform's adopt-curve-service — per entity, aggregated by cluster
- **Real SHAP values** from the lead scoring model — marginal contributions, not default rankings
- **Persuasion angle hierarchy** per entity from the persuasion-recommendation-service
- **Per-contact message generation** — one recipe per cluster becomes one message per person

The standalone version applies the Wrench methodology. The connected version runs the patented hypertargeting engine.

**Setup:** [docs/setup-mcp.md](../../docs/setup-mcp.md)

---

## Learn More

[wrench.ai/personas](https://wrench.ai/personas)
