---
name: wrench-de-review
description: >
  Data engineering PR review checklist — idempotency, dedup logic, cardinality
  assertions, schema contracts, error handling, and automation completeness.
  Use on any PR touching elt-ad, elt, elt-api-requester, billing lambda,
  firmographics-micro, or infra-automation.
---

# Data Engineering PR Review

A structured review checklist for data engineering work at Wrench.ai. Covers both the technical correctness criteria derived from production incidents and the systems-thinking criteria that prevent manual processes from accumulating.

Run this skill on any PR in: `elt-ad`, `elt`, `elt-api-requester`, `common-billing-update-lambda`, `firmographics-micro`, `infra-automation`, or any repo touching the mapping/transformation layer.

---

## How to Use This Skill

1. Read the PR description and diff
2. Work through each section below in order — every ❌ is a blocking issue
3. Leave a structured review comment using the output template at the bottom
4. Mark sections N/A only when the section genuinely doesn't apply (e.g., no DB joins in a pure config change)

---

## Part 1: Scope & Rollbackability

**Q1.1 — Does this PR do exactly one thing?**

A PR should be safe to revert as a single unit. If reverting would also break something unrelated, it bundles too many concerns.

- ✅ Logic change, data migration, ops script, and new feature are each separate PRs
- ❌ Multiple unrelated concerns in one diff

**Q1.2 — Is there a clear rollback story in the description?**

"Revert this PR" should be a safe operation. If it isn't, the description must say what additional steps are required.

**Q1.3 — Are any manual steps documented?**

If this PR introduces a manual step (console action, find-and-replace, SSH command), flag it immediately. Manual steps in deploy processes are automation debt.

- ✅ Manual step is explicitly documented AND a follow-up issue is opened to automate it
- ❌ Manual step is present with no automation ticket

---

## Part 2: Data Quality — The 8 Principles

These principles exist because of real production incidents. Each one that's violated is a defect.

**Q2.1 — Cardinality assertion after every join**

Any code that performs a JOIN must assert the output row count is within expected bounds. Cartesian products don't throw errors — they silently explode costs.

```python
# Required pattern after any join
assert len(result_df) <= expected_max_rows, f"Join fanout: got {len(result_df)}, expected ≤ {expected_max_rows}"
```

- ✅ Every join has an explicit row count assertion
- ❌ JOIN with no cardinality check

**Q2.2 — Success is proven, not assumed**

A pipeline step is successful only when it explicitly asserts success. Check the status of the overall pipeline — it should be the logical AND of all step results.

- ✅ Each step explicitly checks return codes / job status and halts on failure
- ❌ "If no exception, assume success" pattern

**Q2.3 — Filenames are never parsed as structured data**

Any logic that extracts meaning from a filename must normalize the string before matching and must have a fallback if the pattern doesn't match.

**Q2.4 — Null is never written to cache**

Any cache write operation must reject null values at the write boundary. If the upstream failed, the cache must retain its previous value — not be overwritten with null.

**Q2.5 — Shared-state pipelines serialize per scope**

If this pipeline writes to records that another pipeline may also write, there must be explicit locking or queueing per scope (client_id, workspace_id, etc.). Concurrent writes to the same records are a data corruption risk.

**Q2.6 — Queue starvation protection**

If this adds to a job queue, confirm there is a round-robin or max-consecutive-picks mechanism. A high-volume client must not starve lower-priority work indefinitely.

**Q2.7 — dbt source freshness is a hard gate**

Source freshness checks must block the pipeline on failure — they must not log and continue. The `elt-api-requester` returns HTTP 500 on freshness failure so Step Functions can catch it. Do not weaken this gate.

**Q2.8 — Imputation strategy is documented**

If this PR changes how missing values are handled (mean/median/zero/drop), the strategy must be documented in the mapping file — not left as a silent model default.

---

## Part 3: Architecture Integrity

**Q3.1 — Mapping layer vs. transformation layer separation**

The cardinal rule: mapping rules reference source-specific fields; transformation rules never do.

- ✅ Platform-specific logic is in a mapping file; transformation logic has no platform names
- ❌ Transformation SQL or Python references `facebook_ads.*`, `google_ads.*`, or any platform-specific field name

**Q3.2 — Incremental loading**

Does this code do a full table rescan? If yes, is there an explicit justification in the PR description?

- ✅ Incremental pattern with watermark or `updated_at` filter
- ❌ Full refresh with no justification

**Q3.3 — Schema stability at the model-input-builder boundary**

If this change modifies the canonical schema (column names, types, nullability), was the `model-input-builder` maintainer notified? Schema contract changes on either side of this boundary require coordination.

**Q3.4 — Adding a new ad platform**

If this PR adds a new platform, confirm all three are present:
1. New mapping file (source schema → canonical schema)
2. Connector config
3. Validation query proving existing transformations work on the new data

---

## Part 4: Error Handling

**Q4.1 — No exception strings in API/Lambda responses**

`str(e)` in a response body exposes stack traces and schema details to callers. Required pattern:

```python
# Required
logger.exception("Context: what was happening")
return {"statusCode": 500, "body": json.dumps({"error": "Internal server error"})}

# Never
return {"statusCode": 500, "body": str(e)}
```

- ✅ All except blocks log internally and return generic external messages
- ❌ Any `str(e)` in a response body

**Q4.2 — Resource cleanup via try/finally**

Any resource opened (DB connection, file handle, lock) must be closed in a `finally` block or context manager. An early `return` before cleanup leaks connections silently under load.

**Q4.3 — Failure behavior is proven, not claimed**

If the PR description says "handles errors gracefully" or "degrades gracefully," there must be evidence: a test, a recorded repro, or an explicit description of what the caller receives on failure.

- ✅ Test exists that triggers the error condition and asserts the caller's response
- ❌ "Graceful" claimed in description with no evidence

---

## Part 5: Time-Based Logic

**Q5.1 — Clock documentation**

Any datetime comparison must document: what the timestamp represents, whether it's timezone-aware (always `datetime.now(timezone.utc)`, never `datetime.now()`), and what happens when the deadline is exceeded.

```python
# Required
# Measures time since SFN execution started (not queue wait time).
# If > QUEUE_MAX_WAIT_MINUTES, bypass to prevent starvation.
elapsed = datetime.now(timezone.utc) - execution_start
```

- ✅ All datetime logic is documented and uses timezone-aware timestamps
- ❌ `datetime.now()` without timezone, or datetime logic with no comment

---

## Part 6: Tests

**Q6.1 — New logic paths are covered**

Every new function, branch, or error path has at least one test that exercises it. This is not satisfied by confirming existing tests still pass.

**Q6.2 — Idempotency tested for state-changing operations**

Any endpoint or pipeline step that creates, updates, or deletes records must be tested for idempotency: calling twice with the same input produces the same result with no duplicates.

**Q6.3 — No MagicMock for data-bearing objects**

Tests that simulate DB rows or API responses must use concrete fake classes with explicit data, not `MagicMock`. `MagicMock.__getitem__` binding fails silently for special methods, producing tests that pass while returning wrong data.

**Q6.4 — Test results in PR description**

The PR description must include actual pytest output with pass/fail counts. "Tests pass" without the output is not acceptable.

---

## Part 7: Systems Thinking (Automation Completeness)

This section is the one most likely to have been missed. It catches the pattern of doing manually what should be automated.

**Q7.1 — Are there any manual steps in this PR's deploy or operation?**

Manual steps = automation debt. Check for:
- Console actions documented in READMEs
- Find-and-replace instructions for environment promotion
- "Run this command locally" steps that aren't in CI

For each manual step found:
- ✅ A follow-up GitHub Issue is opened and linked in the PR
- ❌ Manual step exists with no automation ticket

**Q7.2 — Does this introduce any new hardcoded values that vary by environment?**

Environment-specific values (account IDs, connector names, job IDs) must be parameterized — not hardcoded in code or manually substituted in JSON.

**Q7.3 — Is any deprecated or dead code being added?**

"Not maintained" tasks, disabled cron jobs, and commented-out code are not acceptable as shipped state. Either the code runs or it's removed.

- ✅ No new dead or disabled code
- ❌ New commented-out blocks, disabled schedules, or "not maintained" documentation sections

**Q7.4 — Does this create a process that will require human intervention on each execution?**

If a scheduled pipeline, Lambda, or Step Function requires a human action to complete each cycle, that's a design defect. The system should self-complete or self-alert on failure.

---

## Review Output Template

```
## Data Engineering Review

### Scope
[Pass / Fail] — [one-sentence note]

### Data Quality (8 Principles)
[Pass / N/A / Fail] per principle, with specific line numbers for any fail

### Architecture
[Pass / N/A / Fail] per check

### Error Handling
[Pass / N/A / Fail] per check

### Time-Based Logic
[Pass / N/A] — [note if applicable]

### Tests
[Pass / Fail] — coverage note, MagicMock check, idempotency check

### Systems Thinking
[Pass / Fail] — manual steps found / automation gaps

### Verdict
✅ Approved | 🔄 Changes Requested | ❌ Blocking Issues

Blocking issues (must fix before merge):
- [list]

Non-blocking suggestions:
- [list]
```

---

## Trigger phrases

Use this skill when reviewing any PR from the `wrench-de-review` domain. Invoke with:

> "Use the wrench-de-review skill on this PR"
> "Run the data engineering review checklist"
> "Review this pipeline change"
