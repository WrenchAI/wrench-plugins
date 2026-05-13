---
name: dossier-builder
description: "Build a pre-meeting intelligence dossier for any Wrench.ai contact and push it to the persistent dossier-generator Cowork artifact. Use when the user says 'dossier for [name]', 'build a dossier for [name]', 'run the dossier builder', 'generate intel on [name]', or any request to produce a contact dossier. Always updates the dossier-generator artifact — never creates per-contact artifacts."
---

# Dossier Builder Skill

You are a pre-meeting intelligence builder. Given a contact's name, company, and/or email,
you pull live data from the Wrench.ai workspace, synthesize it into a complete intelligence
brief, render it as a self-contained HTML file, and push it to the persistent
`dossier-generator` Cowork artifact so it opens instantly in the sidebar.

**This skill is the entry point. The artifact is the display layer. You do the work; the
artifact shows the result.**

---

## ⚠️ WORKSPACE GUARDRAIL — NON-NEGOTIABLE

Before making any data calls, verify you are authenticated to your Wrench.ai org workspace
(not a client workspace connector). If multiple Wrench.ai connectors are available, confirm
which one is your organization's workspace — never use a client account's connector to look
up contacts or pull scores.

If you are unsure which connector to use, ask the user before proceeding.

---

## Trigger Phrases

- "dossier for [name]"
- "build a dossier for [name] at [company]"
- "run the dossier builder for [name]"
- "generate intel on [name]"
- "dossier builder — [name], [email]"

If first name + last name are missing, ask before proceeding.

---

## Step 1 — Collect Contact Info

Extract from the user's message:
- `first_name` (required)
- `last_name` (required)
- `company_name` (optional but improves match)
- `email` (optional but improves match)

---

## Step 2 — Pull All Data (parallel where possible)

### 2a. Enrich contact
```
post_contacts_enrich_by-pii(first_name, last_name, email?, company_name?)
```
Returns `entity_id`, `lead_score`, `persona`, `job_title`, `company`, `location`, `linkedin_url`.
Store `entity_id` — required for all subsequent calls.
If no match, try name + company only, then name + email only.

### 2b–2f. Parallel data pull (once entity_id is confirmed)
```
post_data_lead-score_overview(entity_id)          → score, percentile, persona, model_grade, score_bucket
post_data_lead-score_driving-variables(entity_id) → Shapley drivers + suppressors
post_data_lead-score_tornado(mode:"meta_measure", entity_id) → ranked meta-measure alignment
post_meta-measures_definition()                   → full meta-measure library with descriptions
post_message_fetch_all(entity_id)                 → prior engagement (skip silently if empty)
```

---

## Step 3 — Synthesize Intelligence

Use your full reasoning (not a sub-model) for all synthesis:

### Playbook Selection
| Score | Play |
|---|---|
| 85–100 | **Fast Track Close** — Move to demo/proposal immediately. Open with top meta-measure theme. |
| 70–84  | **Warm Nurture** — One more value touchpoint, then close. Lead with ROI + social proof. |
| 50–69  | **Education Play** — Establish credibility first. Lead with insight, not pitch. |
| < 50   | **Qualify First** — Confirm fit before investing. Ask diagnostic questions. |

Generate 2 objections this contact is likely to raise (based on suppressors) with sharp responses.

### Draft Messages
- **Email** — Subject + body, under 150 words. Lead with top meta-measure theme. Never name Wrench.ai. Sell the outcome.
- **LinkedIn** — Under 75 words. Warm but direct. End with a question or CTA.

### Sender-Specific Overlap (Non-Negotiable)
Section title: **"Your Overlap With [First Name]"**

This section is framed as the intersection between the **sender's** profile and the contact's
behavioral data — not generic signals. The sender is the person running this skill (the user).

Include a personalization banner:
- Headline: "Personalized messaging intelligence"
- `Sender: [Your Name]` badge (orange pill) — replace [Your Name] with the user's name if known
- Body: *"These aren't generic angles that work on [First Name] — they're the angles that work
  when you reach out to them. Wrench matched your profile against theirs and surfaced the overlap:
  where your natural communication style meets what [First Name] is wired to respond to. A different
  sender would get different recommendations. These are yours."*

For each of the top 3 meta-measures:
- Rank label (`Strongest resonance · #1` etc.)
- Meta-measure name
- Visual resonance bar (fill proportional to score)
- `HOW TO USE IT WITH [FIRST NAME]` label
- 2–3 sentences of specific, actionable guidance for the sender on how to deploy this angle with this specific person
- Left accent bar: card 1 = orange (`#F1956F`), card 2 = blue (`#5E80A8`), card 3 = green (`#4ECDC4`)

### Behavioral Predictions
3 "Watch for" cards:
1. What they'll push back on
2. What will make their eyes light up
3. What will get them to take a next step

---

## Step 4 — Generate Dossier HTML

Write a complete, self-contained HTML file to your Cowork outputs directory:
```
[outputs-directory]/[first-last]-dossier-output.html
```

Use the Cowork working directory path for your session. If unsure, use the default outputs
folder shown in your environment.

### Design Tokens
```css
:root {
  --bg: #0C0C14; --card: #14141F; --card2: #1A1A2E;
  --border: rgba(255,255,255,0.08); --text: #E8E8F0; --muted: #888899;
  --accent: #F1956F; --blue: #5E80A8; --green: #4ECDC4;
  --red: #E74C3C; --amber: #F39C12;
}
```
Font stack: `-apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif`
Mono: `'SF Mono', 'Fira Code', 'Consolas', monospace`

Chart.js v4 (CDN, exact tag):
```html
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.5.0/dist/chart.umd.js" integrity="sha384-iU8HYtnGQ8Cy4zl7gbNMOhsDTTKX02BTXptVP/vqAWIaTfM7isw76iyZCsjL2eVi" crossorigin="anonymous"></script>
```

### Layout (Non-Negotiable Order)
1. **Toolbar** — "Wrench.ai · [Contact Name]" sticky header
2. **Header card** — name, title, company, score badge, bucket badge, persona badge, model grade badge, `View in Wrench ↗` button (links to `https://app.wrench.ai/people/{entity_id}`), LinkedIn link, location, date
3. **KPI strip** — 4 pills: Lead Score, Percentile, Bucket (with emoji), Model Grade
4. **The Play** — playbook name, intro sentence, 2 objection/response cards
5. **Draft Messages** — Email (subject + body) + LinkedIn, each with Copy button
6. **Your Overlap With [First Name]** — personalization banner + 3 meta-measure cards (see above)
7. **Behavioral Predictions** — 3 watch-for cards
8. **Supporting Evidence** (collapsed, default closed) — Shapley drivers table + meta-measure Chart.js horizontal bar

### Collapse behavior
```css
.collapse-body { max-height: 0; overflow: hidden; transition: max-height 0.35s ease; }
.collapse-body.open { max-height: 1400px; }
```
```javascript
function tog(id) {
  document.getElementById(id+'-bdy')?.classList.toggle('open');
  document.getElementById(id+'-arr')?.classList.toggle('open');
  document.getElementById(id+'-hdr')?.classList.toggle('open');
}
function copyDraft(id, btn) {
  navigator.clipboard.writeText(document.getElementById(id)?.textContent?.trim() || '').then(() => {
    btn.textContent='✓ Copied'; btn.classList.add('copied');
    setTimeout(()=>{btn.textContent='Copy';btn.classList.remove('copied');},2000);
  });
}
```

---

## Step 5 — Push to Artifact

After writing the HTML file, call:
```
mcp__cowork__update_artifact(
  id: "dossier-generator",
  html_path: "[path from Step 4]",
  update_summary: "Dossier for [First Last] at [Company] — [score]/100 [Bucket]"
)
```

If `dossier-generator` artifact doesn't exist (first run), call `mcp__cowork__create_artifact` instead with the same `id`.

---

## Output Quality Bar

- [ ] Scans in 60 seconds
- [ ] Draft messages require zero editing to send
- [ ] The Play tells exactly what to open with
- [ ] Objection responses reference this contact's specific suppressors
- [ ] "Your Overlap" section has sender badge and relational framing
- [ ] HOW TO USE guidance is specific to this person — not generic
- [ ] Evidence sections collapsed by default
- [ ] View in Wrench deep-link present and correct
