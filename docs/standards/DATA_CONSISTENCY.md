---
date: 2026-06-08
title: Cross-surface data consistency for Stillpoint Labs
audience: Noah, future contractors and AI agents building Troth, Ingle, Reveal
status: authoritative shared reference (v1)
---

# Cross-surface data consistency for Stillpoint Labs

This is the shared data-consistency standard for every Stillpoint product. It is the codification of the now-closed
"cross-surface data consistency" research (2026-05-29 → 2026-06-08), and Troth's shipped implementation is its proof.
If a future contractor or agent asks "why does every page have to call the ledger" or "why can't the MCP tool just run
its own query," this is the document that answers.

The problem it exists to prevent is concrete. A Stillpoint product shows the *same* number in several places — a
"what's left this month" on the dashboard, in a widget, in an MCP tool the user calls from chat, in the Ask answer.
When each of those surfaces computes that number independently, they drift: the dashboard says one thing, the widget
says another, chat says a third. The user can no longer tell whether the app is even working, and trust is the one
thing a household-finance product cannot get back once it's lost. This standard makes divergence structurally
impossible rather than something you chase after the fact.

The audience is Noah, a future contractor Noah hires, and the AI agents that write most of the code. All three should
leave this document able to keep one derived value consistent across every surface without re-litigating the
architecture. It spans the whole stack: Troth and Ingle are TypeScript, Reveal is Python, and the standard applies to
all three. Jargon gets defined the first time it appears. Claims get labeled either **evidence-backed** (drawn from
the research corpus, with its citation) or **practitioner folk wisdom** (a reasonable convention we adopt, but not a
measured finding) so we never quietly inherit a vibe as if it were proof.

A note on what "evidence-backed" means here, stated plainly up front: the research corpus contains **no randomized
trials and no systematic reviews** — its strongest source is a single observational study, and the rest are
practitioner case studies and expert opinion. So "evidence-backed" in this document means *literature-derived best
practice with a real citation*, not *experimentally proven*. The Methodology note at the end gives the full picture.
Citations use the corpus format `[Author, Score X.XX, Level N]`, where Score is a 10-dimension credibility composite
and Level is the evidence hierarchy (Level 3 = observational, 4 = professional body, 5 = case study, 7 = expert
opinion). Lower Level number = stronger evidence.

## Quick reference — the rules in one screen

- **One canonical layer per product.** Every derived value (a number you *compute* from raw facts rather than store)
  is computed in exactly ONE module per product — `ledger.ts` in Troth/Ingle, `ledger.py` in Reveal. Nothing else
  computes it.
- **Every surface reads from that layer.** Pages, widgets, API routes, MCP tools, the Ask/chat feature, and any
  future mobile client all import and call the canonical layer. No surface re-implements a computation. Ever.
- **Enforce it with architecture fitness functions in CI.** Structural tests that fail the build when a surface
  bypasses the canonical layer. The build failure — not a doc — is what makes the rule stick, especially with AI
  agents.
- **Prove agreement with golden-fixture + property-based oracle tests.** Seed a known state, run every surface,
  assert they all return the same number — and pin at least one side to a hand-verified golden value.
- **Cache policy is correctness, not performance.** For financial / decision-critical reads, the default is no
  caching (`staleTime: 0`, `no-store`). You trade a little speed for the guarantee that the number is real.
- **Do NOT reach for CQRS or DB-views-as-computation at this scale.** Separate read/write stores *introduce* the
  staleness window you are trying to remove. Keep complex derived logic in application code.
- **Never trust an AI-generated relational assertion alone.** An agent told to assert `A === B` can satisfy it by
  making both sides wrong. Every cross-surface check pins one side to an external, hand-verified truth.

The rest of this document is the receipts for those rules.

## The framework — four parts, two prevent and two detect

The research synthesized into a four-part framework. The first two parts **prevent** divergence (they make it
structurally hard to introduce); the last two **detect** it (they catch what slips through). This is the council's
recommended Option A + D — per-project canonical layer with fitness functions, plus a golden-fixture and
property-oracle harness — chosen over a shared cross-language SDK (premature) and DB-views-as-source-of-truth (fights
the language for complex logic). Together they implement consistency as an *architecture property you enforce
mechanically*, not a bug you fix repeatedly.

### Part 1 — One canonical computation layer owns every derived value

Each product has exactly one module that computes each derived value. In Troth that is the ledger: it owns
`getWhatsLeft()`, unpaid-bill counts, stale-alert detection, and pay-period boundaries. Pages, MCP tools, chat, and
any future client import and call it — none recompute. Raw *facts* (a transaction, a bill amount) are stored; derived
*values* are computed in the one place, on demand.

- **Why it works:** if a value is computed in exactly one place, there is nothing to keep in sync. Divergence becomes
  structurally impossible rather than something you police. The "different numbers on different pages" bug is a
  *duplication* problem, not a *synchronization* problem — and the fix is deleting the duplicate computations, not
  building clever sync. **Evidence-backed.**
- **The defining case:** Agoda's FINUDP — three teams independently built three pipelines from the same upstream data
  and got three different financial metrics. The fix was not syncing; it was routing everything through one canonical
  pipeline [Agoda Engineering, Score 7.70, Level 5]. Their words: *"financial metrics may differ depending on which
  pipeline produced them, creating confusion and undermining data reliability."* **Evidence-backed.**
- **Corroboration across layers:** the canonical balance *"lives in the ledger, not in any view"* [Modern Treasury,
  Score 7.20, Level 5]; on the frontend, *"the store is the single source of truth"* and *"when a value can be
  transformed with a pure function … we don't really need to store it"* [Codeminer42, Score 6.90, Level 7]; the root
  anti-pattern is multiple layers *"each believing they're authoritative"* [System Design with Sage, Score 6.00,
  Level 7]. **Evidence-backed.**
- **Where it sits in the stack:** the existing server layer (Next.js server actions / API routes; FastAPI routes in
  Reveal) acts as the Backend-for-Frontend that *exposes* the canonical layer — but the BFF must only aggregate and
  format. *"If your BFF starts accumulating business logic, it's a sign you're using an anti-pattern"* [Marmelab,
  Score 6.80, Level 7]. Domain rules live in the canonical layer, not the BFF. **Evidence-backed.**
- **Store raw, compute on demand** is the corollary: don't store a derived value you can recompute, because the
  stored copy is the thing that drifts. **Practitioner folk wisdom** (broadly held, mapped from the frontend-state
  sources [Codeminer42, Score 6.90, Level 7] to the server-side case; no source measured it directly).

### Part 2 — Fitness functions enforce that all surfaces read from it

An **architecture fitness function** is an automated test that checks an *architectural rule* — e.g., "no page
component may import a derived-state engine function directly" or "every MCP tool must call the ledger" — and fails
the build if the rule is broken. These run in the normal test runner (Vitest for TS, pytest for Python), with no new
infrastructure.

- **Why it works, and why it matters more here than for a big team:** the rule is enforced mechanically and
  continuously, and — critically for AI-assisted development — the agent gets the signal it actually responds to. A
  documented rule gets lost in a long context window; a build failure does not. *"Agents respond to automated signals
  more reliably than to documentation: an agent that sees a build failure will try to fix it, while an agent that
  reads 'please don't cross layer boundaries' … might still cross them"* [AI Pattern Book, Score 6.45, Level 7].
  Fitness functions give a solo developer the guardrails a large team gets from code review. **Evidence-backed.**
- **The exact mechanism in TypeScript:** ArchUnitTS expresses the rule as a normal test —
  `projectFiles().inFolder('src/presentation/**').shouldNot().dependOnFiles().inFolder('src/database/**')` — and runs
  in CI like any unit test [ArchUnitTS / Niessen, Score 6.95, Level 7]. **Evidence-backed.** (Troth's shipped
  implementation does this with a lighter-weight import-scan rather than the ArchUnitTS dependency — see Reference
  implementation. The principle is identical; the tooling is a choice.)
- **Boundary condition:** fitness functions are *structural* — they catch import-boundary and layering violations,
  not data-*value* mismatches between two surfaces. A page can import the ledger correctly and the two surfaces can
  still disagree on a number for a logic reason. That is exactly what Parts 3 and 4 are for. [AI Pattern Book,
  Score 6.45, Level 7] flags this limitation explicitly. **Evidence-backed.**

### Part 3 — Golden-fixture + property-based oracle tests catch divergence

Two complementary value-level tests, both running in the existing test runner.

- **Golden fixtures (detection of *change*):** seed a known household/product state, run every surface, capture each
  output once as a "golden" record, and fail the test whenever any surface's output changes. Updating a golden
  requires deliberate human sign-off — the diff *is* the discipline. Slack runs this exact scan/check/report shape in
  production [Slack Engineering, Score 7.05, Level 5]. Kerckhove gives the mechanics: golden tests find defects *"that
  cause a certain result to change since the version that produced the previous result,"* and *"if the current output
  differs from the golden output: Fail the test and offer to update the golden output"* [Kerckhove, Score 6.60,
  Level 7]. **Evidence-backed.**
- **The crucial caveat:** golden tests *"detect change, not initial correctness"* [Kerckhove, Score 6.60, Level 7].
  The first time you capture a golden, a human must verify by hand that the number is actually right. After that, the
  golden defends it. **Evidence-backed.**
- **Property-based oracles (detection across edge cases):** instead of one example, generate hundreds of random valid
  states and assert a property holds for all of them — here, `dashboard(state) === mcp(state)`. This is the oracle /
  equivalence pattern: *"a specific functionality has one clearly correct but slow implementation plus an alternative
  implementation that is efficient but complicated"* — test that they agree [INNOQ, Score 6.80, Level 7]. fast-check
  (TS) / Hypothesis (Python) generate the edge cases example fixtures miss — empty arrays, zero balances, negatives
  — and shrink any failure to a minimal counterexample. **Evidence-backed.**
- **The AI guardrail that makes this safe — read this twice:** pin at least one side of every cross-surface assertion
  to a hand-verified golden value. A bare `A === B` is the single most dangerous test to hand an agent. Agents write
  relational/range checks only 3–8% of the time, and worse, an agent told to make `dashboard === mcp` pass can
  satisfy it by breaking *both* sides identically [arXiv 2602.07900, Score 7.90, Level 3 — the highest-rigor source in
  the corpus]. The golden value is the external truth the relational check lacks. **Evidence-backed.**

### Part 4 — Cache policy as correctness

For financial / decision-critical reads, the default is *no caching*: `staleTime: 0` at the query-cache layer and
`no-store` / `no-cache` at the HTTP layer. Mutations must explicitly invalidate related queries.

- **Why it's a correctness rule, not a tuning detail:** when cache behavior is left implicit, *"every layer invents
  defaults: framework runtime, CDN, browser, intermediary proxies"* and stale data looks current. Optijara's
  postmortem traces a real "stale data looks current" incident to content-page cache defaults applied to an
  operational dashboard, and lands on the principle: *"Cache policy is not an optimization detail. It is part of
  correctness"* [Optijara, Score 6.45, Level 5]. **Evidence-backed.**
- **The client-side trap:** hand-written cache keys are *"the single biggest source of client-side consistency
  bugs"* — *"Cache keys are just arrays. Typos and mismatches are easy and invisible"* — and `staleTime: 0` is the
  valid "nuclear option" for financial data [Ignasave / TanStack pattern, Score 6.35, Level 7]. Where practical,
  generate cache keys from the API spec rather than typing them. **Evidence-backed.**
- **The corollary back to Part 1:** don't store derived values, so there's nothing stale to serve in the first place
  [Codeminer42, Score 6.90, Level 7]. **Evidence-backed.**
- **What "no caching" costs and why it's fine here:** a household-finance dashboard is read a handful of times a day
  by two people. The performance cost of skipping the cache is negligible; the cost of showing a wrong balance is
  trust. The corpus gives no concrete "freshness SLO" number for a household dashboard — how stale is too stale is
  left to judgment, and our judgment is zero. **Practitioner folk wisdom** (the threshold is a Stillpoint choice, not
  a measured finding).

### Boundary conditions — when the heavier patterns ARE justified

This standard tells you to skip CQRS and DB-views-as-computation *at current scale*. That is a scale-conditional
recommendation, not an absolute. The honest steel-man and the conditions for revisiting:

- **CQRS / separate read+write stores.** **CQRS** (Command Query Responsibility Segregation) splits the write side
  from the read side into separate stores, accepting that the read side lags slightly. It delivers real, measured
  wins — one fintech case cut dashboard load from 1s to 400ms — *"typically within 100–500ms"* of eventual
  consistency [Niessen, Score 6.80, Level 5], and the canonical reference confirms the cost: *"the read data might
  not show the most recent changes immediately. This delay results in stale data"* [Microsoft Azure, Score 8.05,
  Level 4 — the highest-scored source in the corpus]. **The reason to skip it now:** Microsoft's own guidance says
  the pattern *"might not be suitable when … the domain or business rules are simple,"* and at solo / single-team
  scale, splitting stores *introduces* the exact 100–500 ms inconsistency window this standard exists to eliminate.
  Niessen applied CQRS to *one* compliance-critical service and left the rest as CRUD. **Revisit when:** read volume
  or team size grows materially, or one service becomes genuinely read-heavy and stable. Retrofitting later is more
  expensive than designing for it — but premature simplification is a real risk only at scale we do not have.
  **Evidence-backed (the boundary, in both directions).**
- **Database views as the primary computation home.** Materialized views are *"a durable, read-only cache … optimized
  for fast and efficient queries"* [Microsoft Azure, Score 8.05, Level 4] and are excellent for *simple, stable,
  read-heavy* aggregations. They are painful for *complex, frequently-evolving* logic — Troth's semimonthly +
  end-of-month pay-period math in SQL is "fighting the language," and harder for an AI agent to modify safely. Keep
  that logic in application code; reserve views for simple stable aggregations if at all. **Evidence-backed.**
- **Pact / consumer-driven contract testing.** Contract tests *"assert that inter-application messages conform to a
  shared understanding"* [Pact Foundation, Score 7.75, Level 4] — service-to-service, not page-to-page. It is the
  wrong tool for whether two surfaces of *one* app agree on a number. Do not reach for it. **Evidence-backed.**

## Anti-patterns — the incidents that teach the rule

Each of these is a documented failure from the corpus. They are the negative space around the framework.

- **Multiple independent pipelines computing the same value.** The Agoda FINUDP incident: three teams, three
  pipelines, same upstream data, three different financial metrics — *"creating confusion and undermining data
  reliability"* [Agoda Engineering, Score 7.70, Level 5]. The MCP-tool-runs-its-own-query and the
  page-computes-inline patterns are the small-scale version of this. The fix is the same: delete the duplicate
  computation, route through the one canonical layer.
- **Cache staleness treated as a performance knob.** The Optijara postmortem: content-page cache defaults applied to
  a decision-critical dashboard meant *"stale data looks current,"* and the bug was silent until someone noticed
  [Optijara, Score 6.45, Level 5]. Treating cache as performance rather than correctness is how a wrong number ships
  looking right.
- **MCP / page query duplication.** The ground-truth read of the Stillpoint codebases found Ingle's MCP server
  *reimplements every query as raw SQL in a separate process* — zero code sharing between pages and MCP tools, so a
  schema change must be applied in two places or they silently diverge. This is the worst-case shape of the Agoda
  anti-pattern living inside one product. (Ground-truth codebase finding from the brainstorm; **practitioner folk
  wisdom** as a general lesson, **evidence-backed** as a specific observed state.)
- **The AI relational-assertion failure mode.** An agent told to assert two surfaces agree will, a meaningful share
  of the time, produce a test that passes while both surfaces are wrong — because it can satisfy `A === B` by breaking
  both [arXiv 2602.07900, Score 7.90, Level 3]. The same study found agents emit value-revealing prints 69–77% of the
  time and write relational checks only 3–8% of the time, and that *"agent-[generated] tests … can also be harmful
  if they embed incorrect assumptions or oracles, diverting effort toward satisfying the test rather than resolving
  the target issue."* The mitigation is structural (Part 2) plus golden-pinned assertions (Part 3) — design tests the
  agent writes reliably; enforce the structure the agent cannot reason about. **Evidence-backed.**

## Per-stack implementation notes

The principle is identical across the three products; the implementation differs by runtime. No source in the corpus
addressed the *cross-language* case directly — Troth (TS), Ingle (TS, separate MCP process), and Reveal (Python)
cannot trivially share one module. A shared cross-language SDK was the council's *rejected* answer to that gap, deemed
premature for a solo developer. The convention below is per-project, enforced by convention rather than coupling.

### TypeScript — Troth and Ingle

- **Canonical layer:** a `src/lib/<product>/ledger.ts` module marked `import "server-only"`, exporting one async
  function per derived value (`getWhatsLeft`, `getUnpaidBills`, `getStaleAlerts`, `getPeriodBoundaries`,
  `getUncategorizedCount`, …). Each takes the tenant id (`householdId`) plus an optional `asOfDate` for deterministic
  testing, and returns a typed result. Pure computation helpers live in an `engine/` module; the ledger orchestrates
  fetch + compute. The BFF (Next.js server actions / API routes) only aggregates and formats.
- **Fitness functions:** import-boundary checks in Vitest. Troth's shipped version walks `src/app/(app)/**` and fails
  if a page imports a derived-state engine symbol (`calculateWhatsLeft`, `findUnpaidBills`, `computeStaleBillAlerts`,
  `computeAlerts`) directly, plus a rule that `finance-tools.ts` (the MCP layer) imports from `ledger.ts` and never
  calls the engine symbols itself. ArchUnitTS is available if you want richer dependency/cycle rules
  [ArchUnitTS / Niessen, Score 6.95, Level 7].
- **Golden + property tests:** golden fixtures in Vitest — a deterministic `TestHousehold` seed with known balances,
  bills, transactions, and income source, asserting the ledger path and the MCP path return identical
  `totalCents` / `unpaidBillsCents` / `spentCents`, plus period-boundary and uncategorized-count agreement. Add
  fast-check property oracles for the highest-value numbers. **For Ingle specifically:** the priority is rewiring the
  MCP server to import the shared TS query/ledger layer instead of reimplementing raw SQL — that is the single
  highest-leverage consistency fix in the Stillpoint family.
- **Cache:** `staleTime: 0` on TanStack Query for financial/decision reads; `no-store` on the corresponding route
  handlers; invalidate related queries on every mutation.

### Python — Reveal

- **Canonical layer:** the equivalent `ledger.py` (or a `domain/` package) owning every derived photography metric,
  with the same shape — one function per derived value, tenant-scoped, `as_of` parameter for determinism, typed
  return (TypedDict or dataclass). The FastAPI route layer is the BFF: aggregate and format only.
- **Fitness functions:** the same import-boundary idea in pytest — a test that scans route/presentation modules and
  fails if they import the computation internals directly, asserting they go through `ledger.py`. (Python has no
  ArchUnitTS; a small AST/import-scan test is the equivalent and is what fits a solo dev.)
- **Golden + property tests:** golden fixtures in pytest seeded from a known state; Hypothesis for property-based
  oracles in place of fast-check.
- **The cross-language contract concern — Reveal-specific.** Reveal's Python pipeline writes JSONB and the TS frontend
  reads and validates it. The JSONB shape is defined twice — once as a Python `TypedDict`, once as a TS `interface`
  — which is a *silent* consistency dependency: change one and the other drifts with no build failure. Treat the JSONB
  contract as a derived value that crosses the language boundary. Until a generated-types pipeline exists, add a
  golden fixture on *both* sides that pins the serialized JSONB shape to one hand-verified record, so a drift between
  the TypedDict and the interface fails a test rather than corrupting data silently. (Ground-truth finding from the
  brainstorm; the mitigation is a **literature-derived prediction**, not a measured result.)

## Reference implementation — Troth is the proof

Troth shipped the full framework on 2026-05-29 (plan:
`troth/docs/plans/assimilated/2026-05-29-domain-layer-consistency-framework.md`). It is the working example to copy:

- **Canonical layer:** `troth/src/lib/troth/ledger.ts` — `getWhatsLeft()`, `getUnpaidBills()`, `getStaleAlerts()`,
  `getPeriodBoundaries()`, `getUncategorizedCount()`. The dashboard, bills page, MCP tools (`finance-tools.ts`), and
  the Ask feature all call it; none recompute.
- **Fitness functions:** `troth/tests/unit/fitness.test.ts` — fails the build if a page imports a derived-state engine
  symbol directly, and asserts `finance-tools.ts` delegates to the ledger.
- **Golden fixtures:** `troth/tests/unit/golden-fixtures.test.ts` — a deterministic `TestHousehold` with five
  cross-surface assertions (what's-left across ledger + MCP paths, unpaid-bill count, stale-alert threshold,
  period boundaries, uncategorized count).

When in doubt about how to structure a new product's consistency layer, read those three files first.

## Relationship to other docs

This document owns the **consistency pattern** — where a derived value is computed and how every surface reads from
it. Two sibling standards are planned and overlap at the edges:

- **TESTING.md** (sibling, authoritative) owns the *testing-methodology* layer. Golden, fitness, and
  property-based tests appear in both documents because they serve consistency *and* general correctness. Where they
  overlap, TESTING.md is the authority on *how to write the test*; this document is the authority on *what consistency
  property the test must assert* (cross-surface agreement, with one side golden-pinned).
- **DATA_PIPELINES.md** (sibling, authoritative) owns import/ETL and the raw-fact ingestion path. The boundary:
  pipelines produce and store *raw facts*; this standard governs the *derived values* computed from them. The Agoda
  case sits on the seam — its lesson (one canonical pipeline) informs both.

The shared color-science standard
(`stillpointlabs-site/docs/principles/2026-05-26-color-science-stillpoint.md`) is the format template this document
mirrors; it is otherwise unrelated in subject.

## Methodology note

This standard is the codification of the 2026-05-29 deep-research project (methodology `deep-research v0.1.0`), closed
2026-06-08. Read this section if you are the skeptic deciding how much to trust the rules above.

- **Corpus:** 17 sources retained after evaluation, scored on a 10-dimension credibility rubric (Authority, Evidence
  Quality, Currency, Intent, Bias, Logic, Corroboration, Intellectual Honesty, Specificity, Relevance) and classified
  on a 9-level evidence hierarchy.
- **Evidence strength — stated plainly:** the corpus contains **zero Level-1 (systematic review) and zero Level-2
  (RCT) sources.** The strongest single source is one Level-3 observational study of AI-agent test-writing behavior
  (arXiv 2602.07900, Score 7.90); the rest are practitioner case studies (Level 5), professional-body docs (Level 4),
  and expert opinion (Level 7). Therefore **every recommendation here is a literature-derived best practice — a
  well-reasoned prediction drawn from how other engineers solved adjacent problems — not experimental proof for this
  exact solo / AI-assisted / multi-product setup.** Treat it as a strong hypothesis that Troth has now shipped and
  partially validated, not as settled science. This is the distinction the document labels throughout as
  *evidence-backed* (cited, literature-derived) vs *practitioner folk wisdom* (a convention we adopt without a
  citation).
- **Verification:** all 17 source cards were sampled for citation verification (100%); 16 verified, 1 had fabricated
  quotes that were removed/reworded before tally, **0% unresolved failure rate** (≤5% gate band). No verbatim quote in
  this document is invented — every quoted string traces to a verified source card.
- **Known limitations, carried forward honestly:** (1) confirmation skew — the corpus agree:disagree ratio was 7:1,
  flagged and partially mitigated with a falsification query and a steel-man of the contrarian (preserved above in
  Boundary conditions); (2) no genuine contrarian source arguing *for* controlled duplication was found; (3) the
  cross-language (TS + Python) canonical-layer implementation is synthesis across partial sources, not a cited
  blueprint — which is precisely why the per-stack notes are marked as prediction where they go beyond the corpus.

The one thing to remember: *compute each number once, make every surface read from that one place, and let the build
fail when something bypasses it.* Consistency is an architecture property you enforce mechanically — not a bug you
chase after the fact.
