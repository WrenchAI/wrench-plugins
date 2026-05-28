---
name: wrench-salesperson-daily
description: >
  Nightly ICP simulation for a B2B AE or SDR. Runs the baseline daily workflow plus
  one discovery variant from the Use Case Library. Each run expands what we know about
  how salespeople can use Wrench.ai. Outputs a scored session log to the ICP Lab Runs
  Notion database and a digest to Slack. Part of the ICP Lab system — requires the
  Use Case Library and Session Log databases to be set up via wrench-icp-lab-setup.
---

# Salesperson ICP — Daily Run

> **ICP:** B2B AE or SDR. Quota-carrying. Works a pipeline of 30–100 active prospects.
> Their question: "Who should I talk to today, and what should I say?"
>
> **This run does two things:**
> 1. Executes the baseline workflow (same every night — this is the benchmark)
> 2. Tries one new use case variant from the Use Case Library (this is the exploration)
>
> **Success criterion for baseline:** Top 3 prospects identified, enriched, and drafted.
> **Success criterion for discovery:** The variant produced output a real salesperson
> would find useful — scored 0–10 by the agent itself at the end of the step.

---

## Smoke Test — Fail Fast (5 minutes max)

Before doing anything else, verify the four integrations this run depends on.

Run each check. If any returns an auth error or timeout, **halt the full run** and post to Slack immediately. Do not proceed to the workflow.

```
Check 1 — Gmail:
  Tool: search_threads, query: "newer_than:1d", max_results: 1
  Pass: any result returned (even empty inbox is OK)
  Fail: 401, 403, or timeout

Check 2 — Wrench contacts:
  Tool: post_contacts_find, input: { "email": "dan@wrench.ai" }
  Pass: any response (found or not found)
  Fail: 401, 403, 5xx, or timeout

Check 3 — HubSpot CRM:
  Tool: get_crm_objects, object_type: contacts, limit: 1
  Pass: response received
  Fail: 401, 403, or timeout

Check 4 — Notion Use Case Library:
  Tool: notion-fetch, target: Use Case Library database ID
  Pass: database rows returned
  Fail: 401, 403, page not found
```

If all 4 pass: proceed. Record smoke test result in session log as `smoke_test: passed`.

If any fail:
```
Post to Slack #product:
  🚨 Salesperson ICP run halted — {DATE}
  Failed integration: {which check}
  Error: {message}
  No session data produced tonight.
```

---

## Part A: Baseline Workflow

*This runs every night. It is the benchmark. Results are comparable across runs.*

### A1. Gmail Inbox Triage

**Tool:** `search_threads`
- Query: `in:inbox newer_than:1d -from:wrench.ai`
- Max results: 20

For each thread with a reply from a non-internal sender: call `get_thread` to read content.

Classify each thread:
- **Hot reply** — prospect replied showing interest or a question
- **Objection** — prospect pushed back or declined
- **Cold** — no reply from this prospect in 24h (do not count toward enrichment queue)
- **New inbound** — first contact from a prospect domain

Capture: sender email, subject, classification, 1-sentence quote from the email body.

### A2. Prospect Enrichment

For each Hot Reply or New Inbound (max 5):

```
a. post_contacts_find — by email
b. post_contacts_enrich_get — full enrichment
c. post_contacts_enrich_lead-score — score + tier + top 3 driving factors
d. post_contacts_personality — communication style + key motivators
e. post_contacts_overview — last activity, enrichment status
```

Build per-prospect card:
```
[Name] — [Title] @ [Company]
Score: [X]/100 ([tier]) | Top driver: [factor]
Style: [communication style]
Email: [1-sentence summary of what they said]
```

### A3. Agent Consultation

For each enriched prospect, call `post_chat_generate`:

```
Prompt template:
"I'm following up with [Name], [Title] at [Company]. Score: [X]/100.
Top driver: [factor]. Communication style: [style].
They wrote: '[email quote]'

Give me:
1. The single most compelling value prop for this person (1–2 sentences)
2. One specific question that shows I've done my homework
3. One objection to anticipate

Keep each to 1–2 sentences."
```

Score each response 1–5: does it reference the specific driver and email content, or is it generic?

### A4. CRM Hygiene

**Tool:** `search_crm_objects`
- Filter: `last_activity_date` older than 14 days, lifecycle = lead or SQL
- Limit: 10, sort by lead score desc

For top 5 stale contacts:
```
a. post_contacts_enrich_get — re-enrich
b. post_contacts_enrich_linkedin → post_contacts_enrich_linkedin_status — check for job change
c. manage_crm_objects — update CRM if title/company changed
   Write verification: fetch the contact back, confirm update landed → append [verified]
```

### A5. Outreach Drafts

For top 3 prospects by lead score (from A2):

Draft a personalized email using:
- Communication style from A2d
- Value prop from A3
- Top driving factor from A2c

Structure:
```
Subject: [references something specific — company, trigger, or situation]
Opening: [1 sentence referencing their specific situation]
Body: [2–3 sentences on value prop matched to their driver]
CTA: [specific, low-friction ask — not "let me know if interested"]
```

**Tool:** `create_draft` (Gmail)
**Write verification:** call `list_drafts` after each creation, confirm draft appears → append `[verified]`

### A6. Score the Baseline

After completing A1–A5, score this run:

| Dimension | 0 | 5 | 10 |
|---|---|---|---|
| Data completeness | All enrichment null | Some fields populated | Full cards for all prospects |
| Score differentiation | All scores identical | Some variation | Clear differentiation |
| Agent insight quality | Generic boilerplate | Company-aware | Specific to driver + email |
| Outreach specificity | Template filler | References role | References driver + style |
| CRM hygiene | No records found | 1–2 updated | 5 reviewed, verified |
| Integration health | >2 failures | 1 degraded | All tools valid |

Record total baseline score (0–60).

---

## Part B: Discovery Step

*This runs every night after the baseline. It tries one unexplored use case variant.*
*The variant changes each night. Over time this builds a map of what works and what doesn't.*

### B1. Fetch the Next Variant

**Tool:** `notion-fetch` — query the Use Case Library database

Filter:
- `ICP` = "Salesperson" OR "Both"
- `Status` = "Exploring"

Sort:
- `Run Count` ascending (least explored first)
- `Latest Run` ascending (oldest attempt first)

Take the first result. Read its `Name` and `Description`.

If no Exploring variants remain (all promoted or gapped): pick the lowest-scoring Promoted variant and re-run it to verify the score holds. Note in the log: "All variants explored — re-validating [name]."

### B2. Execute the Variant

Read the Description field. Follow it exactly as written. Use the same enrichment, CRM, and agent tools as the baseline — the Description specifies what to do differently.

Execute the variant workflow end to end.

### B3. Score the Variant

After executing, score the result 0–10 on a single dimension: **would a real salesperson find this useful?**

- **0–3:** The output was empty, generic, or the workflow step couldn't be completed
- **4–6:** Something came back but it was too vague to act on, or required too much manual follow-up
- **7–8:** Clear, specific output a salesperson could act on directly
- **9–10:** Better than expected — surfaced something genuinely surprising or valuable

Write one honest sentence explaining the score.

### B4. Update the Use Case Library

**Tool:** `notion-update-page` (target: the row for this variant)

Updates:
- `Run Count`: increment by 1
- `Latest Run`: today's date
- `Latest Score`: score from B3
- `Notes`: append `[{date}] Score {X}/10 — {1-sentence explanation}`
- `Status` logic:
  - If score ≥ 7 AND run_count ≥ 2 → set `Status` = **Promoted**
  - If score < 4 AND run_count ≥ 3 → set `Status` = **Gap**
  - Otherwise: leave as **Exploring**

**Write verification:** fetch the row back, confirm `Run Count` incremented and `Notes` updated → append `[verified]`

---

## Session Log — Write to Notion + Slack

### Notion

Create a new page in the **ICP Lab Runs** database:

```
Name: Salesperson Daily — {YYYY-MM-DD}
ICP: Salesperson
Run Date: today
Overall Score: {baseline score}/60
Pass/Fail: {Pass ≥45 / Borderline 30–44 / Fail <30}
Discovery Variant Tried: {variant name}
Discovery Score: {0–10}
Capability Issues: {any smoke test or tool failures}
Status: Complete
```

Page body:
```
## Baseline Results
{Per-prospect cards}
{Agent consultation quality: avg score}
{CRM records reviewed + updated}
{Draft emails created + verified}

## Discovery Step: {variant name}
{What was attempted}
{What came back}
Score: {X}/10 — {explanation sentence}
Status change: {Exploring → Promoted | Exploring → Gap | Exploring (still)}

## Capability Notes
{Any tool degradation or unexpected behavior worth noting}
```

Write verification: fetch the page back, confirm title and both section headings landed → append `[verified]`

### Slack

Post to #product:

```
📋 Salesperson ICP — {DATE}
Baseline score: {X}/60 ({Pass/Borderline/Fail})
Prospects enriched: {count} | Drafts created: {count} | CRM records cleaned: {count}

Discovery: "{variant name}"
Result: {score}/10 — {1-sentence explanation}
{If Promoted: ✅ Promoted to baseline} {If Gap: ❌ Flagged as gap}

🔗 {Notion page link}
```
