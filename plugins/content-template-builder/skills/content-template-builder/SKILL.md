---
name: content-template-builder
description: >
  Builds conversion-optimized B2B outreach templates — email and LinkedIn — calibrated to
  buyer archetype, deal stage, and vertical. Trigger when someone says "create a template",
  "build an email template", "make a template for [ICP]", "build outreach for [vertical]",
  "write a template I can reuse", or pastes a message and says "make a template from this."
  Produces a MASTER base template plus archetype-specific variants ready for any rep to
  customize and send. Standalone or Wrench.ai persona-calibrated.
---

# Content Template Builder

Builds reusable B2B outreach templates calibrated to buyer archetype, deal stage, and vertical.
Each build produces a MASTER base + archetype variants ready for any rep to adapt and send.

---

## When to Trigger

**Keyword signals:**
- "create a template", "build an email template", "make a template for [ICP]"
- "build outreach for [vertical]", "write a template I can reuse"
- "template for [stage] — post-demo, proposal follow-up, cold outreach, etc."
- "make a template from this" (user pastes an email or message)
- "I keep writing the same email — help me template it"
- "content template for [persona] buyers"

**Context signals:**
- A deal stage is being covered for the first time (no existing template)
- Rep is repeating the same outreach manually and wants to systematize it
- User pastes an email they wrote and wants it converted to a reusable template

---

## Input Modes

### Build from scratch
Ask the user for:
1. **What's this for?** Stage + scenario (cold outreach, post-demo follow-up, proposal send, stalled deal, re-engage, etc.)
2. **Who's it going to?** ICP / buyer type (title, vertical, company size)
3. **Channel?** Email, LinkedIn, or both
4. **Tone goal?** Direct / urgent / educational / conversational
5. **Key message?** The one thing this template needs to land (the core value prop, the specific ask, the proof point)

### Build from an existing message
If the user pastes an email or LinkedIn message:
- Extract the structure, the hook, and the CTA
- Identify what's working (specific vs. generic, clear ask, relevant proof)
- Build the MASTER template that preserves the best elements but works for any contact in this ICP

### With Wrench.ai MCP
If connected, pull persona distribution:
- `get_personas_get` — what archetypes are dominant in this workspace?
- `post_data_lead-score_persona-distribution` — what percentage of ICP is each archetype?

Use archetype distribution to decide which variants to prioritize. If 60% of the ICP is Executive/Commander, that variant should be the most polished.

---

## Step 1 — Build the MASTER Template

The MASTER is the archetype-neutral base. Every variant builds from this.

**MASTER structure:**

```
SUBJECT: [Specific, not clever — state what's in it for them]

OPENER (1 sentence):
[Something specific to their world — their vertical, their role, a recent trigger]
[Avoid: "I hope this finds you well" / "Just reaching out" / "I wanted to connect"]

HOOK (1-2 sentences):
[The tension or problem — what's making their life harder right now]
[Connect it to something they care about: revenue, time, risk, team performance]

VALUE (1-2 sentences):
[What you do about it — specific, not vague]
[One concrete outcome or proof point — number, company name, timeframe]

CTA (1 sentence):
[One specific ask — not "let me know if you have questions"]
[Good: "Worth 15 minutes Thursday?" / "Want the case study?" / "Reply yes and I'll send the details"]

CLOSE:
[First name only — no titles, no "Best regards"]
```

**MASTER template rules:**
- ≤ 150 words total (email), ≤ 75 words (LinkedIn)
- One CTA only — never two asks in one message
- No passive voice ("I wanted to reach out" → "I'm reaching out")
- No hedging ("might be worth", "could potentially") — state it directly
- No "let me know" — every CTA is specific

---

## Step 2 — Build Archetype Variants

Take the MASTER and create 3–5 variants calibrated to contact archetype. Each variant changes:
- The hook angle (what problem / motivation leads)
- The proof type (peer social proof vs. data vs. methodology vs. authority)
- The CTA phrasing (aggressive vs. collaborative vs. curious)
- The tone (direct vs. warm vs. precise)

### The 5 B2B Archetypes

| Archetype | What they care about | Hook angle | Proof type | CTA style |
|---|---|---|---|---|
| **Executive / Commander** | Revenue, risk, competitive position | Headline outcome + urgency | Named customer at similar company | "15 minutes?" — short and direct |
| **Data Analyst / Researcher** | Methodology, accuracy, defensibility | The problem with how it's done today | Study, benchmark, methodology detail | "Want the full breakdown?" |
| **Champion / Advocate** | Team performance, visibility, peer credibility | What the team is missing / leaving on the table | Peer company + team outcome | "Happy to walk you through how [Company] did it" |
| **Skeptic / Negotiator** | Value for money, proof before commitment | Acknowledge the skepticism directly | Specific ROI, measurable outcome, risk reduction | "Fair question — here's the specific result" |
| **Relationship Builder** | Trust, shared context, warmth | Something personal or shared | Story-based, named outcome, conversational | "Grab 20 minutes?" |

### Variant format:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VARIANT: [Archetype Name]
BEST FOR: [Title / role type this fits]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SUBJECT: [Variant subject line]

[Full message — ≤ 150 words for email, ≤ 75 for LinkedIn]

WHY IT WORKS FOR THIS ARCHETYPE: [1 sentence on the specific conversion principle applied]
PERSONALIZE: [The one thing to swap before sending — name, company, trigger event]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Step 3 — LinkedIn Adaptation (if requested)

LinkedIn messages have different constraints:
- ≤ 300 characters for connection request note
- ≤ 75 words for first InMail message
- No subject line — the opening line IS the hook
- Avoid: attachments, links, or PDFs in first message
- The goal of the first LinkedIn message is always to get a reply — not to close

Create a LinkedIn variant for each archetype:
- Shorter, warmer version of the email hook
- Same CTA but worded for a more casual channel
- Connection note version (≤ 300 characters): one sentence on why you're connecting + the ask

---

## Step 4 — Usage Guide

After producing the templates, provide a brief guide:

```
HOW TO USE THESE TEMPLATES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Stage: [What deal stage / scenario this template covers]
Channel: [Email / LinkedIn / Both]
Archetype guide: [Quick cheat sheet — "If the contact is a CRO → use Executive variant"]

PERSONALIZATION CHECKLIST (before every send):
□ Replace [Company] with their actual company name
□ Replace [Title] with their actual title
□ Add one specific trigger (their recent news, a shared connection, a relevant metric)
□ Confirm the CTA matches where you are in the relationship
□ Read it out loud — if it sounds like a template, rewrite the opener

DO NOT SEND AS-IS. Every template needs at least one specific detail added.
```

---

## Output Structure

```
CONTENT TEMPLATE — [Stage] × [Vertical/ICP]
Channel: [Email / LinkedIn / Both]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

MASTER TEMPLATE (archetype-neutral base)
[Full MASTER — see Step 1 structure]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

ARCHETYPE VARIANTS
[Variant 1 — Executive / Commander]
[Variant 2 — Data Analyst / Researcher]
[Variant 3 — Champion / Advocate]
[Variant 4 — Skeptic / Negotiator]
[Variant 5 — Relationship Builder] (optional — include if relevant to ICP)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

USAGE GUIDE
[See Step 4 structure]
```

---

## WHY THESE TEMPLATES

**Standalone:**

What drove the structure and variants:
- Stage context: [What the deal stage tells us about where the buyer is in their decision]
- ICP profile: [What we know about this buyer type — what they care about, how they decide]
- Conversion principle applied: [The specific principle behind the CTA, hook, and proof type chosen]
- What's inferred: [Any assumptions about the ICP that the user should verify before using]

The MASTER works for most contacts. Use the archetype variant when you know enough about the individual to match their style — a cold contact gets the MASTER, a researched contact gets the variant.

**With Wrench.ai connected:**

What the data shaped:
- Dominant archetype in your ICP: [The most common persona in this workspace — build the most polished variant for them]
- Lead score distribution: [Whether high-scorers skew toward certain archetypes — affects which variant to prioritize]
- Top meta-measure themes: [The behavioral signals driving scores in this workspace — surfaced in the hook angle]

When the data shows your top-scoring contacts are Researchers, the Researcher variant should be your default — not an afterthought.
