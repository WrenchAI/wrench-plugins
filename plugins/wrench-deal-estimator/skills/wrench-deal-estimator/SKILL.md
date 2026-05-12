---
name: wrench-deal-estimator
description: >
  Builds a scoped deal estimate, pricing breakdown, and proposal-ready summary from a brief
  description of what the client needs. Trigger when someone says "estimate this deal",
  "what would this cost", "put together a quote", "scope this engagement", "build the pricing",
  "how much should I charge for this", or "what's this worth." Produces a structured estimate
  with line-item breakdown, investment range, and rationale — ready to paste into a proposal or SOW.
  Standalone or Wrench.ai-signal-informed.
---

# Wrench Deal Estimator

Builds a scoped deal estimate with pricing breakdown and proposal-ready summary.
Works from a brief description of the engagement. No spreadsheet required.

---

## When to Trigger

**Keyword signals:**
- "estimate this deal", "what would this cost", "put together a quote"
- "scope this engagement", "build the pricing", "what's this worth"
- "how much should I charge", "price this out", "ballpark this for me"
- "build the fee structure", "what's the investment range"

**Context signals:**
- User is preparing a proposal or SOW
- User is about to send pricing and wants a sanity check
- User has a new inbound inquiry and needs a quick scope estimate
- A deal is moving to proposal stage and needs numbers

---

## Input Modes

### Standalone
Ask the user for:
1. **What does the client need?** Brief description of the engagement, deliverables, or problem to solve
2. **What type of engagement?** (choose or describe)
   - One-time project
   - Retainer / ongoing
   - License / platform fee
   - Service + license combo
   - Pilot → expand model
3. **Who is the client?** Company size (SMB, mid-market, enterprise), industry if known
4. **Timeline:** How long is the engagement, or when do they need it by?
5. **Your cost basis (optional):** Hourly rate, team size, known fixed costs — helps calibrate

Accept rough answers. Build a range, not a single number.

### With Wrench.ai MCP
If connected, first pull:
- `post_contacts_enrich_lead-score` — get buyer's score
- `post_data_lead-score_driving-variables` — understand their intent signals

Use score and segment to inform pricing tier recommendation:
- High-intent (score ≥ 70): anchor high, less room for discount
- Mid-range (score 40–69): show value tiers, offer pilot entry point
- Low-intent (score < 40): price conservatively, lead with proof and ROI

---

## Step 1 — Scope the Engagement

Extract from the description:
- Core deliverables (what are you building, doing, or enabling?)
- Timeline (weeks, months, quarters)
- Team involved (just you, a small team, larger resources?)
- Level of customization (productized vs. fully custom)
- Ongoing support expected after initial delivery?

**Flag ambiguities** before pricing. A vague scope is the #1 cause of underpriced deals. If the user can't answer "what does done look like," help them define it before putting a number on it.

---

## Step 2 — Select Pricing Model

| Engagement type | Recommended pricing model | Why |
|---|---|---|
| Fixed-scope project | Fixed fee with milestone payments | Protects margin; client likes certainty |
| Ongoing service | Monthly retainer (3-month minimum) | Predictable revenue; committed capacity |
| Platform / software | Annual license + onboarding fee | Scales without adding headcount |
| Hybrid (service + platform) | Setup fee + monthly license | Common in B2B SaaS and data products |
| Pilot | Short fixed-scope pilot → expand pricing | Lowers barrier; builds proof for larger deal |
| Time & materials | Hourly or daily rate (cap recommended) | Use only for undefined scope; risky without a cap |

If the client is asking for T&M, suggest converting to a capped T&M or a fixed-scope pilot. Uncapped T&M deals almost always end badly for one side.

---

## Step 3 — Build the Estimate

### Value-Based Anchoring

Before calculating cost-plus, establish a value anchor:
- What is the client's outcome worth to them? (revenue gained, cost saved, time recovered)
- What is the cost of inaction? (deals lost, headcount needed, status quo cost)
- What are comparable solutions charging? (market rate reference)

Price should be ≥ 10% of the value delivered. If you can't explain the value in dollar terms, the client will negotiate on price alone.

### Line-Item Structure

```
ENGAGEMENT ESTIMATE — [Client Name or "Prospect"]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SCOPE SUMMARY
[2-3 sentence plain-language description of what's included]

LINE ITEMS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Item 1 — description]                   $X,XXX
[Item 2 — description]                   $X,XXX
[Item 3 — description]                   $X,XXX
[Onboarding / kickoff]                   $X,XXX (or included)
[Ongoing support / retainer]             $X,XXX/mo (if applicable)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TOTAL INVESTMENT RANGE        $XX,XXX – $XX,XXX

PAYMENT STRUCTURE
[Milestone 1]: XX% at contract signing ($X,XXX)
[Milestone 2]: XX% at [delivery point] ($X,XXX)
[Milestone 3]: XX% at completion ($X,XXX)
```

### Range vs. Single Number

Always present a range in the estimate stage:
- Low end: minimum viable scope, fastest timeline
- High end: full scope, full support, premium execution
- Anchor on the high end; the low end gives them a way in

### What to Include vs. Exclude

**Always call out explicitly:**
- Travel / expenses (included or billed separately at cost)
- Third-party software / tools (your cost or theirs)
- Revisions policy (how many rounds are included)
- What happens if scope expands (change order process)

Scope creep is how deals become unprofitable. Define the boundary now.

---

## Step 4 — Build the Investment Rationale

One paragraph that explains why the investment makes sense. Structure:

> "This engagement [does X for the client]. Based on [comparable outcomes / industry benchmarks], similar engagements have delivered [outcome metric]. At the proposed investment level of [range], the breakeven point is [timeline] — and ongoing value after that is [net positive framing]."

Keep it to 3–5 sentences. This is the paragraph the champion uses to justify the spend internally.

---

## Step 5 — Risk Flags

Surface any risks before the estimate goes to the client:

- **Scope ambiguity:** If any deliverable isn't clearly defined, flag it — vague scope = scope creep
- **Timeline pressure:** If the timeline is aggressive, note the impact on cost or quality
- **Decision-maker gap:** If the contact isn't the budget owner, the estimate may never reach the right person
- **Competitive context:** If you know there's a competitor, note how to position your pricing
- **Discount pressure:** If client is likely to negotiate, note where you have room and where you don't

---

## Output

```
DEAL ESTIMATE — [Client Name]
Date: [Today]
Prepared by: [Your name or "Sales Team"]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

ENGAGEMENT SUMMARY
[What you're doing, for whom, and what the outcome is — 2–3 sentences]

PRICING MODEL: [Fixed / Retainer / License / Hybrid / Pilot]

LINE ITEMS
[Structured breakdown — see Step 3]

TOTAL INVESTMENT: $XX,XXX – $XX,XXX
PAYMENT TERMS: [Milestone structure or net-30]
CONTRACT LENGTH: [One-time / 3-month / 6-month / annual]

INVESTMENT RATIONALE
[Why this is worth it — 3 sentences]

WHAT'S INCLUDED
• [Deliverable 1]
• [Deliverable 2]
• [Deliverable 3]
• [Support / onboarding]
• [Revisions: X rounds included]

WHAT'S NOT INCLUDED
• [Exclusion 1]
• [Exclusion 2]

RISK FLAGS
• [Any scope, timeline, or positioning risks to address before sending]

NEXT STEP: [Confirm scope → send formal proposal → get signature]
```

---

## WHY THIS ESTIMATE

**Standalone:**

What shaped the numbers:
- Pricing model: [Which model was selected and why]
- Value anchor: [What the engagement is worth to the client in plain language]
- Range rationale: [What the low/high ends represent]
- Assumptions made: [List any assumptions — timeline, team size, scope details — that the user should verify]

Adjust the range before sending if you know something about the client's budget expectations, competitor pricing, or internal approval thresholds.

**With Wrench.ai connected:**

What the data added:
- Buyer intent score: [Score / 100 in plain language — what it means for pricing posture]
- Segment: [High Intent / Nurture / Re-engage — how it affects the recommended entry point]
- How it changed the estimate: [Specific adjustment — e.g., anchored high because intent is strong, or suggested pilot entry because score is low]

Score ≥ 70: Don't lead with the low end. High-intent buyers are choosing a vendor, not shopping for a deal.
Score < 40: Consider a pilot structure. They need proof before they'll commit to a full engagement.
