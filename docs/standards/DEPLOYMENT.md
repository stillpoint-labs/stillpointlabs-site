---
date: 2026-07-01
title: Do-no-harm deployment standard — Stillpoint Labs
audience: Noah, future contractors and AI agents; the CI/CD orchestrator that enforces these gates
status: Draft (v0.1, for review)
---

# Do-no-harm deployment standard — Stillpoint Labs

> **Status: Draft (v0.1, for review)**

The canonical standard for shipping changes to production **without harm**: small, backward-compatible, independently
reversible changes with automated statistical rollback and a bounded human handoff. It complements
[`MIGRATIONS.md`](./MIGRATIONS.md) (which owns the database schema tier) and is validated against a cited research pass —
Martin Fowler's *Parallel Change*, the Google SRE Book/Workbook + CRE, Netflix/Google *Kayenta*, Argo Rollouts, and GitHub
*Scientist*. The (in-design) CI/CD orchestrator enforces the eight gates below; until it exists, they are the manual
checklist.

The problem it prevents is concrete: a bad deploy reaching production with **no automatic containment**, a destructive
change coupled to a code change so it **can't be reverted**, and unbounded auto-remediation that **thrashes** instead of
stopping. Each rule below removes one of those failure modes.

---

## The one principle to internalize

**Reversibility lives at the code/deploy layer, not in the database.** Migrations are **forward-only**. You do NOT make
each migration "fully reversible" — you make each one **backward-compatible** and decouple the schema change from the code
change, so you can roll back the **binary without rolling back the schema** (Google's `v+1 → schema → v+2` sequence). A
`DROP` loses data and is not reversible on its own; **PITR/snapshot before drop** is the escape hatch for the rare genuine
removal, not the everyday model.

---

## Quick reference — the 8 gates in one screen

1. **Change size.** One logical change per deploy; reject oversized diffs. Small batches limit blast radius.
2. **Backward-compat.** Migrations additive/expand-only; `DROP`/destructive `UPDATE` is blocked in the normal path and
   routed to a human-approved lane (see [`MIGRATIONS.md`](./MIGRATIONS.md) `expand-contract-ack`).
3. **Schema/binary decoupling.** `v+1` (schema-tolerant) → schema change → `v+2` (schema-using). **Auto-rollback is
   binary-scoped, never schema-scoped.**
4. **Functional pre-gate.** Smoke + e2e (PWA + MCP server) must pass before promotion.
5. **Canary + concurrent baseline.** Route 1–5% to a canary; compare against a **concurrently-deployed baseline running the
   current prod version** (same time, size, traffic) — NOT a static historical snapshot. Judge **statistically**
   (significance test + failure-count threshold) on the Four Golden Signals.
6. **Auto-rollback triggers.** Canary FAIL verdict / golden-signal regression vs baseline / error-budget burn → abort +
   reroute all traffic to prod.
7. **Bounded remediation.** ≤2 fix→deploy→rollback cycles; **revert-first** (roll back before forward-fix); stop-and-page on
   a distinct-error slew, any schema/stateful PR, or an error-budget breach.
8. **Contract gate.** Remove old code/objects only when **both** static (dependency graph) **and** runtime telemetry are at
   **zero** across a window ≥ the slowest consumer's cadence.

*Gates 3, 6, and 8 are the highest-leverage "do no harm" controls.*

---

## The change shape: small, backward-compatible, expand/contract

- **Small changes, one logical change per deploy.** Error-budget burn is directly proportional to the fraction of traffic
  exposed to a defect — small changes + staged ramps are the quantitative reason harm stays contained. [SRE Workbook]
- **Migrations follow expand/contract (Parallel Change):** *expand* (additive: add the new table/column) → *migrate*
  (dual-write, then dual-read) → *contract* (remove the old, gated on Gate 8). Our four-PR split (add → write → read →
  remove) is the standard three phases with *migrate* decomposed into write-path then read-path deploys. [Fowler]
- **Schema/binary decoupling.** Ship `v+1` that *tolerates* the new schema, then apply the schema, then ship `v+2` that
  *uses* it. If either binary misbehaves you roll back the code and leave the additive schema in place. This is *why*
  expand/contract exists. [Google CRE]

---

## The gates in detail

- **Gate 4 — functional pre-gate** is a deterministic check (smoke + e2e) that sits **in front of** the statistical canary
  gate. Deterministic first (does it work at all?), statistical second (is it as good as prod?).
- **Gate 5 — canary** must compare against a **concurrent baseline = the current prod version**, deployed at the same time,
  same size, serving the same live traffic. This removes confounds (cache warmup, heap) that a static "golden" snapshot
  cannot. Judge with a significance test (e.g. Mann-Whitney U) plus a **failure-count threshold** — not a single-error
  tripwire, which flaps on noise. [Spinnaker/Kayenta; Netflix]
- **Gate 6 — rollback triggers** are the Four Golden Signals (latency, traffic, errors, saturation) plus an
  error-budget/SLO guard — not raw error appearance. An unsafe verdict aborts the canary and routes traffic back to prod.
  [SRE Book/Workbook]

---

## Rollback + remediation policy

- **Revert-first.** On a suspected bad release, roll back first and investigate second. Rollback is normal and expected,
  not a failure or an attack on the author. Prefer it over a hasty roll-forward, which often fails to fix or makes things
  worse and moves you away from a known-good state. [Google CRE]
- **Bounded auto-remediation.** At most **2** fix→deploy→rollback cycles. One error firing 1000× counts as **one** error; a
  slew of *distinct* new error signatures stops immediately. After 2 failed cycles, **stop and page a human.** Also
  stop-and-page on any error-budget breach or any PR that touches schema/stateful state.
  *(The "2 cycles" number is our tunable policy, not a canonical constant; it is subordinate to the error-budget guard —
  stop earlier if the budget is already spent.)*

---

## Deadness proof before removal (Gate 8)

Before the *contract* phase removes old code or DB objects, prove they are dead with **two signals, both at zero**, over a
window **≥ the slowest consumer's cadence** (nightly batch, monthly reports, third-party integrations — not just request
traffic):

- **Static** (what *could* reference it): `pg_depend` + `information_schema` for DB (views/FKs/functions/triggers);
  `knip`/`ts-prune` (TS), `vulture` + prod coverage (Python), and grep across **consumer repos** for code.
- **Runtime** (what *actually* hits it): `pg_stat_statements` (grep for the old table/column — the best signal for
  column-level usage) + `pg_stat_user_tables`/`_indexes` deltas for DB; a **deprecation-canary** counter at the old path's
  entry (endpoint/function/MCP tool), shipped in the read-migration PR, for code.

This mirrors GitHub *Scientist*-style dual-run verification. Instrument in the read PR so the evidence already exists when
the remove PR comes up. [GitHub Scientist; Kayenta window sizing]

---

## Open policy questions (to resolve in the orchestrator design)

1. **Multi-consumer deadness window** — how long reliably proves deadness when some consumers reference an object only
   intermittently? Request-canary sizing guidance (≥~50 samples / hours) badly under-windows slow consumers.
2. **Auto-quarantine safety** — is auto-revert + block-re-merge safe, or does it risk merge-queue deadlock / blocking
   unrelated fixes? Needs a defined policy.
3. **Failing schema-expand** — when the *failing* change is the additive expand itself (forward-only), the correct action
   is pause+page or a compensating additive migration, **never** a `DROP`-rollback.
4. **Breaker vs budget precedence** — when the 2-cycle circuit-breaker and the error-budget halt disagree, which governs?

---

## Caveats (policy vs canon)

The "2-cycle" cap, "quarantine the PR", and the exact `pg_depend`/`pg_stat_statements`/deprecation-canary instruments are
**our defensible engineering choices**, not citable canon — labelled as policy. The canonical, verified anchors are Fowler
Parallel Change, Google SRE Book/Workbook + CRE, Netflix/Google Kayenta, Argo Rollouts, and GitHub Scientist. The
automated-canary-analysis *pattern* is current; the specific *tool* (Kayenta/Spinnaker) has ceded ground to Argo
Rollouts / Flagger.

## Sources

- Fowler/Sato, *Parallel Change (expand/contract)* — https://martinfowler.com/bliki/ParallelChange.html
- Google CRE, *Reliable releases and rollbacks* —
  https://cloud.google.com/blog/products/gcp/reliable-releases-and-rollbacks-cre-life-lessons/
- Google SRE Book, *Monitoring Distributed Systems (Four Golden Signals)* —
  https://sre.google/sre-book/monitoring-distributed-systems/
- Google SRE Workbook, *Error-Budget Policy* + *Canarying Releases* — https://sre.google/workbook/error-budget-policy/ ·
  https://sre.google/workbook/canarying-releases/
- Netflix/Google, *Automated Canary Analysis (Kayenta)* —
  https://netflixtechblog.com/automated-canary-analysis-at-netflix-with-kayenta-3260bc7acc69
- Spinnaker/Kayenta, *Canary best-practices + judge* — https://spinnaker.io/docs/guides/user/canary/best-practices/
- Argo Rollouts, *Analysis + failureLimit* — https://argo-rollouts.readthedocs.io/en/stable/features/analysis/
- GitHub, *Scientist (dual-run verification)* — https://github.com/github/scientist
