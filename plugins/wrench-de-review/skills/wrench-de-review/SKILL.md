---
name: wrench-de-review
description: >
  Review a data-engineering pull request — pipeline, transformation, schema, or
  warehouse change — for correctness, migration safety, idempotency, and cost.
  Use when reviewing a dbt model, SQL transformation, ingestion job, schema
  migration, or backfill, or when asked whether a data change is safe to ship.
  Applies data-specific review criteria that a general code review misses.
---

# Data Engineering Review

A data PR fails differently from an application PR. Application bugs break
requests; data bugs quietly produce wrong numbers that people then act on for
weeks. Review accordingly: correctness and reversibility first, style last.

Review the diff, then the data. A model that looks right and returns the wrong
row count is still wrong.

---

## Phase 1: Establish what the change touches

```text
Change type:     <ingestion | transformation | schema | backfill | orchestration>
Objects touched: <tables, models, views, topics>
Downstream:      <who reads these — dashboards, models, exports, APIs>
Grain:           <one row = what?>
Volume:          <rows and bytes affected>
Full refresh?:   <incremental | full | rebuild>
```

The downstream list is the part most reviews skip and most incidents come from.
Trace it before approving. A column rename is trivial in isolation and breaking
three dashboards away.

---

## Phase 2: Correctness

- **Grain is stated and preserved.** If the PR changes what one row means, that
  is a breaking change to every consumer, whether or not the schema changed.
- **Joins cannot fan out.** Every join either provably preserves grain or has a
  deduplication step with a stated tiebreak rule. "It's one-to-one in practice"
  is not a guarantee.
- **Null and empty semantics** are handled explicitly, not left to coalesce
  incidentally.
- **Time zones and boundaries.** Date filters state their timezone. Windows are
  half-open and consistent. Late-arriving data has a defined policy.
- **Type changes are widening, not narrowing.** Narrowing a type silently
  truncates.
- **Row counts before and after** are in the PR, with an explanation for any
  delta. An unexplained count change is the review finding.

---

## Phase 3: Migration and reversibility

- **Expand, migrate, contract** — never a single destructive step. Add the new
  column, dual-write, migrate readers, then drop. A PR that adds and drops in
  one commit is a rollback with no path back.
- **Dropped columns and tables** require a named owner approval and a stated
  retention window. Dropping is not reversible by rerunning the pipeline.
- **The rollback is written down** and it is not "revert the PR." Reverting code
  does not un-drop a column or un-write bad rows.
- **Backfills are restartable and bounded.** A backfill that cannot resume from
  a checkpoint will be killed halfway and leave partial state. State the batch
  size, the checkpoint, and the expected duration.
- **Backfill and forward-write do not race.** Say which wins if both touch the
  same partition.

---

## Phase 4: Idempotency and recovery

- Re-running the job produces the same result. If it appends, it deduplicates.
- Partial failure leaves a recoverable state, not a half-written table. Prefer
  atomic swap over in-place mutation.
- Late or replayed source data does not double-count.
- The job's watermark or cursor advances only after a successful write, never
  before.

---

## Phase 5: Cost and performance

- Partition and cluster keys match the actual query pattern, not the natural key.
- No unbounded scan introduced — check for a filter on the partition column.
- Incremental models stay incremental; a change that forces a full refresh on
  every run is a cost regression even when it is correct.
- Estimate the run cost delta. "Slightly more" is not an estimate.

---

## Phase 6: Tests and evidence

Require, in the PR:

- A test that fails without the change and passes with it.
- Grain and uniqueness assertions on the primary key.
- Not-null assertions on the columns downstream consumers rely on.
- Referential checks against the parent table where a relationship is assumed.
- Row counts and a spot-checked sample, in the PR body.

A green pipeline is not evidence of correct data. It is evidence the job ran.

---

## Phase 7: Verdict

```text
PR:              <repo>#<number>
Change type:     <type>
Grain:           <preserved | changed — and what that breaks>
Downstream:      <consumers traced, and impact on each>
Migration:       <expand/contract | single-step — flagged>
Reversible:      <yes, via <path> | no — and what that means>
Idempotent:      <yes | no — where it breaks>
Cost delta:      <estimate>
Tests:           <present and sufficient | gaps listed>
Verdict:         <approve | approve with follow-up | request changes | block>
Blocking items:  <numbered, each with what would resolve it>
```

Use `block` — not `request changes` — when the PR risks data loss, a
non-reversible drop, or silent wrong numbers reaching a consumer. Blocking says
this needs a decision from an owner, not a tweak from the author.

---

## Rules

- Correctness and reversibility outrank style. Do not lead a review with naming.
- Never approve a destructive migration without a named owner and a written
  rollback that does not assume rerunning the pipeline fixes it.
- Never accept "it's one-to-one in practice" for a join. Ask for the assertion.
- Never approve on a green pipeline alone — require counts and a sample.
- Trace downstream consumers before approving any schema or grain change.
- If you cannot determine the grain from the PR, that is the first finding.
