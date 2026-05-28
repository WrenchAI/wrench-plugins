---
name: wrench-campaign-manager-daily
description: >
  Nightly ICP simulation for a B2B campaign manager or demand gen lead. Runs the
  baseline daily workflow plus one discovery variant from the Use Case Library. Each run
  expands what we know about how campaign managers can use Wrench.ai analytics and
  creative intelligence. Outputs a scored session log to Notion and a digest to Slack.
  Part of the ICP Lab system — requires setup via wrench-icp-lab-setup.
---

# Campaign Manager ICP — Daily Run

> **ICP:** B2B campaign manager or demand gen lead. Manages 3–10 active campaigns.
> Their question: "What changed overnight, and am I running the right creative
> for the right audience?"
>
> **This run does two things:**
> 1. Executes the baseline workflow (same every night — this is the benchmark)
> 2. Tries one new use case variant from the Use Case Library (this is the exploration)
>
> **Success criterion for baseline:** At least one anomaly identified and classified,
> top 3 creatives scored and mapped to personas.
> **Success criterion for discovery:** The variant produced output a campaign manager
> would act on — scored 0–10 at the end of the step.

---

## Smoke Test — Fail Fast (5 minutes max)

Verify the integrations this run depends on before doing anything else.

```
Check 1 — Wrench analytics:
  Tool: post_data_lead-score_kpi
  Pass: returns any numeric KPI data
  Fail: 401, 403, 5xx, empty response on a workspace with contacts

Check 2 — Wrench creatives:
  Tool: post_creatives_list
  Pass: response received (empty list is OK for new workspaces)
  Fail: 401, 403, 5xx, or timeout

Check 3 — Notion Use Case Library:
  Tool: notion-fetch, target: Use Case Library database ID
  Pass: database rows returned
  Fail: 401, 403, not found

Check 4 — Slack:
  Tool: slack_read_channel (any known channel, limit 1)
  Pass: response received
  Fail: 401, 403
```

All 4 pass → proceed. Record `smoke_test: passed`.

Any failure → halt and post to Slack:
```
🚨 Campaign Manager ICP run halted — {DATE}
Failed integration: {which check}
Error: {message}
```

---

## Part A: Baseline Workflow

*Same every night. Comparable across runs. This is the performance benchmark.*

### A1. Campaign Overview

**Tool:** `post_data_dashboard_campaigns`
- Capture: active campaign count, total impressions/clicks/conversions last 7 days

**Tool:** `post_data_campaigns_overview`
- Capture: per-campaign name, status, budget utilization %

**Tool:** `post_data_dashboard_contacts-by-source`
- Capture: top 3 lead sources + % of total leads each

Build overview card:
```
Active campaigns: {count}
Total leads (7d): {count} | vs prior 7d: {delta%}
Top source: {channel} ({%})
Campaigns over budget: {count}
```

### A2. Lead Score Health

**Tool:** `post_data_lead-score_kpi`
- Capture: total scored, avg score, tier distribution

**Tool:** `post_data_lead-score_trend`
- Capture: is avg score rising, stable, or declining over last 30 days?

**Tool:** `post_data_lead-score_behavioral-insights`
- Capture: top 3 behavioral predictors

**Tool:** `post_data_lead-score_demographic-insights`
- Capture: top 3 demographic predictors

Flag anomalies:
- Avg score dropped >5 points in 7 days → "Score erosion"
- High-tier % dropped >10 points → "Lead quality decline"

### A3. Campaign Anomaly Detection

**Tool:** `post_data_ad-creatives_summary`
**Tool:** `post_data_ad-creatives_latest`

For each campaign with data, compare this week vs last week:
- CTR ratio = CTR this week / CTR last week
- Conversion ratio = conversions this week / last week
- Flag if either ratio < 0.75 or > 1.5

Classify each flagged campaign:
- CTR ↓ + Conversion ↓ → Creative fatigue → swap creative
- CTR stable + Conversion ↓ → Landing page / offer issue
- CTR ↑ + Conversion ↓ → Audience mismatch
- CTR ↓ + Conversion ↑ → Scale budget (high intent, low reach)
- Both ↑ → Document what's working

### A4. Driving Variables

**Tool:** `post_data_lead-score_driving-variables`
- Top 10 variables by predictive weight with direction

**Tool:** `post_data_lead-score_tornado`
- Which variables add vs. subtract from lead scores

For each top positive variable: map to one creative or targeting implication.

### A5. Persona Analysis

**Tool:** `post_data_lead-score_persona-distribution`
- Persona breakdown + avg score per persona

**Tool:** `get_personas_get`
- Full persona definitions

**Tool:** `post_data_lead-score_heatmap`
- Persona × attribute correlation

Cross-reference with driving variables:
- Which persona has the highest avg score?
- Which has the best score-to-conversion rate?
- Which is underrepresented in the high tier?

### A6. Creative Scoring + Persona Mapping

**Tool:** `post_creatives_list`
- All creatives from last 14 days

For top 3 most recent:
**Tool:** `post_creatives_score` — score + dimension breakdown
**Tool:** `post_creatives_get-summary` — what it says, who it's for

Map each creative to its best-fit persona from A5.
Flag any creative-persona mismatch (high-scoring creative targeting a low-priority segment).

**Tool:** `post_data_creatives_scatter` — does higher creative score correlate with conversion?
**Tool:** `post_data_creatives_tornado` — which creative attributes drive better performance?

### A7. AI Recommendations

**Tool:** `post_chat_generate`

Prompt:
```
Based on today's data:
- Anomalies: {anomaly report from A3}
- Top driving variables: {top 3 from A4}
- Top persona: {name + avg score from A5}
- Best creative: {name + score from A6}

What are the top 3 specific changes I should make to my campaign strategy this week?
Name the campaign, the persona, and the creative change. Be specific.
```

Score the AI's response 1–5: does it name specific campaigns/personas/creatives, or is it generic advice?

### A8. Score the Baseline

| Dimension | 0 | 5 | 10 |
|---|---|---|---|
| Data availability | All analytics empty | Partial data | Full campaign + score + creative |
| Anomaly detection | None identified | Found but unclassified | Found, classified, actioned |
| Driving variable insight | Empty/all-equal | Variables listed | Variables + creative implications |
| Persona-creative mapping | No mapping | Basic match by score | Ranked + mapped to best persona |
| AI recommendation quality | Generic advice | Names campaign or persona | Names campaign + persona + creative change |
| Integration health | >3 tools failed | 1–2 degraded | All tools returned valid data |

Record total baseline score (0–60).

---

## Part B: Discovery Step

*Tries one unexplored use case variant from the Use Case Library.*
*Different every night. Over time, maps the full space of campaign manager use cases.*

### B1. Fetch the Next Variant

**Tool:** `notion-fetch` — query Use Case Library database

Filter:
- `ICP` = "Campaign Manager" OR "Both"
- `Status` = "Exploring"

Sort: `Run Count` ascending, then `Latest Run` ascending.

Take the first result. Read `Name` and `Description`.

If no Exploring variants remain: re-run lowest-scoring Promoted variant. Note in log.

### B2. Execute the Variant

Follow the Description exactly. Use the same analytics, creative, persona, and AI tools as the baseline. The Description specifies what to do differently — a new combination, angle, or output format.

Execute end to end.

### B3. Score the Variant (0–10)

**Would a real campaign manager act on this output?**

- **0–3:** Empty, couldn't complete, or output was too vague to use
- **4–6:** Something returned but required too much interpretation or manual work
- **7–8:** Specific, actionable output — could go directly into a campaign decision
- **9–10:** Surfaced something genuinely unexpected or high-value

Write one honest sentence explaining the score.

### B4. Update the Use Case Library

**Tool:** `notion-update-page` (target: this variant's row)

Updates:
- `Run Count`: +1
- `Latest Run`: today
- `Latest Score`: from B3
- `Notes`: append `[{date}] Score {X}/10 — {1-sentence explanation}`
- Status logic:
  - Score ≥ 7 AND run_count ≥ 2 → **Promoted**
  - Score < 4 AND run_count ≥ 3 → **Gap**
  - Otherwise: stays **Exploring**

Write verification: fetch back, confirm `Run Count` incremented → append `[verified]`

---

## Session Log — Write to Notion + Slack

### Notion

New page in ICP Lab Runs database:

```
Name: Campaign Manager Daily — {YYYY-MM-DD}
ICP: Campaign Manager
Run Date: today
Overall Score: {baseline score}/60
Pass/Fail: {Pass ≥45 / Borderline / Fail <30}
Discovery Variant Tried: {variant name}
Discovery Score: {0–10}
Capability Issues: {any failures}
Status: Complete
```

Page body:
```
## Campaign Overview
{Overview card from A1}

## Anomalies — {count} flagged
{Per-campaign anomaly classifications + recommended actions}

## Lead Score Health
{KPIs, trend, behavioral + demographic predictors, any flags}

## Driving Variables
{Top 5 with creative implications}

## Persona Analysis
{Ranking + best-fit creative mapping}

## Creative Scorecard
{Top 3 creatives: score + persona match + recommendation}

## AI Recommendations
{3 recommendations + quality score 1–5}

## Discovery Step: {variant name}
{What was attempted}
{What came back}
Score: {X}/10 — {explanation sentence}
Status: {Exploring → Promoted | Gap | still Exploring}

## Capability Notes
{Any degraded tools or unexpected behaviors}
```

Write verification: fetch back, confirm title + 3 section headings → append `[verified]`

### Slack

Post to #marketing or #product:

```
📊 Campaign Manager ICP — {DATE}
Baseline score: {X}/60 ({Pass/Borderline/Fail})

{count} anomalies: {top anomaly in 1 line}
Best creative this week: {name} — {score}/100 → best for {persona}
AI priority action: {1-sentence summary of top recommendation}

Discovery: "{variant name}"
Result: {score}/10 — {1-sentence}
{If Promoted: ✅ Promoted} {If Gap: ❌ Gap}

🔗 {Notion page link}
```
