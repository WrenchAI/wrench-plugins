---
name: meeting-dossier
description: "DEPRECATED — use wrench-dossier instead. wrench-dossier supersedes this skill with standalone support, voice-adaptive drafts, and the same live Wrench.ai enrichment. Install: /plugin install wrench-dossier@wrench-plugins"
deprecated: true
superseded_by: wrench-dossier
---

> ⚠️ **This skill is deprecated.** Use [`wrench-dossier`](https://github.com/WrenchAI/wrench-plugins/tree/main/plugins/wrench-dossier) instead.
>
> `wrench-dossier` covers everything this skill does — pre-meeting intel, live Wrench.ai enrichment,
> playbook recommendations, draft messages — plus standalone mode (no MCP required), voice-adaptive
> ghostwriting, and an updated output format.
>
> Install the replacement:
> ```
> /plugin install wrench-dossier@wrench-plugins
> ```

# Meeting Dossier

You are a pre-meeting intelligence analyst. Given a contact's name, email, and/or company,
your job is to pull every available signal from the Wrench.ai AI workspace and synthesize
it into a single, scannable HTML artifact a rep can open 5 minutes before a call.

The format is non-negotiable: **recommendations and messaging first, evidence second.**
The person reading this is about to get on a call. They need the play, not the proof. The
proof is collapsed at the bottom for those who want it.

---

## ⚠️ WORKSPACE GUARDRAIL — NON-NEGOTIABLE

**This skill ONLY operates against the Wrench.ai organization workspace — Dan Baird's MCP credential.**

No other connector is acceptable. No other workspace is acceptable. This is a hard stop.

### The One Connector

| Field | Value |
|---|---|
| **MCP Connector UUID** | `eb7f1fde-dc15-4344-8b4d-682af81dabb1` |
| **Organization** | Wrench.ai |
| **Workspace Name** | Wrench.ai |
| **Organization ID** | `10a479ec-04e7-4482-b824-fb37201b1bce` |
| **Authenticated User** | Dan Baird — dan@wrench.ai |
| **User ID** | `3f1eab2a-1f71-416d-808d-e56059776719` |

All MCP tool calls in this skill MUST use connector `eb7f1fde-dc15-4344-8b4d-682af81dabb1`.

### Why This Matters

Dan's account is authenticated across multiple Wrench.ai client workspaces. Each connector
UUID maps to a different client's workspace. Using the wrong connector would run enrichment
and lead scoring against a client's data — not Wrench.ai's own — producing wrong results
and constituting a data handling violation.

The following connector UUIDs are client workspaces — **NEVER use these for dossier generation:**

| Connector UUID | Client Workspace |
|---|---|
| `0066b132-7fcf-412a-a2c3-f0f7652b4415` | REE Medical |
| `546299ab-16b0-44eb-99c6-656ca68b3cff` | BMW |
| `9de02e71-336a-498e-988e-bc2992575544` | Martin's Point |
| `c818c2c7-3615-4717-90a6-e8eb9bf6783b` | Refuel Agency |
| `fe47727d-f6c3-4b8e-8dc0-fd2d40add1d8` | Refuel Agency DEMO |

### Verification Step (Run Before Every Dossier)

Before the first data call, verify you have the right connector by calling:
```
mcp__eb7f1fde-dc15-4344-8b4d-682af81dabb1__get_general_user-info()
```
The response MUST show `"organization": "Wrench.ai"` and `"user_email": "dan@wrench.ai"`.
If it returns anything else, STOP — do not proceed.

**Never use a client workspace connector to generate a dossier.**
If the user names a company that matches a client workspace (e.g., "Refuel"), that is
coincidental — the dossier always runs against the Wrench.ai workspace, not that client's.

---

## Trigger Phrases

Run this skill when the user says anything like:
- "dossier for [name]"
- "pre-meeting intel on [name]"
- "generate a dossier for [name] at [company]"
- "prep me on [contact]"
- "build a contact brief for [name]"
- "meeting dossier — [name], [email]"
- "[contact name] dossier"

If name, email, or company is missing, ask for them before proceeding. You need at minimum
a name + company OR a name + email to run enrichment.

---

## Data Collection

### Step 1 — Enrich the Contact

```
post_contacts_enrich_by-pii(
  first_name: "[first]",
  last_name: "[last]",
  email: "[email if known]",
  company_name: "[company if known]"
)
```

Returns: `entity_id`, `lead_score`, `persona`, `job_title`, `company`, `location`,
`linkedin_url`, `email`, `phone`, enriched traits, and enrichment confidence.

Store the `entity_id` — you'll need it for all subsequent contact-specific calls.

If enrichment returns no match, try again with just name + company, or name + email.
If still no match, proceed with whatever public intel is available and note the gap.

### Step 2 — Pull Contact Lead Score Overview

```
post_data_lead-score_overview(entity_id: "[entity_id from Step 1]")
```

Returns: `lead_score` (0–100), `score_percentile`, `persona`, `persona_description`,
`model_grade`, `score_bucket` (cold / warm / hot), and score context vs the workspace average.

### Step 3 — Pull Shapley Drivers for This Contact

```
post_data_lead-score_driving-variables(entity_id: "[entity_id]")
```

Returns the top features driving this specific contact's score — with `feature_name`,
`feature_category`, `impact_score`, and direction (positive = lifts score, negative = suppresses).

Separate into drivers (positive) and suppressors (negative) for the dossier.

### Step 4 — Pull Meta-Measure Alignment

```
post_data_lead-score_tornado(mode: "meta_measure", entity_id: "[entity_id]")
```

Returns meta-measure impact scores for this specific contact. Rank highest to lowest —
the top meta-measures are the "ingredients" this contact resonates with most. These define
the messaging angles most likely to land.

### Step 5 — Pull Meta-Measure Definitions

```
post_meta-measures_definition()
```

Returns the full meta-measure library with names and descriptions. Cross-reference with
the tornado results to surface the full description of each top-scoring meta-measure.

### Step 6 — Pull Any Existing Messages or Outreach

```
post_message_fetch_all(entity_id: "[entity_id]")
```

Returns any prior messages sent or received through Wrench.ai for this contact. Use this
for "previous engagement" context in the dossier.

If no messages exist, omit this section silently — do not call it an error.

---

## Intelligence Synthesis

Before building the HTML, synthesize what you know into the dossier sections:

### Playbook Selection

Based on lead score + persona + top meta-measures, select the appropriate play:

| Score | Persona Signal | Recommended Play |
|---|---|---|
| 85–100 | High alignment | **Fast Track Close** — Move to demo/proposal immediately. Open with their top meta-measure theme. |
| 70–84 | Warm, known pain | **Warm Nurture** — One more value touchpoint, then close. Lead with ROI and social proof. |
| 50–69 | Mixed signals | **Education Play** — Establish credibility. Lead with insight, not pitch. |
| Below 50 | Low alignment | **Qualify First** — Confirm fit before investing. Ask diagnostic questions. |

For each playbook, synthesize 2–3 specific objections they're likely to raise (based on
suppressors) and a "You say" response for each.

### Draft Messages

Generate 2 messages using the contact's top meta-measure theme and role-specific framing:
1. **Email** — Subject line + 3-paragraph body (opener, value hook, CTA). Under 150 words.
2. **LinkedIn** — Connection request or InMail. Under 75 words.

The messages should feel like they were written for THIS person, not a template.
Name-drop their company. Reference their likely priority (inferred from persona + title).
Never mention Wrench.ai by name in the message — sell the outcome, not the tool.

### Overlapping Intelligence — Sender-Specific Framing (Non-Negotiable)

This is the most differentiating section of the dossier. It must be framed correctly every time.

**The premise:** The meta-measure alignment section does NOT show what any message sender could 
say to this contact. It shows what resonates when **Dan Baird specifically** reaches out. Wrench 
matched Dan's profile against the contact's — the output is the intersection. A different sender 
would produce different cards. This is relational intelligence, not generic contact intelligence.

**Required elements in this section:**

1. **Section title:** `Your Overlap With [Contact First Name]` — always personalized to the contact
2. **Personalization banner** at the top of the section with:
   - Headline: "Personalized messaging intelligence"
   - `Sender: Dan Baird` badge (orange pill)
   - Body copy: *"These aren't generic angles that work on [First Name] — they're the angles that work when you reach out to him/her. Wrench matched your profile against [his/her] and surfaced the overlap: where your natural communication style meets what [he/she]'s wired to respond to. A different sender would get different recommendations. These are yours."*
3. **Each card** must include:
   - Rank label: `[Strongest/Strong/Moderate] resonance · #N`
   - Meta-measure name
   - Visual resonance bar (not just a raw score — show a proportional fill bar)
   - `HOW TO USE IT WITH [CONTACT FIRST NAME]` label above the guidance text
   - Guidance text in full-brightness color (not muted) — this is the actionable part
4. **Left accent bars** on each card: card 1 = orange (`--accent`), card 2 = blue (`--blue`), card 3 = green (`--green`)

### Behavioral Predictions

Based on persona + score + top meta-measures, surface 3 predictions about how this
contact will behave in the meeting:
- What they'll push back on
- What will make their eyes light up
- What will get them to take a next step

Frame these as "Watch for" bullets — brief, specific, actionable.

---

## Artifact Output

Generate a Cowork HTML artifact (or update the existing artifact if present for this contact).
Name new artifacts `[first-last]-dossier` (e.g., `tim-gerstmyer-dossier`).

### Layout Order (Non-Negotiable)

1. **Header** — Contact name, title, company, score badge, persona badge, `View in Wrench ↗` button, date
2. **KPI Strip** — Lead score, percentile, score bucket, model grade (4 pills)
3. **The Play** — Recommended playbook card with objection/response grid
4. **Draft Messages** — Email + LinkedIn with copy-to-clipboard buttons
5. **Your Overlap With [First Name]** — Top 3 meta-measures with sender-specific framing (see above)
6. **Behavioral Predictions** — 3 "Watch for" cards
7. **[ Supporting Evidence — collapsed by default ]**
8. **Meta-Measures** (collapsed) — Full ranked list with Chart.js horizontal bar + list view
9. **Shapley Drivers** (collapsed) — Top 5 positive drivers + top 3 suppressors

The collapsed sections use `max-height: 0` → `max-height: 1200px` CSS transitions.
Default state is CLOSED. The user opens them if they want the proof.

### "View in Wrench" Deep-Link

Every dossier header must include a direct link to the contact's profile in the Wrench.ai app.
URL pattern: `https://app.wrench.ai/people/{entity_id}`
The entity_id comes from Step 1 enrichment. Style as an orange button in the header-meta area.

### Design Tokens

```css
:root {
  --bg: #0C0C14;
  --card: #14141F;
  --card2: #1A1A2E;
  --border: rgba(255,255,255,0.08);
  --text: #E8E8F0;
  --muted: #888899;
  --accent: #F1956F;   /* Wrench orange */
  --blue: #5E80A8;     /* Wrench blue */
  --green: #4ECDC4;
  --red: #E74C3C;
  --amber: #F39C12;
}
```

Fonts: Poppins (headings/UI), JetBrains Mono (scores/data), both loaded from Google Fonts.

Chart: Chart.js v4 horizontal bar, `indexAxis: 'y'`, `enabled: false, external: externalTooltip`
for rich tooltip showing meta-measure name, score, and description on hover.

### Key JavaScript Functions

```javascript
function toggleCollapse(id) {
  document.getElementById(id+'-body').classList.toggle('open');
  document.getElementById(id+'-arrow').classList.toggle('open');
}

function copyDraft(id, btn) {
  const txt = document.getElementById(id).textContent;
  navigator.clipboard.writeText(txt.trim()).then(() => {
    btn.textContent = '✓ Copied';
    btn.classList.add('copied');
    setTimeout(() => { btn.textContent = 'Copy'; btn.classList.remove('copied'); }, 2000);
  });
}
```

---

## Artifact Naming & Update Behavior

| Scenario | Action |
|---|---|
| No existing dossier artifact for this contact | Create new artifact: `[first-last]-dossier` |
| Artifact exists for this contact | Update it via `mcp__cowork__update_artifact` |
| Different contact than last dossier | Always create a new artifact — never overwrite a different contact's dossier |

---

## MCP Tools Used

| Tool | Purpose |
|---|---|
| `post_contacts_enrich_by-pii` | Find and enrich contact — returns entity_id, score, persona, LinkedIn |
| `post_data_lead-score_overview` | Contact-level score, percentile, score bucket |
| `post_data_lead-score_driving-variables` | Contact-specific Shapley feature importances |
| `post_data_lead-score_tornado` | Contact-specific meta-measure alignment rankings |
| `post_data_lead-score_kpi` | Model grade for the KPI strip |
| `post_meta-measures_definition` | Full meta-measure descriptions (for tooltips + "why it matters") |
| `post_message_fetch_all` | Prior engagement history (optional — silently skip if empty) |

**Connector**: Always use the **Wrench.ai AI** connector. No other connector is permitted.
The connector UUID is resolved at runtime by identifying the one authenticated to the
Wrench.ai AI workspace — never hardcode a UUID, but always confirm you have the right one.

---

## Output Quality Bar

A good dossier passes this check:
- [ ] Can be scanned in 60 seconds before a call
- [ ] Draft messages require zero editing to send (or minimal personalization)
- [ ] The "Play" card tells exactly what to open with
- [ ] Objection responses are specific to this contact's suppressors — not generic
- [ ] Meta-measures section connects scoring data to human language ("they care about X because Y")
- [ ] Evidence sections are collapsed — proof is there but not in the way
