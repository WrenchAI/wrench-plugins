---
name: workspace-onboarding
description: >
  Fully populates a new Wrench.ai workspace before a client's first login — competitors,
  AI ingredients (meta-measures), seed contacts, AI creatives, and workspace agent configuration —
  so every screen shows relevant data on day one. Trigger when someone says "set up the workspace",
  "populate the workspace", "onboard the workspace", "get the workspace ready", "prepare the demo",
  or "workspace setup for [vertical]." Calls wrench-report (Competitive 360) and
  wrench-metameasure-create as sub-skills.
role: platform
triggers:
  - "set up the workspace"
  - "populate the workspace"
  - "workspace setup"
  - "onboard the workspace"
  - "get the workspace ready"
  - "prepare the demo"
  - "workspace onboarding"
---

# Workspace Onboarding

Fully populates a new Wrench.ai workspace — competitors, AI ingredients, seed contacts,
creatives, and workspace agent — so the platform is useful from the first login.

---

## When to Trigger

**Keyword signals:**
- "set up the workspace", "populate the workspace", "workspace setup"
- "get the workspace ready for [client/vertical]"
- "onboard the workspace", "prepare the demo workspace"
- "every screen is empty", "nothing is showing up yet"

**Context signals:**
- A new workspace was just created and needs initial data
- A client is about to log in for the first time
- Demo workspace screens are blank before a sales call

---

## Before Starting — Required Information

Ask these questions. Accept answers in any order. Do not proceed until you have at least #1 and #2.

1. **Which workspace are you setting up?** (Confirm workspace name via `get_general_user-info` before touching any data)
2. **What vertical / industry?** (Used for competitor defaults, meta-measure themes, and agent configuration)
3. **Client domain?** (Optional but preferred — unlocks brand signals, competitor mentions, and logo)
4. **Known competitors?** (Optional — supplements domain research)
5. **Known buyer profile / ICP?** (Optional — used to refine meta-measures and seed contacts)

⚠️ **Workspace verification is mandatory.** Before any write operation, call `get_general_user-info` and confirm the workspace name matches what the user specified. If you get an unexpected workspace, stop and ask for clarification.

---

## Step 0 — Audit Current State

Before creating anything, check what already exists:

```
get_general_user-info()              → confirm workspace identity (hard stop if wrong)
post_meta-measures_definition()      → list existing meta-measures
get_competitors_list()               → list existing competitors
post_data_availability()             → check data / scoring state
post_assistants_fetch()              → list existing agents
```

**Rules:**
- If workspace already has ≥ 6 meta-measures, skip meta-measure creation and report existing count
- If workspace already has ≥ 3 competitors, skip competitor creation and report existing list
- If a step's data already exists, skip that step and note it in the final report
- Never overwrite existing data without explicit confirmation

---

## Step 1 — Domain Research

If a client domain was provided:

```
WebFetch("https://{domain}")              → homepage: tagline, value prop, primary color
WebFetch("https://{domain}/about")        → company description, positioning
WebFetch("https://{domain}/solutions")    → product lines, use cases
WebFetch("https://{domain}/vs-*")         → competitor mentions
```

**Extract:**
- Company name (exact)
- Tagline / headline
- Primary color hex (from hero, CTAs, or logo)
- Named competitors on comparison pages

**If no domain provided:** Use vertical category defaults (see Category Defaults section below). Note in final report that defaults were used.

---

## Step 1b — Competitive Research via `wrench-report`

Invoke the `wrench-report` skill in Competitive 360 mode:

> "Run wrench-report — Competitive 360 — for [vertical]. Known competitors: [list]. Domain signals: [tagline, differentiators]. Generate battle cards."

The Competitive 360 output provides:
- Battle cards for each competitor
- Creative strategy signals
- Positioning recommendations

**Creative Volume Check:**

For each competitor from the report, check their ad activity:

```
WebFetch("https://www.facebook.com/ads/library/?q={competitor_name}&country=US")
```

Classify:
- **High** — 50+ active ads
- **Medium** — 10–49 active ads
- **Low** — fewer than 10 active ads

Create competitor records ONLY for Medium and High volume. Low-volume competitors will have thin 360 results — flag in final report.

---

## Step 2 — Update Workspace Settings

Update the workspace with client or category default brand identity:

```
post_data_fetch(
  resource: "org_settings",
  updates: {
    company_name: "{extracted or default}",
    domain: "{domain or placeholder}",
    description: "{tagline + value prop}",
    industry: "{vertical}",
    primary_color: "{hex or #5E80A8}"
  }
)
```

If a logo URL was found in Step 1, upload it:

```
post_files_upload(file_url: "{logo_url}", file_type: "logo", label: "{company_name} Logo")
```

> Note: Logo and brand colors may need to be confirmed manually in Org Settings > Brand Kit in the Wrench.ai UI after upload.

---

## Step 3 — Create Competitors

For each Medium + High creative volume competitor (High first):

```
post_competitors_create(
  company_name: "{name}",
  website: "{url}",
  description: "{battle card summary from wrench-report}"
)
```

After creation, verify:

```
get_competitors_list()                → confirm competitors appear
get_creatives_list-competitors()      → check 360 Creative indexing status
```

If 360 indexing shows no results yet, note in final report: "360 Creative indexing in progress — check back in 15–30 minutes."

---

## Step 4 — Meta-Measure Bundle via `wrench-metameasure-create`

Invoke the `wrench-metameasure-create` skill:

> "Run wrench-metameasure-create for [vertical]. Use Competitive 360 output: [battle card themes]. Brand signals: [tagline, differentiators]. Target buyer profile: [seed contact roles]. Create a full bundle."

After the skill completes:

```
post_meta-measures_definition()       → verify ≥ 6 meta-measures created
```

If fewer than 6 are returned, flag in final report and note manual completion is needed.

---

## Step 5 — Enrich Seed Contacts

Use category defaults (see table below). Enrich 3 representative contacts to demonstrate lead scoring:

```
post_contacts_enrich_by-pii(
  first_name: "{first}",
  last_name: "{last}",
  company: "{company}",
  title: "{title}"
)
```

Then trigger scoring:

```
post_contacts_enrich_run()
```

> **Persona generation note:** Persona creation requires 200+ enriched contacts. With 3 seed contacts, persona generation is skipped. To enable personas, import a contact list of 200+ records via CSV or CRM integration after onboarding.

---

## Step 6 — Generate AI Creatives

Use the top High-volume competitor as the creative reference:

```
post_creatives_run(
  creative_type: "display_ad",
  competitor_reference: "{top competitor name}",
  brand_signals: {company_name: "{name}", tagline: "{tagline}", primary_color: "{hex}"}
)

post_creatives_run(
  creative_type: "social_post",
  competitor_reference: "{top competitor name}",
  brand_signals: {company_name: "{name}", tagline: "{tagline}"}
)
```

Poll `post_creatives_run_status()` until complete.

---

## Step 7 — Configure Workspace Agent

Find the workspace agent:

```
post_assistants_fetch()       → look for agent with "architect", "workspace", or "assistant" in name
```

If found, update with vertical-specific instructions:

```
post_assistants_edit(
  assistant_id: "{found_id}",
  instructions: "{vertical-specific instructions from table below}",
  favorite_prompts: ["{prompt 1}", "{prompt 2}", "{prompt 3}", "{prompt 4}"]
)
```

If no agent is found, note in final report: agent creation requires manual setup in Workspace Settings > Agents > Create Agent.

### Agent Configuration by Vertical

| Vertical | Instructions | Favorite Prompts |
|---|---|---|
| AI / Enterprise Software | "Help users identify AI-ready leads, analyze competitor creative in the enterprise software space, and draft outreach for IT and digital transformation decision-makers. Prioritize contacts at companies showing digital transformation intent." | "Who are my highest-scoring AI-ready leads?", "Compare my creative to [top competitor]", "Draft outreach for an IT decision-maker", "What signals are driving my top scores?" |
| Healthcare IT | "Help users identify high-scoring healthcare IT contacts, analyze competitor creative, and draft outreach for clinical and administrative leaders. Prioritize contacts at health systems, hospitals, and clinics." | "Show me high-scoring healthcare contacts", "Compare our creative to [top competitor]", "Draft outreach for a clinical operations lead", "Which contacts show highest clinical fit?" |
| Higher Education | "Help users identify enrollment and student success leads, analyze competitor creative, and draft outreach for enrollment and student success leaders at universities and community colleges." | "Show my top enrollment-ready contacts", "Compare my creative to [top competitor]", "Draft a nurture email for VP of Student Success", "What's driving my lead score model?" |
| CRO / Revenue Operations | "Help users identify sales-ready leads, analyze competitor creative from revenue intelligence platforms, and draft outreach for CROs and sales operations leaders." | "Who are my highest-scoring sales-ready leads?", "Compare my creatives to [top competitor]", "Draft outreach for a CRO persona", "Show me my pipeline's score distribution" |
| B2B Marketing / ABM | "Help users identify marketing-qualified leads, analyze competitor creative from B2B marketing platforms, and draft outreach for CMO and VP Marketing personas." | "Who are my top marketing-qualified leads?", "Compare my brand to [top competitor] ads", "Draft a demand gen email for a CMO", "What meta-measures are most predictive?" |
| [Custom vertical] | Adapt the instructions above to the specific vertical — focus on the buyer's role, their top concerns, and the competitors in their space. | Create 4 prompts that address the most common questions a sales rep would have about their pipeline in this vertical. |

---

## Step 8 — Final Report

```
WORKSPACE SETUP REPORT — {Workspace Name}
Category: {vertical}
Date: {today}
Domain researched: {domain or "none — used category defaults"}

WHAT WAS CONFIGURED
✅ Competitors: {N created} ({H High-volume, M Medium-volume})
✅ Meta-measures: {N created via wrench-metameasure-create}
✅ Seed contacts: 3 enriched — lead scores: {score1}, {score2}, {score3}
✅ AI Creatives: display ad ✓, social post ✓
✅ Media library: {logo uploaded / not found}
{✅/⚠️} Workspace agent: {updated / not found — manual step needed}

WHAT WAS SKIPPED
⏭️ Persona generation: requires 200+ contacts (current: 3)
   → Next: Import contact list via CSV or CRM integration

WHAT THE CLIENT SEES ON FIRST LOGIN
- Lead Scoring: {N} scored contacts with data
- 360 Creative: {N} competitors indexed (15–30 min to fully populate)
- Contacts: 3 enriched seed contacts with scores
- Creatives Library: 2 AI-generated examples
- Workspace Agent: {configured / needs manual setup}

NEXT MANUAL STEPS (if any)
{List any steps that could not be completed programmatically}
```

---

## Category Defaults

Use when no domain is provided.

### Default Competitors (High creative volume first)

| Vertical | Competitors |
|---|---|
| AI / Enterprise Software | Microsoft Copilot, Salesforce AI, HubSpot AI, Notion AI, Glean, Writer |
| Healthcare IT | Epic, Oracle Health, Veradigm, PointClickCare, Health Catalyst |
| Higher Education | EAB, Ellucian, Civitas Learning, Salesforce Edu, Anthology |
| CRO / Revenue Ops | Gong, Outreach, Salesloft, Clari, Apollo, ZoomInfo |
| B2B Marketing / ABM | HubSpot, 6sense, Demandbase, Marketo, Bombora, Drift |

### Default Seed Contacts (3 per workspace)

| Vertical | Contact 1 | Contact 2 | Contact 3 |
|---|---|---|---|
| AI / Enterprise Software | CTO, Fortune 500 enterprise | Director of Digital Transformation, mid-market | VP IT, manufacturing |
| Healthcare IT | CMIO, regional health system | Clinical Ops Lead, regional hospital | VP IT, clinic network |
| Higher Education | VP Enrollment, state university | VP Student Success, community college | CIO, private university |
| CRO / Revenue Ops | CRO, Series B SaaS | VP Sales, mid-market B2B | Sales Ops Director, growth-stage tech |
| B2B Marketing / ABM | CMO, B2B tech company | VP Marketing, growth-stage SaaS | Head of Demand Gen, enterprise software |

---

## Success Criteria

All 7 checks must pass for the workspace to be marked READY:

| Check | Tool | Target |
|---|---|---|
| Correct workspace confirmed | `get_general_user-info` | Workspace name matches what user specified |
| Competitors created | `get_competitors_list` | ≥ 3 Medium/High-volume competitors |
| 360 Creative indexed | `get_creatives_list-competitors` | Results returned for ≥ 1 competitor |
| Meta-measures created | `post_meta-measures_definition` | ≥ 6 entries |
| Contacts enriched + scored | `post_contacts_enrich_overview` | 3 contacts, score > 0 |
| Creatives generated | `post_creatives_list` | ≥ 2 creatives |
| Agent configured | `post_assistants_fetch` | Category-specific prompts visible |

If any check fails, document the gap and provide the manual completion path.
