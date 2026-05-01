---
name: wrench-ux-constitution
description: "Wrench.ai UX governance skill. Apply this skill whenever making any design, copy, agent, or engineering decision on the Wrench.ai platform. This skill encodes the UX Constitution (11 principles), the Six Pillars initiative, the Seven Empirical Gaps, and the pre-ship checklist. Trigger when someone says \"does this pass the constitution\", \"run the checklist\", \"review this design\", \"is this on-brand UX\", \"check this against our principles\", or any time a feature, flow, component, or agent message is being designed, reviewed, or shipped. Also trigger proactively whenever a PR, design review, or agent prompt update is in progress."
---

# Wrench.ai UX Constitution Skill

This skill is the single source of truth for how Wrench.ai **feels, behaves, and communicates** —
across every surface: product UI, agent interactions, support tools, onboarding flows, error
states, and live tests. Every feature, every message, every animation is measured against these
principles before it ships. When there is a disagreement about a design decision, this document
settles it.

**Paired with the brand DNA**: The *visual* source of truth (colors, typography, spacing,
radius, shadows, chart styling, artifact templates) lives in the `wrench-dna` GitHub repo under
`docs/brand/`. This skill governs behavior and experience; the DNA governs pixels. The two are
designed to cross-reference — when this skill and the DNA disagree on a visual token, the DNA
wins and this skill is out of date. Open a PR to realign.

Authoritative DNA references:
- Design tokens (CSS): https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/design-tokens.css
- Artifact governance: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/artifact-governance.md
- Colors: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/colors.md
- Typography: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/typography.md
- Voice and tone: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/voice-and-tone.md
- Component library: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/component-library.md
- Style guidelines: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/style-guidelines.md

---

## How to Use This Skill

When this skill is triggered, Claude should:

1. Read the relevant section(s) for the task at hand
2. Run the applicable **principle tests** against the thing being reviewed
3. Flag any violations against the **anti-patterns**
4. Apply the **pre-ship checklist** before signing off
5. Reference the **Six Pillars** and **Seven Empirical Gaps** for feature-level decisions
6. For any *visual* decision (color, font, radius, spacing, shadow, chart styling), defer to
   the brand DNA files above — do not invent values
7. Never approve a ship if any checklist item is unchecked

These are not guidelines. They are constitutional rules.

---

## Part 1 — The UX Constitution (11 Principles)

### Principle 1: The User Always Knows What's Happening

**The rule**: At every moment, in every state, the user can answer three questions without clicking
anything: What is the system doing right now? Did it succeed or fail? What do I do next? If any of
those three are unclear, the UX has failed.

**The test**:
- Cover the screen and ask a new user: "Is anything running?" They should be able to tell you.
- Remove all loading spinners. Does the user know the system is working? If not, we've
  under-communicated.
- Pull the network plug mid-operation. What does the user see? Is it honest? Is it helpful?

**The anti-pattern**: Silent background processes. Spinners with no context. Success states with no
confirmation. Error messages that disappear before the user reads them. HTTP error codes in the UI.

**Applies to**: All product states, all agent interactions, all support flows, all background jobs.

---

### Principle 2: Own the Error, Every Time

**The rule**: When something goes wrong, Wrench takes responsibility for the user's experience —
regardless of whose fault it is. Every error message is written in first person, communicates
cause, communicates next steps, and — when it's our fault — says so plainly.

**The test**:
- Read every error message out loud. Does it sound like a human wrote it?
- Would the user know if this was caused by us, a third party, or something they did?
- Does the error message make the user feel stupid or at fault when the system failed?

**Error attribution framework**:
- Our fault → "This is on us. [What happened]. We've been notified and are working on it."
- Third-party fault → "[Provider] is experiencing issues outside our control. Here's what you can
  do in the meantime."
- User action needed → "[Specific thing] needs attention. Here's how to fix it in 60 seconds."

**The anti-pattern**: Raw HTTP status codes. Stack traces. "Something went wrong." "An error
occurred." Error messages that imply user error when the system failed.

**Applies to**: All error states, toast messages, empty states, agent responses, network failures.

---

### Principle 3: Never Make the User Feel Dumb

**The rule**: Wrench is built on Shapley values, embeddings, and vector similarity. Our users run
marketing campaigns. The gap between those two realities is our responsibility to bridge —
completely, on every surface. If a user needs to understand a technical concept to use a feature,
we've failed the feature.

**The test**:
- Show the UI copy to someone who has never heard of Wrench. Do they understand what they're being
  asked to do?
- Run a readability test on all primary UI copy. Target: 6th grade or below.
- Look for any of these words in user-facing UI and replace every one:
  API, embedding, vector, Shapley, pipeline, enrichment job, model run.

**The canonical language map**:

| Never say | Always say |
|---|---|
| Shapley values | Signal strength |
| Meta-measures | Ingredients |
| Embeddings | How your AI thinks about your brand |
| Enrichment job | Contact lookup |
| Model run | AI scoring update |
| Vector similarity | How well a contact matches your profile |
| API error | Something went wrong on our end |
| Pipeline | Workflow |
| Data source | Contact list |
| Webhook | Automatic update |

**The anti-pattern**: Technical jargon in primary UI. Tooltip-free field labels. Empty states with
no explanation. Forms that ask for things without explaining why.

**Applies to**: All UI copy, all agent responses, all error messages, all onboarding flows, all
tooltips, all email notifications.

---

### Principle 4: Time Is the User's Most Valuable Asset

**The rule**: Every field we ask a user to fill out costs them something. Every step they take
before reaching value is friction we chose to impose. The goal is always: minimum inputs, maximum
outputs, as fast as possible.

**The test**:
- Time a new user from account creation to their first useful output. If it takes more than 15
  minutes, the onboarding is failing.
- For every required field: can we default it? Can we derive it? Can we ask for it after first
  value is delivered? If any answer is yes, the field should not be required at that point.
- Count the decisions a user must make before seeing their first lead score. Target: 3 or fewer.

**Progressive disclosure principle**: Show what matters now. Reveal everything else only when it's
relevant. Advanced configuration always goes behind an expandable section, never in the critical
path.

**The anti-pattern**: Long onboarding flows before first value. Required fields that serve our data
needs, not the user's. Asking a user to configure something they won't understand until they've
used the product.

**Applies to**: Onboarding, all forms, all configuration flows, all setup wizards.

---

### Principle 5: The Agent Is a Character, Not a Tool

**The rule**: The Wrench agent has a personality, a voice, a memory, and a perspective. It is not
a chatbot. It is not a search bar. It is a knowledgeable assistant that knows the user's workspace,
tracks what's happening, notices when things change, and proactively surfaces what matters.

**Agent voice principles**:
- Specific over generic. Name the thing. Name the number. "Your Healthcare leads dropped 12%" not
  "some leads may have lower scores."
- Confident but honest. The agent says what it knows and flags what it doesn't. It never bluffs.
- Proactive but not annoying. Surfaces things the user needs to know, not everything it could say.
- Short by default. If it can be said in one sentence, say it in one sentence.

**The test**:
- Read an agent response out loud. Does it sound like a person who cares, or a template?
- Does the agent know who this user is and reflect what's happening in their workspace right now?
- If the user asks a vague question, does the agent ask a smart clarifying question?

**The anti-pattern**: Generic responses that could apply to any user. Responses that use technical
language. Responses that are longer than they need to be. The agent apologizing for things that
aren't its fault.

**Applies to**: All agent messages, welcome screens, notifications, proactive alerts, support
interactions.

---

### Principle 6: Surprise and Delight Must Be Earned by Relevance

**The rule**: Micro-animations, personalized messages, and contextual celebrations are powerful
when they're relevant — and annoying when they're not. Delight is not decoration. Every animated
moment must be triggered by something the user actually did or achieved.

**Earned delight triggers (approved)**:
- First lead score generated → Workspace activation moment
- Lead score model reaches >80% coverage → Coverage milestone
- A contact's score improves significantly → Score improvement indicator
- Onboarding reaches 100% → Completion celebration (once only)
- Agent successfully resolves a user's question without human escalation → Subtle confirmation

**Unearned delight (never do)**:
- Animations that play on page load regardless of state
- Confetti for completing a form
- Celebration messages on login
- Agent expressing enthusiasm unprompted

**Applies to**: Animations, agent messages, notification copy, onboarding milestones, score
updates.

---

### Principle 7: Support Is a Product, Not an Afterthought

**The rule**: When a user has a problem, the speed and quality of our response is part of the
product experience. The support experience is designed with the same rigor as the product.

**Support lifecycle (every issue must pass through all states, visibly)**:
1. Received — User knows we got it. Timestamp visible.
2. Assigned — User knows who has it. First name + avatar.
3. In progress — User knows what's being done. Brief plain-language description.
4. Resolved — User knows what happened, whether it was our fault, and what we did about it.

**The anti-pattern**: Silent issue queues. "We'll get back to you" with no timeline. Resolution
with no explanation. Support that feels like shouting into a void.

**Applies to**: In-app support, email notifications, agent escalation flows, incident
communications.

---

### Principle 8: Consistency Is Credibility

**The rule**: Every interface element follows the same design language, the same tone, and the
same behavioral rules. Inconsistency signals a team that doesn't communicate. Users trust
consistent products and abandon inconsistent ones.

**Source of truth for visual tokens**: All colors, fonts, spacing, radius, shadows, and chart
styling are defined in the `wrench-dna` repo at `docs/brand/`. These files are synced from Figma
(`Wrench.ai_aligned-Metronic-library`, file key `XDABmqgxsih9ChiulLu1Wn`) and are authoritative.
**Never invent off-palette values.** If a value isn't defined in `docs/brand/design-tokens.css`,
it isn't a valid Wrench.ai value.

- Design tokens: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/design-tokens.css
- Artifact governance (the full rule set): https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/artifact-governance.md
- Colors: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/colors.md
- Typography: https://github.com/WrenchAI/wrench-dna/blob/main/docs/brand/typography.md

**The Wrench design token baseline** (mirrors the DNA — update here when the DNA updates):

- Primary accent: `#5E80A8` steel blue (`--color-primary`). Dominant interactive color —
  CTAs, links, active states, key metrics.
- Secondary accent: `#F1956F` warm orange (`--color-secondary`). Eyebrow labels, section
  markers, badges, warm call-outs. Punctuates; never dominates. Never a background fill on
  large surfaces.
- Dark surfaces (dark-first default): `--surface-base` `#1D2835` (page), `--surface-raised`
  `#243347` (cards), `--surface-overlay` `#2C3E55` (modals/dropdowns), `--surface-border`
  `#36404A` (dividers), `--surface-hover` `#4F5E6C`.
- Text on dark: `--text-primary` `#FFFFFF`, `--text-secondary` `#9AADC0`,
  `--text-tertiary` `#687B8E`, `--text-brand` `#5E80A8`, `--text-accent` `#F1956F`.
- Semantic: `--color-success` `#6DD48F`, `--color-danger` `#F8285A`,
  `--color-warning` `#F4AD8F`.
- Typography: **Poppins** for all display, heading, and body text
  (weights 300/400/500/600/700/800). **JetBrains Mono** for data IDs, API keys, score values,
  and code. No other fonts. Minimum heading weight `--font-semibold` (600). Minimum body size
  `--text-base` (13px).
- Border radius tokens: `--radius-sm` 4px (tags, badges), `--radius-md` 8px (inputs, small
  cards, buttons), `--radius-lg` 12px (cards, panels), `--radius-xl` 16px (feature cards),
  `--radius-2xl` 24px (large panels), `--radius-full` 9999px (pills, avatars).
- Transition tokens: `--transition-fast` 150ms ease, `--transition-base` 200ms ease,
  `--transition-slow` 300ms ease, `--transition-spring` 400ms cubic-bezier(0.34, 1.56, 0.64, 1).
- Dark-first default: every artifact sets `body { background: var(--surface-base);
  color: var(--text-primary); }`. Light mode requires an explicit request.
- Toast timing (product convention): 4 seconds for success/info, persistent until dismissed
  for error/warning.
- Icons (product convention): duotone icon set used consistently across product UI. Every
  status indicator must pair color with an icon — never color alone (see Principle 11).
  No emojis in product UI.

**Artifact-governance rules that also apply here** (the DNA's `artifact-governance.md` is binding
for anything styled — HTML/React, decks, PDFs, Notion pages, emails, social assets):

- Always embed the full `:root { }` block from `docs/brand/design-tokens.css` in every HTML/React
  deliverable (artifact-governance.md Rule 1).
- Dark-first default (Rule 2).
- No magic numbers — every CSS value references a design token (Rule 3).
- Correct heading hierarchy, no skipped levels; eyebrows for named sections (Rule 4).
- Steel blue is the single primary accent; warm orange punctuates (Rule 5).
- Every interactive element has defined hover + focus-visible states (Rule 7).
- Chart styling follows Rule 11 — `--chart-1` through `--chart-8`, custom dark tooltip,
  Poppins labels, JetBrains Mono values.

**The test**:
- Take any two screens side by side. Do they look like they came from the same product?
- Read any two error messages. Do they have the same voice, structure, and level of specificity?
- Grep the code for raw hex values or hardcoded px spacing. Zero tolerance — every value must
  reference a design token.
- If you're about to add a color, font, or size: confirm it exists in `docs/brand/design-tokens.css`
  first. If not, it's a brand defect.

**Applies to**: All UI components, all agent messages, all email notifications, all in-app
notifications, all Claude artifacts, all slide decks, all PDFs, all Notion pages bearing the
Wrench.ai brand.

---

### Principle 9: Every State Is a Designed State

**The rule**: There is no such thing as a default state. Loading is a state. Empty is a state.
Error is a state. Partial data is a state. Every state the platform can be in has been
intentionally designed — with appropriate copy, visual treatment, and guidance.

**Required states for every major feature**:
- Empty (no data yet)
- Loading (data is fetching)
- Partial (some data loaded, more coming)
- Error (something failed)
- Complete (data loaded successfully)
- Stale (data exists but hasn't been updated recently)

**The anti-pattern**: Blank screens. Raw data with no context. Empty tables with no explanation.

**Applies to**: All product screens, all data tables, all agent states, all onboarding steps.

---

### Principle 10: Build for the Relationship, Not the Session

**The rule**: Wrench is a platform users return to repeatedly over months and years. Every decision
should optimize for the long-term relationship, not the individual session. The experience should
feel meaningfully different on day 1 vs. day 100.

**Relationship-building features (prioritize)**:
- Persistent agent memory across sessions
- Historical lead score trends visible over time
- "Since your last visit" summaries
- Progressive workspace improvements the user can see
- Proactive insights that improve as data matures

**Applies to**: Agent design, product roadmap prioritization, data retention decisions,
notification strategy.

---

### Principle 11: Accessibility Is Not Optional

**The rule**: Every user deserves to use Wrench — including users with motor impairments, visual
disabilities, vestibular disorders, or cognitive differences. Accessibility is the baseline quality
standard. A platform that can't be used by a screen reader or a keyboard-only user is unfinished.

**Accessibility baseline**:
- Standard: WCAG 2.1 Level AA compliance on all product surfaces
- Motion: All CSS animations check `@media (prefers-reduced-motion: reduce)` and disable or
  substitute static alternatives
- Color contrast: All text and interactive elements meet minimum ratios (4.5:1 normal text, 3:1
  large text)
- Toast system: Every toast has both color AND an icon indicator — never color alone
- Keyboard: Every interactive element reachable by keyboard. Every modal has a keyboard-accessible
  close. Focus is never lost.
- Screen reader: All images have alt text. All form inputs have associated labels. Dynamic content
  updates announced via aria-live regions.

**The anti-pattern**: Animations without prefers-reduced-motion gates. Color-only status
indicators. Modals that trap keyboard focus. Buttons with no accessible label.

**Applies to**: All product UI, all animations, all toast/notification components, all form
elements, all agent interface elements, all onboarding flows.

---

## Part 2 — The Six Pillars Initiative

The Six Pillars are the active build framework derived from the UX Constitution. They translate
principles into shippable feature categories. Every UX task in the backlog maps to one of these.

**[P1-VIS] Pillar 1: System Status Visibility**
Users must always know what the system is doing. Covers: loading states, background job indicators,
real-time status updates, progress communication, and agent activity signals. Maps to P1 of the
Constitution.

**[P2-LANG] Pillar 2: Plain Language Design**
All user-facing copy must use the canonical language map. No technical jargon anywhere in the
product. Covers: UI copy audits, tooltip rewrites, error message rewrites, agent response language,
onboarding copy. Maps to P3 of the Constitution.

**[P3-ONBOARD] Pillar 3: Onboarding Simplification**
New users reach first value in under 15 minutes with 3 or fewer decisions. Covers: onboarding flow
redesign, progressive disclosure implementation, default-value strategies, first-session experience.
Maps to P4 of the Constitution.

**[P4-AGENT] Pillar 4: Agentic Chat**
The agent is a character with memory, voice, and workspace context. Covers: agent personality,
proactive insight surfacing, workspace-aware responses, clarifying question logic, Gulf of
Evaluation transparency (see Empirical Gaps). Maps to P5 of the Constitution.

**[P5-PERSONAL] Pillar 5: Personalization**
The experience evolves with the user. Covers: day-1 vs. day-100 experience differentiation,
"since your last visit" summaries, score trend history, persistent agent memory. Maps to P10 of
the Constitution.

**[P6-SUPPORT] Pillar 6: Live Support Escalation**
Support is a designed product experience. Covers: in-app support lifecycle, issue status
visibility, agent-to-human escalation, incident communication. Maps to P7 of the Constitution.

---

## Part 3 — The Seven Empirical Gaps

The Seven Empirical Gaps are specific, research-backed UX problems identified in the Wrench.ai
platform that the Six Pillars initiative is designed to close. Every gap has a backlog tag prefix.

**[GAP1-ACCESS] Gap 1: Accessibility / WCAG 2.1 AA**
The platform has not been fully audited against WCAG 2.1 AA. Zero critical violations is the
target. Includes prefers-reduced-motion compliance for all animations, color contrast verification
for brand palette, keyboard navigation audit, and screen reader compatibility.

**[GAP2-UNCERT] Gap 2: AI Uncertainty Communication**
Users don't know when AI is uncertain. The platform must communicate AI confidence levels in plain
language — not probabilities or percentages, but clear signals like "This score is based on limited
data" or "We're still learning this contact." Grounded in Microsoft Research Human-AI Interaction
Guidelines (2019).

**[GAP3-CONTROL] Gap 3: User Control and Reversibility**
Users cannot easily undo or override AI decisions. Every AI-driven action must have a clear
reversal path. Users must feel in control of the AI, not subject to it. Covers: score overrides,
ingredient edits, agent action confirmation before execution.

**[GAP4-NOTIF] Gap 4: Notification Fatigue**
Proactive notifications must be relevant and rare enough to stay meaningful. Covers: notification
frequency logic, relevance scoring for proactive agent messages, user-controlled notification
preferences, suppression after repeated dismissals.

**[GAP5-REPAIR] Gap 5: Conversational Repair**
When the agent misunderstands or gives a wrong answer, the user needs a clear, low-friction path
to correct it and teach the agent. Covers: thumbs down / correction flow in agent UI, agent
acknowledgment of correction, memory update on correction.

**[GAP6-EVAL] Gap 6: Gulf of Evaluation for Agent Commitments**
Users cannot tell what the agent has committed to doing. This is the highest-priority agentic UX
problem. When an agent says it will do something, the user must be able to see: (a) what it
committed to, (b) whether it has started, (c) whether it completed, (d) what the result was. This
is not a secondary concern — it is first-class.

**[GAP7-FEEDBACK] Gap 7: Feedback Loops into the Model**
User behavior and explicit feedback (ratings, corrections, dismissals) must feed back into the AI
to improve it over time. Users who give feedback must see evidence that it made a difference.
Covers: feedback capture UI, model improvement transparency, "based on your feedback" messaging.

---

## Part 4 — The Pre-Ship Checklist

Run this before every PR, every design review, every agent prompt update, every support flow
change. If any item is unchecked, the feature is not ready to ship.

- [ ] **P1 — Visibility**: Does the user always know what's happening?
- [ ] **P2 — Error ownership**: Is every error message honest, clear, and attributed?
- [ ] **P3 — Plain language**: Is every word intelligible to a non-technical user? No banned terms?
- [ ] **P4 — Time respect**: Is this the minimum viable friction to reach the next value moment?
- [ ] **P5 — Agent character**: Does the agent sound like a person who knows this user?
- [ ] **P6 — Earned delight**: Is every animation or celebration triggered by something meaningful?
- [ ] **P7 — Support as product**: Does the user always know the status of their issue?
- [ ] **P8 — Consistency**: Does this look and sound like Wrench? Every visual value references a
  token from `docs/brand/design-tokens.css`? Poppins + JetBrains Mono only? Steel blue dominant,
  warm orange punctuates? No emojis in product UI?
- [ ] **P8 — Artifact governance**: If this is styled output (HTML, React, deck, PDF, email,
  Notion), does it pass the pre-generation checklist in
  `docs/brand/artifact-governance.md`?
- [ ] **P9 — All states designed**: Have empty, loading, partial, error, complete, and stale states
  all been accounted for?
- [ ] **P10 — Relationship over session**: Does this decision serve the user's long-term
  experience?
- [ ] **P11 — Accessibility**: Does this work without motion, without color, without a mouse, and
  with a screen reader? WCAG 2.1 AA?
- [ ] **GAP6 — Gulf of Evaluation**: If an agent makes a commitment, can the user track it end to
  end?

---

## Part 5 — Notion References

The authoritative versions of these *behavioral* principles live in Notion. The *visual* tokens
they reference live in `wrench-dna/docs/brand/`. Always fetch from source before making
constitutional amendments.

- UX Constitution (source): `3345eef4866d818eb60af3b3945dd6b4`
  https://www.notion.so/3345eef4866d818eb60af3b3945dd6b4

- Agent Experience and World Class UX project (parent):
  `3335eef4866d8042a5cbd2c6eb6c7b36`
  https://www.notion.so/3335eef4866d8042a5cbd2c6eb6c7b36

- Task backlog DB collection ID: `6360cc78-9379-4535-bc80-c9262c3bde60`
- Projects & Priorities DB collection ID: `a00de035-0465-403e-a479-0ff6ffe798cb`

**Task naming convention**: All UX initiative tasks are prefixed with their Pillar or Gap code,
e.g. `[P1-VIS]`, `[GAP3-CONTROL]`, `[GAP6-EVAL]`.

---

## Empirical Foundations

These principles are grounded in:
- Nielsen Norman Group — 10 Usability Heuristics (#1 Visibility of System Status, #5 Error
  Prevention, #9 Help users recognize/recover from errors)
- Don Norman — Emotional Design. Three levels: visceral, behavioral, reflective. All three must
  be addressed.
- Calm Technology (Weiser, Brown) — Technology should inform without demanding attention.
- Steve Krug — Don't Make Me Think. Best UX requires the least cognitive effort.
- Human-AI Interaction Guidelines (Microsoft Research, 2019) — Make clear when AI is involved.
  Show confidence. Allow correction. Remember context.
- Agentic UX research (Stanford HAI, Anthropic) — Users trust agents that explain reasoning.
  Users abandon agents that are uncertain without communicating it. Users form emotional
  relationships with agents that maintain consistent character.

---

*Source: Wrench.ai UX Constitution, last updated 2026-04-23 | Owner: Dan Baird*
*Visual source of truth: `wrench-dna/docs/brand/` (design-tokens.css, artifact-governance.md)*
*Skill compiled: 2026-04-23 | Status: Living document — update when Constitution or brand DNA changes*
