---
date: 2026-06-08
title: Resilient data pipelines for Stillpoint Labs
audience: Noah, future contractors and AI agents building Troth, Ingle, Reveal
status: authoritative shared reference (v1)
---

# Resilient data pipelines for Stillpoint Labs

This is the shared data-pipeline standard for every Stillpoint product. It governs the path from *raw source data
arriving* (a CSV upload, a Plaid sync, an image batch) to *that data safely landed and enriched*. Troth's
transaction-safe import pipeline shipped this pattern (2026-06-05) and is its proof. If a future contractor or agent
asks "why does the import write to a staging table first instead of straight to `transactions`," or "why is there a
separate worker instead of just doing the work in the upload handler," this is the document that answers.

The problem it exists to prevent is concrete, and it is two distinct failures wearing one coat. **First, ephemeral edge
runtimes kill unawaited work.** Troth's upload handler ran on Cloudflare's edge; three follow-up steps
(`applyRulesToUncategorized`, `computeProfile`, `inferAllocations`) were fired without `await`, and the moment the HTTP
response returned, the runtime tore down the unfinished promises. They *silently might never run*. **Second, non-atomic
multi-write imports leave partial data and a lying row count on failure.** The old `commitCsvImport` wrote
account → import → transaction batches as independent calls with no transaction boundary. If batch 3 of 5 failed,
batches 1–2 stayed committed, `imports.row_count` claimed the full file landed, and there was no rollback and no
resume — the user's only recourse was to re-upload and pray it didn't double. A household-finance product cannot
show a wrong balance and keep the user's trust; a photo product cannot lose half a batch silently. This standard
makes both failures structurally impossible rather than something you discover in production.

The audience is Noah, a future contractor Noah hires, and the AI agents that write most of the code. All three should
leave this document able to build a pipeline that never loses raw data, never half-writes its destination, and resumes
itself after a crash — without re-deriving the architecture. It spans the whole stack: Troth and Ingle are TypeScript
on Supabase, Reveal is Python, and the pattern applies to all three with per-stack adaptations noted below.

A note on what kind of standard this is, stated plainly up front. This is **not a literature survey.** It is an
*extracted, battle-tested pattern* — every rule below is something Troth's shipped pipeline does, with a passing
integration test behind it. Where this document says "the worker reclaims a stale lease," there is a test
(`tests/integration/queries/import-pipeline.test.ts`) that crashes a run and asserts it. So the confidence here is of a
different kind than the sister `DATA_CONSISTENCY.md`: that document is literature-derived best practice that Troth has
*begun* to validate; this one is a working implementation generalized *after* it shipped and proved out. Treat it as a
recipe you have already watched come out of the oven once.

## Quick reference — the rules in one screen

- **Edge entrypoints only validate, stage, and enqueue.** An upload/sync handler does exactly three things: validate
  the input, write raw rows to a staging table, and create a pipeline-run row. It performs **zero** direct writes to
  the destination table and **zero** fire-and-forget work. It awaits everything and returns. (`stageImport` in
  `troth/src/lib/troth/import-pipeline.ts`.)
- **One atomic RPC is the sole writer of the destination table.** All destination writes go through a single
  transactional function — all-or-nothing, idempotent on re-run via `ON CONFLICT`, so replaying it produces zero
  duplicates. Nothing else writes that table, ever. (`troth.promote_import` — *"the ONLY path that writes
  troth.transactions."*)
- **Raw rows persist in a staging table until the run reaches `complete`.** The source data lands verbatim and is never
  deleted while the run is in flight, so any downstream failure replays from staging with no re-upload and no re-sync.
  (`troth.import_staging_rows`, `raw JSONB` kept verbatim.)
- **A durable worker drives an explicit stage machine, resuming from the last healthy stage.** A long-lived process
  (not the edge handler) advances a `stage` column one transition at a time. A crash mid-run resumes at the stage it
  was on. (`troth/workers/categorizer/index.js`.)
- **Claim work with `FOR UPDATE SKIP LOCKED` + a lease.** Two workers can never grab the same run; a stale lease
  (its deadline passed) is reclaimable, which is exactly what makes a worker crash recoverable.
  (`troth.claim_pipeline_run`.)
- **A crash is NOT a failure.** Only real stage errors count toward bounded retry and eventual quarantine. A worker
  restart or a reclaimed lease bumps an observability counter, never the failure counter. Two innocent restarts must
  never quarantine a financial import. (`claims` vs `failures` in `troth.import_pipeline_runs`.)
- **Retain raw rows; GC only completed runs.** Garbage-collect staging only for runs that reached `complete`, and only
  after a retention window. Quarantined and in-flight runs keep their staging forever — it is the recovery buffer.
  (`troth.gc_staging_rows`.)

The rest of this document is the receipts for those rules.

## The pattern — six components, each load-bearing

The pipeline is six components that compose into one guarantee: raw data is never lost, the destination is never
half-written, and the run drives itself to completion or to a recoverable quarantine. Each component below is named
with the Troth reference file that implements it. None of them is optional — drop one and a specific failure mode
re-opens.

### 1. Staging-first ingestion

Every parsed source row lands in a staging table *first*, verbatim, before anything touches the destination. In Troth
that table is `troth.import_staging_rows` (`migrations/20260605010001_import_staging_rows.sql`): one row per source
line, with the original CSV object or Plaid transaction kept untouched in a `raw JSONB` column, plus a normalized
projection (`external_id`, `occurred_on`, `payee`, `amount_cents`) filled at stage-time so the promote step is a pure
set-based `INSERT … SELECT` with no parsing in plpgsql.

- **Why it works:** the staging table is the replay buffer and the audit trail in one. Because the raw row is preserved,
  every downstream stage can be re-run against staging — there is nothing to re-upload and no Plaid cursor to rewind.
  The standing rule is *never discard source data*: even a row that fails to normalize is kept with `status = 'error'`
  rather than silently dropped, which preserves the no-data-loss guarantee for malformed input too.
- **The normalized projection is deliberate.** Filling `external_id`/`occurred_on`/`payee`/`amount_cents` at stage-time
  (the edge action already has them from `normalizeRows`/`plaidToInsert`) keeps the atomic boundary a dumb, fast,
  set-based insert. Date and amount parsing — the error-prone part — happens once, in TypeScript, before the row
  is ever eligible to promote.
- **RLS posture:** household members may `SELECT` (so the processing UI can surface "N rows couldn't be read"); only the
  service-role worker and the edge admin client may write. There is deliberately no write policy.

### 2. The atomic promote boundary

Exactly one function writes the destination table, and it does so in one transaction. In Troth that is
`troth.promote_import(p_import_id)` (`migrations/20260605010003_promote_import_rpc.sql`), and its header states the
contract bluntly: *"the ONLY path that writes troth.transactions."* The entire function body is one implicit
transaction — if any statement raises, Postgres rolls the whole call back and **zero** rows are written. There is no
batch-N partial commit possible, because there are no separate batches at the DB boundary; it is one `INSERT … SELECT`
over the eligible staging rows.

- **Idempotent by construction.** CSV/manual rows insert `ON CONFLICT (household_id, external_id) DO NOTHING`; a second
  run promotes only rows still `status = 'normalized'`, of which there are none after the first run marks them
  `promoted`. Re-running therefore promotes 0 and creates no duplicates.
- **Upsert vs. drop is a per-source decision.** Plaid `modified` transactions arrive as `op = 'upsert'` but already
  exist by `external_id`; `DO NOTHING` would silently drop the corrected amount, so for Plaid the function does
  `DO UPDATE` on the mutable columns (`amount_cents`, `payee`, `occurred_on`, `raw`) and *deliberately does not touch
  `category_id`* — a user-confirmed category must survive a re-sync.
- **Soft-delete for source removals, never hard-delete.** Plaid `removed` rows (`op = 'delete'`) set
  `deleted_at = now()` on the matching transaction. Financial rows are never physically deleted.
- **Isolation is enforced, not assumed.** The function is `SECURITY DEFINER` (RLS-exempt), so it scopes every statement
  to a `v_household` derived from the import row. `promote_import` on household A's import can never touch household B's
  rows — a dedicated cross-household isolation test proves exactly this. `EXECUTE` is granted to `service_role` only.

### 3. Idempotent stage machine with resume

A run moves through an explicit, small `stage` machine, and **every transition is idempotent** so re-running a stage is
safe. Troth's `troth.import_pipeline_runs` (`migrations/20260605010002_import_pipeline_runs.sql`) holds one row per
import attempt with a `stage` column constrained to five values. The shipped machine is intentionally collapsed (a
Collective-review decision) to four live stages plus quarantine:

```
staged    → promote_import (atomic)                      → promoted
promoted  → apply_rules + LLM categorize (gating)        → enriched
enriched  → inferAllocations + computeProfile (best-effort) → complete
<any>     → quarantined   (only after failures >= max_failures)
```

- **`imports` stays immutable; the run holds the mutable lifecycle.** `troth.imports` remains the provenance record
  ("a file named X with N rows arrived"); the *processing state* of that file lives entirely on the pipeline run. This
  separation is what lets the run be retried and quarantined without rewriting history.
- **Resume from the last healthy stage.** Because the worker advances `stage` only after a transition succeeds (via
  `advance_pipeline_run`), a crash leaves the run on the stage it was attempting. The next claim picks it up *there* —
  not from the top. A failed enrich never re-runs the already-committed promote.
- **Gating vs. best-effort stages.** `promoted → enriched` is *gating*: rule + LLM categorization must succeed to
  advance. `enriched → complete` is *best-effort*: `inferAllocations` and `computeProfile` are wrapped in try/catch
  and logged-but-swallowed, because a failed allocation inference must not strand a financially-complete import.
  Choosing which stages gate and which are best-effort is a per-pipeline design decision and should be explicit.

### 4. Durable orchestration off ephemeral runtimes

The downstream work runs in a long-lived process, *not* in the edge handler. Troth's
`workers/categorizer/index.js` is a Fly worker that loops: claim a run, `switch (run.stage)`, run the one idempotent
handler for that stage, advance. The edge action's entire job (`stageImport`) is reduced to validate + stage + enqueue,
all awaited; it returns `{ importId, runId, staged, errored }` and does no destination writes at all.

- **Why the split is non-negotiable.** Edge/serverless runtimes reclaim the execution context when the response
  returns — unawaited promises die. Any multi-step enrichment *must* live somewhere durable. The worker is that
  somewhere. This is the direct fix for the original "fire-and-forget silently never runs" bug.
- **The run is created last, on purpose.** `stageImport` writes `imports → staging rows → categorization_jobs →
  pipeline_run`, and the run is created *last and deliberately* — its existence is the "staging is complete, claim me"
  signal. A failure partway leaves at most an orphan import plus staging rows: inert, never promoted, never claimed.
- **Lease heartbeat for long stages.** A stage that runs longer than the lease (the LLM categorization pass) extends
  its own lease (`heartbeat()` updates `lease_until` every batch, scoped to `locked_by = WORKER_ID`) so a healthy
  worker mid-LLM isn't reclaimed out from under itself.
- **Self-draining loop + daily GC.** The worker drains all claimable work each tick, then once per day calls
  `gc_staging_rows` — no `pg_cron` dependency required.

### 5. Claim-with-lock + bounded backoff

Work is claimed atomically and exclusively, and failures back off on a bounded schedule. Troth's three
state-machine RPCs (`migrations/20260605010004_pipeline_run_rpcs.sql`) are the single source of truth for how a run
moves; the worker never `UPDATE`s the runs table directly.

- **`claim_pipeline_run` is race-free.** It does `SELECT … FOR UPDATE SKIP LOCKED LIMIT 1` over claimable runs
  (`stage NOT IN ('complete','quarantined') AND next_run_at <= now() AND (lease_until IS NULL OR lease_until < now())`),
  then stamps the lease. `SKIP LOCKED` means two workers polling at once can *never* claim the same run — the second
  gets the next row or nothing. A stale lease (`lease_until < now()`) is reclaimable, which is precisely what makes a
  crash mid-stage recoverable.
- **`advance_pipeline_run`** records a successful transition and releases the lease; setting `stage = 'complete'` stamps
  `completed_at`. Re-advancing to the same stage is harmless.
- **`fail_pipeline_run`** records a *real* stage error, backs off, and quarantines only at the threshold. The backoff is
  `30s · 2^min(failures, 5)` → 30s, 60s, 2m, 4m, 8m, 16m (capped). Quarantine happens only when
  `failures >= max_failures` (default 5) — terminal, but *resumable*, because staging is retained and
  `failed_stage` is recorded.

### 6. Raw-row retention

Staging is the no-data-loss guarantee, so it is deleted only when it is provably safe. `troth.gc_staging_rows`
(`migrations/20260605010006_gc_staging_rows.sql`) deletes staging rows **only** for runs that reached `complete`, whose
`completed_at` is older than the retention window (default 30 days). Quarantined and in-flight runs keep their staging
forever — it is the recovery buffer. And because the promoted source row also lives permanently on
`troth.transactions.raw`, GC'ing a completed run's staging loses no audit trail.

## Required artifact — the failure / rollback semantics table

Every pipeline built to this standard MUST ship this table as a design artifact, one row per stage (plus the
quarantine terminal state). It is the contract: for each stage, what makes re-running it safe, what happens when it
errors, and where it resumes. The table below is derived directly from Troth's worker handlers and the three
state-machine RPCs — it is the canonical worked example to copy.

| Stage / state | Idempotency guard | On-error behavior | Resume behavior |
| --- | --- | --- | --- |
| `staged` → `promoted` (`promote_import`) | One atomic `INSERT … SELECT`; CSV `ON CONFLICT DO NOTHING`, Plaid `DO UPDATE` on mutable cols only; only `status='normalized'` rows eligible, marked `promoted` after | RPC raises → whole transaction rolls back, **zero** rows written; worker catches, calls `fail_pipeline_run('staged')` → backoff, lease released | Next claim re-runs `promote_import` from staging; already-promoted rows are no longer `normalized` → promotes 0, no duplicates |
| `promoted` → `enriched` (`apply_rules` + LLM categorize) | `apply_rules` touches only `category_id IS NULL` rows (never overwrites a user category); LLM pass selects only `category_id IS NULL AND predicted_by IS NULL` | Throw → `fail_pipeline_run('promoted')` → backoff; `failures` increments; promoted transactions are already durable and untouched | Next claim re-runs enrich; already-categorized rows are filtered out, so re-run only finishes the remainder |
| `enriched` → `complete` (`inferAllocations` + `computeProfile`) | Allocation upsert keyed `(household, category, period_start, 'inferred')`; profile recompute overwrites wholesale | Best-effort: each sub-step is try/caught and logged, **not** fatal — a failure here never blocks `complete` and never increments `failures` | Stage still advances to `complete`; a swallowed sub-step is simply re-attempted's-worth-skipped, never a quarantine cause |
| `<any stage>` → `complete` (`advance_pipeline_run`) | Setting `complete` stamps `completed_at`; re-advancing is harmless | n/a (success transition) | Terminal-success; run no longer claimable; staging eligible for GC after retention |
| `<any stage>` → `quarantined` (`fail_pipeline_run` at threshold) | Reached only when `failures >= max_failures` (default 5); a crash/reclaim never gets here (it bumps `claims`, not `failures`) | Terminal-but-recoverable: `stage='quarantined'`, `failed_stage` + `last_error` recorded, staging retained forever | Not auto-claimed (excluded from the claimable predicate); manual resume by resetting `stage`/`failures` — raw rows are intact, so it replays cleanly |

Five rows: four stage transitions plus the quarantine terminal state.

## Guarantees — each tied to the mechanism and the test that proves it

This pattern delivers three guarantees. None is a claim of intent; each is tied to the mechanism that enforces it and
the integration test in `troth/tests/integration/queries/import-pipeline.test.ts` that demonstrates it.

- **No partial data, ever.** *Mechanism:* `promote_import` is the sole writer of `troth.transactions` and runs as one
  transaction, so any raise rolls the whole call back. *Proof:* the atomicity test stages one good row and one row with
  a `NULL occurred_on` (which violates `transactions.occurred_on NOT NULL`); the RPC raises and the test asserts
  `txnCount === 0` — *"rolled back entirely — not even the good row."*
- **No data loss.** *Mechanism:* raw rows live in `import_staging_rows` from upload until the run hits `complete`; every
  stage replays from staging; GC spares quarantined and in-flight runs. *Proof:* the idempotency test promotes 3
  rows, re-runs `promote_import`, and asserts the second run promotes 0 and the count stays 3 — i.e., staging-driven
  replay is safe and lossless. The quarantine test confirms a run reaches `quarantined` *with staging retained for
  resume*.
- **No double-processing.** *Mechanism:* `claim_pipeline_run` uses `FOR UPDATE SKIP LOCKED`; stale leases make crashes
  recoverable without re-running committed work. *Proof:* the concurrency test fires two simultaneous claims at one
  claimable run and asserts exactly one wins (`claimedIds` has length 1). The crash-recovery test expires the lease and
  reclaims four times, then asserts `claims === 4`, `failures === 0`, `stage === 'staged'` — *every reclaim bumped
  claims… but none counted as a failure… so it never quarantined.*

The fourth tested property is the **crash-is-not-a-failure** invariant underwriting all three: the backoff/quarantine
test drives `fail_pipeline_run` to `max_failures` and asserts quarantine fires at 5 *real* failures, while the
crash-recovery test proves restarts never count. That asymmetry — `claims` for observability, `failures` for the
quarantine gate — is the single most important rule for a financial pipeline: two innocent worker restarts must never
quarantine a household's import.

## Per-stack implementation notes

The pattern is identical across the three products; the implementation differs by runtime. Troth is the TypeScript +
Supabase reference; the same shape adapts to Reveal's Python.

### TypeScript + Supabase — Troth and Ingle

- **Staging table + run table** in the product schema, both `service-role-write` (no write policy), member-`SELECT` for
  the progress UI. RLS via the shared `is_household_member()` helper.
- **The atomic boundary is a plpgsql `SECURITY DEFINER` RPC.** `promote_import`-style: one `INSERT … SELECT` over
  eligible staging rows, `ON CONFLICT` for idempotency, `EXECUTE` granted to `service_role` only, search_path pinned,
  household scoping derived inside the function. This is where the all-or-nothing transaction lives — do not split it
  into application-level batches.
- **The claim/advance/fail RPCs** own all run-state transitions; the worker never `UPDATE`s the runs table directly, so
  the "crash is not a failure" rule and the backoff math live in exactly one place.
- **The worker is a Node process on Fly** (long-lived, claims via `claim_pipeline_run`, leases, heartbeats long
  stages). The edge entrypoint is a Next.js server action / route handler reduced to `stageImport`: validate →
  stage → enqueue, all awaited, zero destination writes, zero fire-and-forget. **For Ingle specifically:** its order
  import is already idempotent via `ON CONFLICT`, so it is the closest to compliant — the gap to close is
  staging-first ingestion and moving any post-import enrichment off the request path onto a durable claimer.
- After schema changes, regenerate types with `supabase gen types typescript`.

### Python — Reveal

- **Same three components, Python-shaped.** A pipeline runner (a long-lived process or a scheduled job, not a
  request handler) drives an explicit stage machine over a `pipeline_runs` table; a staging table holds raw rows
  (image metadata / batch manifest) verbatim until the run completes.
- **The atomic boundary is still a single transactional write** — either the same plpgsql `SECURITY DEFINER` RPC
  called from Python (preferred, because it keeps the all-or-nothing guarantee in the database where it cannot be
  bypassed), or a single `with conn.transaction():` block in the runner doing one bulk upsert. Either way: one
  writer, all-or-nothing, idempotent via `ON CONFLICT`/equivalent.
- **The claim-with-lock pattern ports directly.** `SELECT … FOR UPDATE SKIP LOCKED` against the runs table from the
  Python runner gives the same race-free, lease-based, crash-recoverable claiming. Keep the same `claims` vs `failures`
  split and the same bounded exponential backoff.
- **Reveal-specific seam:** Reveal's pipeline produces JSONB the TS frontend reads. That cross-language contract is
  governed by `DATA_CONSISTENCY.md` (treat the JSONB shape as a derived value pinned by a golden fixture on both
  sides); this standard governs only that the *raw input* lands in staging and the *destination write* is atomic.

## Anti-patterns — the failure shapes this standard forecloses

Each of these is a real shape the Troth rewrite eliminated. They are the negative space around the pattern.

- **Multi-write imports with no transaction boundary.** The original `commitCsvImport` wrote account → import →
  transaction batches as independent calls. A mid-batch failure left earlier batches committed, `imports.row_count`
  lying, and no rollback. The fix is the single atomic promote RPC: no batches at the DB boundary means none to
  half-commit.
- **Fire-and-forget on edge runtimes.** Calling enrichment without `await` from an edge/serverless handler. The runtime
  kills the unfinished promise when the response returns, so the work *silently might never run*. All durable work moves
  to the claiming worker; the edge handler awaits everything it does.
- **Hard `DELETE` for source removals.** A Plaid (or any source) removal must soft-delete (`deleted_at = now()`), never
  physically delete. Financial and audit rows are never destroyed; a hard delete is unrecoverable and breaks
  reconciliation.
- **Claiming without a row lock.** Selecting "the next pending run" without `FOR UPDATE SKIP LOCKED` lets two workers
  grab the same run and double-process it. The lock is not optional; it is the mechanism behind the no-double-processing
  guarantee. (Troth's pre-rewrite worker had exactly this race.)
- **Treating a crash as a failure.** Counting worker restarts and lease reclaims toward the retry/quarantine budget will
  quarantine healthy imports after a couple of innocent deploys. Keep `claims` (observability) strictly separate from
  `failures` (the quarantine gate).
- **GC'ing staging too eagerly.** Deleting raw rows before a run reaches `complete`, or deleting them for quarantined
  runs, throws away the recovery buffer. GC only `complete` runs, only after retention.

## Relationship to other docs

This document owns the **ingestion-and-enrichment path** — how raw facts arrive, land durably, and reach the
destination table without partial writes or loss. Two sibling standards sit adjacent:

- **`DATA_CONSISTENCY.md`** (sibling, authoritative) owns the *derived-value* layer: once raw facts are in the
  destination table, that standard governs computing values from them so every surface agrees. The boundary is clean:
  **pipelines produce and store raw facts; consistency governs the values computed from them.** The two meet at the
  destination table — this standard guarantees what lands there is whole and never duplicated; the consistency
  standard guarantees what is *computed* from it never diverges across surfaces. Agoda's one-canonical-pipeline
  lesson, cited in the consistency doc, sits on exactly this seam and informs both.
- **`TESTING.md`** (sibling, authoritative) owns the *testing-methodology* layer. The integration tests cited
  throughout this document — atomicity, idempotency, SKIP-LOCKED concurrency, crash-recovery, backoff/quarantine,
  cross-household isolation — are the proof harness for this pattern. Where they overlap, `TESTING.md` is the
  authority on *how to structure the test*; this document is the authority on *what pipeline guarantee the test must
  assert*.

The shared color-science standard (`docs/principles/2026-05-26-color-science-stillpoint.md`) is the format template this
document mirrors; it is otherwise unrelated in subject.

## Methodology note

This standard is an *extracted, battle-tested pattern*, not a literature survey. Read this section if you are the
skeptic deciding how much to trust the rules above.

- **Provenance:** the pattern was designed, implemented, and integration-tested in Troth's transaction-safe import
  pipeline (shipped 2026-06-05), then generalized here *after* it proved out. Every rule maps to a concrete file:
  `import_staging_rows.sql` (staging), `import_pipeline_runs.sql` (stage machine + lease), `promote_import_rpc.sql`
  (atomic boundary), `pipeline_run_rpcs.sql` (claim/advance/fail + backoff), `gc_staging_rows.sql` (retention),
  `workers/categorizer/index.js` (the durable stage machine), and `src/lib/troth/import-pipeline.ts` (the shared edge
  entrypoint).
- **Validation:** the three guarantees — no partial data, no data loss, no double-processing — plus the
  crash-is-not-a-failure invariant are each demonstrated by a passing test in
  `tests/integration/queries/import-pipeline.test.ts`, run against a local Supabase (port 54331), never production. This
  is the document's evidence base: a working implementation with tests, not citations.
- **Known limitation, carried forward honestly:** this is a *one-product* extraction. Troth (TS + Supabase) is the only
  shipped instance; the Python (Reveal) and Ingle adaptations above are predictions by analogy, sound but not yet
  shipped. When Reveal and Ingle bring their pipelines to this standard, fold their lessons back in and bump the version
  accordingly.

The one thing to remember: *land the raw rows first, make one atomic function the only writer of the destination,
and let a durable worker drive the stage machine — so a crash resumes instead of corrupting, and a re-run is a
no-op instead of a duplicate.* Resilience is a property you build into the pipeline's shape, not a recovery you
bolt on after the incident.
