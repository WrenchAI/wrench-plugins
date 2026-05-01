---
name: feature-spec
description: >
  Write a complete feature spec for any new feature or significant change.
  Trigger whenever someone says "write a spec", "spec this out", "create a
  feature spec", "write a PRD", "spec a feature", or drops a feature idea and
  asks for structure. Also trigger proactively when a Notion project is created
  in "Ready for Engineering" without a behavioral spec attached. A spec is NOT
  complete without acceptance criteria, a task breakdown, and a Cypress test
  plan. Missing any of these three sections = incomplete spec. Do not allow
  development to start without all three present.
---

# Feature Spec

Complete spec writing for any new Wrench.ai feature or significant change. A spec
written with this skill is the source of truth for what gets built, how it gets
tested, and what "done" means.

## Why This Exists

Most bugs that reach customers originate from ambiguous or missing specs — not
from bad code. When "done" isn't defined before development starts, every
developer builds their own mental model, tests validate the wrong thing, and
bugs slip through because no one agreed on what correct looked like.

This skill closes that gap. Every spec produced here is also a test
specification. Cypress tests are derived directly from the acceptance criteria.
The spec and the tests are the same document at two levels of abstraction.

---

## The Three Required Sections

A spec is incomplete — and development must not start — if any of these are missing:

1. **Acceptance Criteria** (Given/When/Then, written before any code)
2. **Task Breakdown** (dev tasks, each mapping to specific ACs)
3. **Cypress Test Plan** (one test case per AC, derived from AC language)

If any of the three are absent when a project is moved to "Ready for Engineering",
move it back to "In Progress" and flag the gap.

---

## Spec Template

### 1. Problem Statement

Write from the user's perspective, not the implementation's.
If there is no clear user pain, the feature should not be built.

```
User: [who is affected]
Current behavior: [what happens today]
Problem: [why that's bad]
Business impact: [what it costs in retention, trust, or revenue]
```

---

### 2. Proposed Solution

One paragraph. High level — no implementation details yet. If you cannot
describe the solution in one paragraph, the scope is too large. Split the spec.

---

### 3. Acceptance Criteria

**Format: Given / When / Then. One scenario per criterion.**

This is the contract between product and engineering. Written by the spec author
(Dan or product) before any engineering conversation begins. Developers can
challenge ACs — they cannot skip them or write their own retroactively.

Every AC must be:
- **Observable** — verifiable by looking at the UI or API response
- **Unambiguous** — one interpretation, no "usually" or "typically"
- **Testable** — a Cypress test can be written directly from this language

```
AC-1: [Happy path — primary user flow]
Given: [the user's starting state]
When:  [the action they take]
Then:  [the observable outcome]

AC-2: [Error / failure scenario]
Given: ...
When:  ...
Then:  ...

AC-3: [Permission / access boundary]
Given: ...
When:  ...
Then:  ...

AC-4: [Empty / zero-state scenario]
Given: ...
When:  ...
Then:  ...
```

**Minimum required criteria:**
- At least one happy path scenario
- At least one error/failure scenario
- At least one permission or access boundary (if applicable)
- At least one empty/zero-state scenario (if the feature displays data)

**What makes an AC invalid:**
- "Works correctly" — not observable
- "Looks good" — not testable
- "Should be fast" — not specific (use: "responds within 2 seconds")
- Implementation details ("calls /api/v2/scores") — ACs describe behavior, not code
- Passive voice that hides who or what is acting

---

### 4. Out of Scope

List what is explicitly NOT included. If something is not in the ACs and not
listed here, it is ambiguous. Ambiguity ships bugs.

---

### 5. Task Breakdown

Break the work into discrete engineering tasks. Each task should be completable
in one PR and map to specific ACs. No task should exist without an AC.

```
Task 1: [Name]
Owner:             [Willem / Jeong / Muhammad / Frontend Developer]
AC coverage:       [AC-1, AC-2]
Depends on:        [None / Task N]
Scope estimate:    [Small / Medium / Large]
Feature flag:      [Yes — flag name: wrench_[feature_name]_enabled / No]

Task 2: ...
```

**Task rules:**
- Every task maps to at least one AC. No orphan tasks.
- Self-initiated refactors unrelated to any AC in this spec do not belong here.
- Feature flags are required for all user-facing changes. Format:
  `wrench_[feature_name]_enabled`. Flag off = old behavior. Flag on = new.
  The flag must be validated before the feature is considered deployed.
- Tasks from the committed backlog are prioritized over self-initiated work.
  No self-initiated task is started while an AC-mapped task is in "To Do".

---

### 6. Cypress Test Plan

**This section is mandatory. A spec without a Cypress test plan is not a spec.**

Derive test cases directly from the AC language. One test case per AC minimum.
Critical path ACs require Critical priority tests.

```
Test file: cypress/e2e/[feature-name]/[feature-name].cy.js
@owner: [engineer responsible for writing and maintaining these tests]

TC-1 (AC-1): [Test name — matches the happy path AC description]
  Priority:       Critical
  Preconditions:  [logged in as X, data state Y, flag on]
  Steps:
    1. cy.visit('[route]')
    2. cy.intercept('POST', '/api/[endpoint]').as('[alias]')
    3. cy.get('[data-testid="..."]').click()
    4. cy.wait('@[alias]')
  Assertion:      cy.get('[data-testid="..."]').should('[state]')

TC-2 (AC-2): [Error scenario name]
  Priority:       Standard
  ...

TC-3 (UX — loading state): [Loading behavior during async operation]
  Priority:       Standard
  What this protects: [user sees a loading indicator, not a blank screen]
  ...

TC-4 (UX — empty state): [Empty state for zero-data scenario]
  Priority:       Standard
  ...

TC-5 (UX — error state): [Error message display on failure]
  Priority:       Standard
  ...
```

**Cypress test requirements — non-negotiable:**
- Every AC-1 (happy path) must have a Critical priority test
- Every error/failure AC must have at least a Standard test
- Every UX state (loading, empty, error) requires at least one test
- No `cy.wait(ms)` — use `cy.intercept()` and wait for named API aliases
- All selectors use `data-testid` attributes — no CSS class or text selectors
- Tests must not require manual data setup — use factories or seeded fixtures
- Tests must clean up state in `afterEach` to prevent order-dependency
- Tests must pass in CI without manual intervention

**The verification gate:**
When a developer self-reports "Cypress tests complete" for this feature,
the Monday CI run is the verification. The claim is not accepted until CI
passes without new skips, retries, or `.only()` modifiers. Repeat failures
to hold under Monday verification = Tier 2 miss for that week.

---

### 7. UX Considerations

Address each of these explicitly. Omit only if genuinely not applicable,
and state why.

**Loading states:** What does the user see while data is fetching? Spinner,
skeleton, or disabled state? Consistent with the rest of the UI?

**Empty states:** What does the user see when there is no data? Does it
explain why it's empty and what they can do next?

**Error states:** What does the user see when something fails? Is the
error message in plain English, actionable, and not a raw error code?

**Permission states:** What does a user without access see? Blank screen,
redirect, or "you don't have access" message?

**Flow completion:** Can the user complete the primary task end-to-end
without hitting a dead end or needing to understand system internals?

**Edge data:** How does the UI handle very long strings, null values,
zero counts, and numbers with many decimal places?

*Reference: wrench-ux-constitution. Apply the 11 principles and run the
pre-ship checklist before marking the feature done. A UX failure that would
fail the pre-ship checklist is a defect — it ships as a bug.*

---

### 8. Downstream Handoff

Required if this spec introduces a new API endpoint, changes a data contract,
or affects any downstream consumer (frontend, data pipeline, other services).
This section is what Willem writes on the Notion project page before merge.
No handoff written = Tier 1 miss on that deploy.

```
Consumer:           [who is affected — frontend, Jeong's pipeline, Muhammad]
Contract change:    [what changed — new field, removed field, renamed endpoint]
Migration required: [Yes / No — if Yes, describe]
Backward compat:    [Yes / No]
Handoff deadline:   [by merge]
```

---

### 9. Definition of Done

A feature is not done until ALL of the following are true. No exceptions
without Dan approval logged on this page.

- [ ] All acceptance criteria verified in prod (not staging)
- [ ] All Cypress tests passing in CI — no new skips, retries, or `.only()`
- [ ] Handoff written on this Notion page for every downstream consumer
- [ ] Feature flag validated: flag off = old behavior, flag on = new behavior
- [ ] 7-day post-deploy soak: zero rollbacks, zero linked bugs, zero reports
      in #assistant-feedback traceable to this feature
- [ ] UX states (loading, empty, error) verified by Dan or designated reviewer
- [ ] Data dictionary updated if any schema changed (Jeong, within 5 biz days)
- [ ] Architecture decision recorded if architectural change was made (Willem)

---

## Enforcement Rules

Before a spec is marked "Ready for Engineering" in Notion, verify all eight:

1. Problem Statement is written from the user's perspective (not the implementation's)
2. At least 3 ACs present: happy path + error + boundary minimum
3. Every AC is Given/When/Then format, observable, and testable
4. Out of Scope section is populated
5. Task breakdown maps every task to at least one AC
6. Cypress Test Plan exists with at least one test case per AC
7. UX Considerations addresses loading, empty, and error states explicitly
8. Definition of Done checklist is present and all items unchecked

If any item is missing: the spec is incomplete. Add a Notion comment
identifying the gap. Do not move to "Ready for Engineering" until resolved.
