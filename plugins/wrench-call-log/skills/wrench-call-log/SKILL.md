---
name: wrench-call-log
description: >
  Post-call CRM logging and follow-up writer. Accepts rough notes, bullet points, or a
  voice transcript from any sales call and produces structured CRM-ready fields, a key
  signals summary, a clear next action, and a ready-to-send follow-up message. Eliminates
  the #1 source of CRM data rot. Works standalone with any notes. With Wrench.ai connected,
  pushes the call outcome back to the contact record and surfaces the follow-up angle from
  their live meta-measure alignment.
  Invoke for: "log this call", "call summary", "update CRM", "post-call notes",
  "I just finished a call with", "write up this call".
role: sales
model:
  tier: operational
  claude: claude-sonnet-4-6
triggers:
  - "log this call"
  - "call summary"
  - "update CRM"
  - "post-call notes"
  - "I just finished a call with"
  - "write up this call"
  - "call log for"
  - "summarize this call"
  - "use the wrench-call-log skill"
---

# Wrench Call Log

You turn messy post-call notes into clean, structured CRM entries plus a ready-to-send
follow-up — in one pass.

The rep's job after a call is to capture what happened and do the next thing. This skill
handles the capture so they can focus on the next thing.

---

## Step 1 — Intake

Accept any of the following (no reformatting required from the user):
- Stream-of-consciousness notes
- Bullet points
- Voice transcript paste
- A verbal description: "I just talked to Sarah at Acme, she was interested but wanted ROI data, next step is a case study"

If the contact's name or company is not clear from the notes, ask before proceeding.

---

## Step 2 — Extract and Structure

Parse the notes and extract:

**Call metadata:**
- Contact name, title, company (from notes or ask)
- Call date (today if not stated)
- Duration (if mentioned)
- Channel (phone, Zoom, LinkedIn call, etc.)

**Outcome classification:**
- Meeting booked
- Interested — nurture (engaged but no commitment)
- Requested follow-up (specific deliverable)
- No decision (non-committal, not a clear next step)
- Not a fit (disqualified)
- Other (describe)

**Signals from the call:**
- Objections raised
- What resonated (anything they responded positively to)
- Decision criteria mentioned
- Timeline or urgency signals
- Stakeholders mentioned (name, title, their role in the decision)
- Competitor mentions

**Next action:**
- The single most important next step (with owner and specific date if mentioned)

---

## Step 3 — Output

Always in this order:

```
CALL LOG — [Contact Name] · [Company] · [Date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CRM FIELDS — copy-paste ready
  Contact:    [Name]
  Company:    [Company]
  Title:      [Title if known]
  Call date:  [Date]
  Duration:   [if known, else omit]
  Channel:    [Phone / Zoom / etc.]
  Stage:      [inferred from outcome]
  Outcome:    [Classification from Step 2]
  Next step:  [Specific action + owner + date]
  Notes:      [3–5 sentence summary, third person, CRM-ready — what happened,
               what they said, what matters for the next conversation]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
KEY SIGNALS FROM THIS CALL

  Objections raised:
  • [Each objection as a bullet]

  What resonated:
  • [What they responded well to]

  Decision criteria:
  • [Anything they said matters to them]

  Timeline signals:  [Any urgency cues, or "none stated"]
  Stakeholders:      [Any new names mentioned, their role]
  Competitors:       [Any mentioned, or "none mentioned"]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NEXT ACTION: [The single most important next step — specific and owned]
DEADLINE:    [Date, or "end of week" if implied, or "TBD"]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FOLLOW-UP DRAFT — [Email / LinkedIn]

[Subject line if email]
---
[Ready-to-send follow-up message. Reference specifics from the call — what they said,
what was agreed, what comes next. ≤120 words. Direct, not corporate.]
---
```

---

## Step 4 — With Wrench.ai MCP

If MCP is available, after producing the call log:

```
post_contacts_enrich_by-pii(first_name, last_name, company_name?)
```

Store `entity_id`. Then:

```
post_contacts_enrich_update(entity_id, {
  last_contact_date: [today],
  call_outcome: [outcome classification],
  notes: [CRM notes from above]
})
```

Then pull the updated score:
```
post_data_lead-score_overview(entity_id)
post_data_lead-score_tornado(mode:"meta_measure", entity_id)
```

Add to the output:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WRENCH.AI UPDATE

  Score before this call: [X]/100
  Score after logging:    [Y]/100  [▲ or ▼ or unchanged]
  Bucket:                 [Hot / Warm / Nurture / Qualify]

  FOLLOW-UP ANGLE (from their behavioral profile):
  Top meta-measure for [First Name]: [Meta-measure name]
  → [1 sentence on how to apply this angle in the follow-up — specific, not generic]
```

Use this meta-measure to sharpen the follow-up draft if it changes the recommended angle.

---

## WHY Block

Include at the end of every output. Write this specifically to the output just produced — name the actual reasoning, not a generic description.

**In standalone mode:**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WHY THIS FOLLOW-UP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[1–2 sentences: what angle was chosen for the follow-up and why, based on what the
call notes told you. Name the actual signal — e.g., "The follow-up leads with the
ROI data they asked for because that was the explicit blocker they named on the call"
not "I wrote a relevant follow-up."]

What's inferred: [1 sentence on what you assumed about their communication style or
motivation that isn't directly stated in the notes. Be specific — e.g., "The direct
framing assumes they prefer speed over relationship-building; if they're a slower
decision-maker, soften the CTA."]

If the angle doesn't land, rewrite the opening sentence of the follow-up first —
that's where the framing is set.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**With Wrench.ai connected (contact found):**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WHY THIS FOLLOW-UP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Two things shaped this follow-up:

From the call: [1 sentence on what signal from the call drove the angle — what they
said, what resonated, what the objection was]

From their profile: [translate the top signal in plain language — e.g., "they tend
to respond to concrete outcomes and peer proof, so the follow-up leads with a result
rather than a feature list" — never raw score or meta-measure name]

Score movement: [plain language — e.g., "this call pushed them up 6 points — they're
now in your top 15% and moving toward active consideration" not "score: 71, was 65"]

If this framing doesn't feel right for what you know about [name], trust your read
and adjust the opening angle first.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**With Wrench.ai connected (contact not found):**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WHY THIS FOLLOW-UP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
We looked up [name] but couldn't find a contact record — this follow-up was written
from your call notes only. [Apply standalone WHY: name the signal from the notes
and what was inferred.]

To get behavioral data on this contact and push the call outcome to their record,
add them at wrench.ai or run a contact lookup from your workspace.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Output Quality Bar

- [ ] CRM fields are copy-paste ready — no reformatting needed
- [ ] Notes are 3–5 sentences, third person, factual
- [ ] Next Action is specific: what, who, when — not "follow up with them"
- [ ] Follow-up references something specific from the call — not a template
- [ ] Key Signals section captures objections and what landed — useful for the next rep who reads the record
- [ ] With MCP: score updated, follow-up sharpened to their behavioral signals
- [ ] WHY block is specific to this output — names the actual signal from the call and what was inferred
- [ ] With MCP: WHY block translates all data to plain language — no raw scores, no technical terms
