---
name: bug-audit
description: >
  Audit a feature, release, or issue queue for bugs — functional AND UX.
  Trigger when someone says "bug audit", "audit this release", "what's broken",
  "QA this feature", "review before release", "check for issues", "is this ready
  to ship", or when Dan or a customer reports a problem and the team needs to
  triage scope. Also trigger proactively after any deploy where the 7-day soak
  window shows activity in #assistant-feedback. Covers four areas: functional
  bugs, UX bugs, test coverage gaps, and customer-surfaced classification.
  "It works" and "it feels right" are separate questions. Both must pass.
---

# Bug Audit

Structured bug audit covering functional correctness, UX quality, test coverage
gaps, and customer-surfaced classification. Run this before any release, after
any incident, and any time "it doesn't work" arrives without specifics.

## Why UX Bugs Are Bugs

A feature can be functionally correct and still be broken from the user's
perspective. Wrong loading state, missing error message, confusing empty state,
broken flow requiring a workaround — these are bugs. They cause churn. They
get reported to Dan. They end up in #assistant-feedback and count as
customer-surfaced in the quality KPIs.

This audit treats UX failures identically to functional failures: classified,
assigned, and tracked. "It works, it just looks wrong" is not a valid close state.

---

## Bug Severity

| Severity | Definition | Response |
|----------|-----------|----------|
| **P1** | Customer-facing data loss, auth failure, complete feature outage | Immediate — page on-call |
| **P2** | Feature degraded, workaround exists, affects multiple users | Same business day |
| **P3** | Minor functional error, edge case, easy workaround | Within current sprint |
| **P4** | UX polish issue, cosmetic, no functional impact | Backlog — prioritize by frequency |

## Bug Source Classification

**Customer-surfaced:** First reported by a customer or by Dan.
Dan's reports are captured as customer-reported in the system — there is no
separate category. Customer-surfaced bugs of any severity are tracked against
the team's quality KPIs. Recurring customer-surfaced bugs in a quarter cap
uplift eligibility regardless of other scores.

**Internally detected:** Caught by automated tests, CI, Datadog, or a team
member before any customer saw it. This is the quality system working correctly.
A bug caught internally is not a failure — it's a save.

---

## Audit Protocol

### Step 1: Define Scope

Before auditing, confirm:

```
Feature / release:  [name or Notion project URL]
Spec page:          [Notion URL — where ACs and Cypress test plan live]
Environment:        [prod / staging / both]
Audit trigger:      [pre-release / post-deploy soak / customer report / scheduled]
Audit date:         [date]
Audited by:         [name]
```

If there is no spec page, the feature was built without acceptance criteria.
Note this as a P2 process failure in the report — it means no baseline exists
for what "correct" looks like.

---

### Step 2: Functional Bug Check

For each acceptance criterion on the spec, verify in the target environment:

```
AC-[N]: [Description]
Status:   PASS / FAIL / NOT TESTED
Evidence: [What was observed — be specific]
Bug ID:   BUG-[YYYY-MM-DD]-[N]  (if FAIL)
Severity: P1 / P2 / P3
Source:   Customer-surfaced / Internal
```

**What counts as a functional bug:**
- Behavior that does not match a written acceptance criterion
- API returning wrong data, wrong status code, or no response
- A feature flag not behaving correctly (flag off ≠ old behavior)
- Data written to the wrong location or not written at all
- Auth or permission boundary not enforced as specified
- An error that does not surface to the user when it should
- A success response that fires when the operation failed
- A loading state that never resolves

---

### Step 3: UX Bug Check

Audit UX failures separately from functional failures. Work through each
category. Check against the spec's UX Considerations section if it exists.
If no UX Considerations section exists, that is itself a gap — note it.

**Loading and Async States**
- [ ] Every async operation shows a loading indicator
- [ ] Loading indicators disappear correctly when data arrives
- [ ] Requests slower than 2 seconds are visually communicated to the user
- [ ] Spinners and skeleton states are consistent with the rest of the UI
- [ ] Parallel requests do not cause the UI to flicker or flash inconsistently

**Empty States**
- [ ] Zero-data states show a meaningful empty state — not a blank screen
- [ ] The empty state explains why it's empty and what the user can do next
- [ ] Empty states are consistent with the Wrench.ai design system
- [ ] First-time-use empty states are distinguishable from "no results" empty states

**Error States**
- [ ] Failures surface a user-visible error message
- [ ] Error messages are in plain English — no raw error codes, stack traces,
      or HTTP status numbers visible to the user
- [ ] Error messages are actionable — they tell the user what to do, not just
      what went wrong
- [ ] Error messages disappear or update when the issue is resolved
- [ ] Validation errors appear inline next to the relevant field
- [ ] Network errors are handled gracefully — not a white screen or JS exception

**Flow Continuity**
- [ ] The user can complete the primary task end-to-end without hitting a dead end
- [ ] After a failed action, the UI returns to a valid state — not stuck
- [ ] Confirmation dialogs, toasts, and success notifications are firing correctly
- [ ] There are no flows where the user gets feedback that contradicts what happened
- [ ] Navigation after a completed action lands the user somewhere sensible

**Visual and Layout**
- [ ] No elements overflow their containers at standard viewport sizes
- [ ] Text truncation has a disclosure mechanism (tooltip, expand, etc.)
- [ ] Interactive elements (buttons, links) are visually distinct from static content
- [ ] Keyboard navigation works for all interactive elements
- [ ] No regressions in elements not directly modified by this feature

**Data Display**
- [ ] Numbers, percentages, and dates are formatted consistently across the page
- [ ] Data updates when the underlying data changes — no stale display
- [ ] Nulls and undefined values render as empty/default — not as the string
      "null" or "undefined" visible to the user
- [ ] Long strings (names, URLs, descriptions) do not break the layout
- [ ] Zero and negative numbers are handled and displayed correctly

**For each UX issue found:**

```
UX-[N]: [Description — what the user experiences]
Category:     Loading / Empty / Error / Flow / Visual / Data
Severity:     P2 (blocks task completion) / P3 (degrades experience) / P4 (cosmetic)
Reproduction: [Steps to see it]
Expected:     [What the user should see]
Actual:       [What the user sees]
Test needed:  Yes — TC description / No / Existing test needs update
```

---

### Step 4: Test Coverage Gap Analysis

Review the Cypress test plan from the spec. If no Cypress test plan exists,
that is a P2 process failure — note it and reconstruct coverage from the ACs.

```
AC-[N]: [Description]
Test exists:    Yes / No
Test file:      cypress/e2e/[file].cy.js  (if exists)
CI status:      Passing / Failing / Flaky / Quarantined / Not run
Coverage gap:   [What scenario is not tested]
```

**Mandatory coverage checklist:**
- [ ] E2E test exists for the primary happy path (AC-1)
- [ ] Test exists for the primary error scenario
- [ ] Tests exist for loading, empty, and error UX states
- [ ] No `cy.wait(ms)` in new tests — flag any found, these are bugs
- [ ] All new test selectors use `data-testid` — flag CSS class or text selectors
- [ ] Tests ran in CI and passed without new skips on the Monday post-deploy
- [ ] Test file has an `@owner` comment at the top

**A missing Cypress test for any P1 or P2 acceptance criterion is itself a
P2 bug. The feature ships without a safety net.**

---

### Step 5: Audit Report

```
## Bug Audit Report

Feature:        [name]
Notion spec:    [URL]
Audit date:     [date]
Audited by:     [name]
Environment:    [prod / staging / both]
Trigger:        [pre-release / post-deploy soak / customer report / scheduled]

## Summary

Total issues:   N
  P1:  N  (customer-surfaced: N  /  internal: N)
  P2:  N  (customer-surfaced: N  /  internal: N)
  P3:  N
  P4:  N
  UX bugs:          N
  Test gaps:        N

Ship decision:  HOLD / SHIP WITH KNOWN ISSUES / CLEAR TO SHIP

## Functional Bugs
[List using Step 2 format]

## UX Bugs
[List using Step 3 format]

## Test Coverage Gaps
[List using Step 4 format]

## Customer-Surfaced Issues
[Any P1/P2 first reported by a customer or Dan — these are KPI-tracked.
Dan's reports count as customer-surfaced in the quality scoring system.]

## Process Failures
[Missing spec, missing ACs, missing Cypress test plan, missing UX Considerations
section — note each one. These are systemic gaps, not one-time misses.]
```

---

## Ship Decision Rules

**HOLD — do not release:**
- Any open P1 bug
- Any P2 functional bug that maps to a written acceptance criterion
- Cypress suite failing, skipping, or using `.only()` on the feature's tests
- No Cypress tests exist for any P1/P2 acceptance criterion
- Feature flag behavior unverified

**SHIP WITH KNOWN ISSUES:**
- Open P3 bugs only, documented and sprint-assigned before shipping
- P4 UX issues that do not block task completion
- Known issues must be logged with an owner and sprint target — no open-ended backlog items

**CLEAR TO SHIP:**
- All P1/P2 bugs resolved
- All P1/P2 ACs have passing Cypress tests — no new skips in CI
- UX states (loading, empty, error) verified
- 7-day soak window clean (for post-deploy audits)

---

## Systemic Pattern Escalation

If the same UX bug category appears in three or more consecutive audits
(e.g., empty states consistently missing, loading states consistently wrong),
it is a systemic gap in the spec template — not a one-time miss.

Escalate to Dan to update the `feature-spec` skill's UX Considerations
requirements so the pattern is caught at spec time, not at audit time.

---

## Reference

- Feature spec: `skills/feature-spec/SKILL.md`
- Test health standards: `docs/standards/testing/test-health.md`
- UX principles: `skills/wrench-ux-constitution/SKILL.md`
- Quality KPIs: Willem and Jeong performance pages in Notion
