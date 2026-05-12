---
name: deal-strategist
description: >
  Selects the right playbook, recommends the next step, and drafts personalized outreach
  for any deal — using pasted deal notes or live CRM data. Trigger when someone says
  "what's the next step for [deal]", "what should I send [contact]", "prep for my
  [company] call", "draft a follow-up", "which playbook", "what's the play here",
  or any time a deal needs a recommended action, messaging, or strategy.
  Personalizes to contact archetype. Standalone or Wrench.ai-powered.
---

# Deal Strategist

Selects playbook, recommends next step, and drafts personalized outreach for any deal.
Works from pasted notes or a live CRM record.

---

## When to Trigger

**Keyword signals:**
- "what's the next step for [deal]", "what's the play here"
- "what should I send [contact]", "draft a follow-up for [company]"
- "prep for my [company] call", "which playbook should I use"
- "what do I say to [contact]", "how do I move this deal forward"
- "deal strategy", "next move on [company]"

**Context signals:**
- User names a specific deal or company and asks what to do next
- User just finished a call and asks "what now"
- User shares deal notes and wants a recommended action

---

## Input Modes

### Standalone (no CRM)
Ask the user to paste or describe:
- **Deal:** Company name, contact name + title, deal value (approximate)
- **Stage:** Where is this in the pipeline? (e.g., first outreach, demo done, proposal sent, in negotiation)
- **Last contact:** When did you last interact, and how did it go?
- **What you know about the contact:** Their communication style, priorities, objections raised, what resonated
- **Your goal for next interaction:** What do you want to happen after the next message or call?

Accept notes in any format — bullet points, stream of consciousness, a copied email thread, CRM export snippet. Extract what you can and ask only for what's missing.

### With Wrench.ai MCP
If the Wrench.ai MCP is connected, before asking for input:
1. `post_contacts_enrich_by-pii` — look up the contact by name + company
2. `post_contacts_enrich_lead-score` — get their current lead score
3. `post_data_lead-score_driving-variables` — pull what's driving the score
4. Use persona, segment, and top meta-measure to calibrate playbook selection and draft angle

---

## Step 1 — Establish Deal Context

Extract from pasted notes or CRM data:
- Stage in pipeline
- Contact archetype / communication style (infer from notes if not stated)
- Last communication date and channel
- Open commitments or unanswered questions
- Any objections raised
- Deal urgency signals (timeline, budget discussion, decision-maker engaged)

---

## Step 2 — Select Playbook

Match deal to a playbook based on Stage + Buyer Type + Urgency.

**Playbook selection logic:**

| Stage | Buyer signal | Recommended playbook |
|---|---|---|
| First outreach | Cold / no context | Cold outreach: hook → proof → CTA |
| First outreach | Warm intro / referral | Warm activation: connect the dots → light ask |
| Demo scheduled | Any | Pre-demo: set agenda, confirm pain, prep open questions |
| Post-demo | Strong interest | Momentum: recap value, surface next step, trail close |
| Post-demo | Lukewarm / delayed | Re-engage: address the real hesitation, provide specific proof |
| Proposal sent | Awaiting feedback | Follow-up sequence: check in → add value → create mild urgency |
| Negotiation | Active discussion | Close sequence: respond to objections, make it easy to say yes |
| Stalled | No response >10 days | Breakup + re-engage: low-ask check-in or honest breakup note |

If the deal context doesn't map cleanly to one playbook, surface the two closest options with a one-line rationale for each. Let the rep confirm before drafting.

---

## Step 3 — Recommend Next Step

Output a formatted recommendation block:

```
NEXT STEP RECOMMENDATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Action: [What to do — send email, schedule call, share asset, etc.]
To: [Contact name, title, company]
Channel: [Email / LinkedIn / Phone]
Timing: [When to send — immediately, tomorrow AM, end of week]
Angle: [The specific hook or value frame for this contact]
Why this: [One sentence explaining why this is the right move right now]
```

Base the recommendation on:
- Stage exit criteria (what needs to happen to advance)
- Days since last contact
- Contact's communication style / archetype
- Open commitments or unanswered questions from last interaction
- Urgency signals in the deal

---

## Step 4 — Surface Supporting Content

Recommend content assets to attach or reference:

- **Case study:** Find an analogous win — same vertical, similar company size, comparable problem
- **Data point:** A specific metric that hits their top concern (ROI, speed, cost reduction)
- **Comparison/battle card:** If a competitor has come up, address it directly
- **Demo/proof asset:** Video, walkthrough, or sample if they're in evaluation stage

If you don't know what assets exist, ask: "Do you have a case study or proof point from a similar company I can reference in the draft?"

---

## Step 5 — Draft (Requires Approval)

Do not draft until the rep has confirmed the next step recommendation. Once confirmed:

**Personalization checklist:**
- Contact name + company in subject/opener
- Pain point specific to their role and situation
- Social proof from an analogous deal or customer
- One concrete ROI or outcome number if available
- Single, specific CTA
- Tone and framing matched to contact archetype

**Contact archetype persuasion modes:**

| Archetype | Lead with | CTA style | Tone |
|---|---|---|---|
| Executive / Decision-maker | Headline risk + urgency | "15 minutes?" | Direct, no fluff |
| Data-driven / Analyst | Methodology + proof | "Want the full breakdown?" | Precise, sourced |
| Champion / Advocate | Peer wins + team visibility | "Happy to arm you with this" | Collaborative |
| Skeptic / Negotiator | Acknowledge the concern + counter | "Fair point — here's how others handled it" | Honest, direct |
| Relationship-builder | Warmth + shared context | "Grab 20 minutes?" | Conversational |

**Draft format:**
- Subject line (if email)
- Message body (≤150 words for email, ≤75 words for LinkedIn)
- Optional P.S. line for proof point or urgency

---

## Output

```
DEAL: [Company] — [Contact Name]
STAGE: [Current stage]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PLAYBOOK: [Name] — [One-line rationale]

NEXT STEP:
  Action: [Specific action]
  To: [Contact]
  Channel: [Email / LinkedIn / Phone]
  Timing: [When]
  Angle: [The hook]
  Why: [One sentence]

CONTENT TO REFERENCE:
  • [Asset 1 — type and what to use it for]
  • [Asset 2 — type and what to use it for]

DRAFT: (awaiting your approval of the next step above)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## WHY THIS RECOMMENDATION

**Standalone mode:**

What drove this playbook and next step:
- Stage signal: [What the current stage tells us about where you are]
- Contact signal: [What we know about how this person communicates and decides]
- Timing signal: [What the recency of contact and deal activity tells us]
- Gap: [The specific thing missing that, if addressed, advances the deal]

What's inferred: [Any assumptions made from limited context — flag these so you can correct them]

You own the call. Adjust the draft angle if something doesn't match what you know from the relationship.

**With Wrench.ai connected:**

What drove this recommendation:
- Lead score: [Score / 100 — what it means in plain language]
- Top engagement signal: [The meta-measure that's driving their score — translated to plain language]
- Persona: [Contact archetype from Wrench.ai data]
- Why this angle: [How their live data shaped the playbook selection and draft hook]

Score up or down since last contact? [Plain language — not a number, a direction]

The Wrench.ai data adds signal about what's actually resonating with this person — but you've been in the room. If something doesn't match, trust your read.
