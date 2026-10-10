---
date: 2026-06-09
title: Test sufficiency for Stillpoint Labs
audience: Noah, future contractors and AI agents building Troth, Ingle, Reveal
status: authoritative shared reference (v1)
---

# Test sufficiency for Stillpoint Labs

This is the shared testing standard for every Stillpoint product. It is the third sibling in the set, alongside
`DATA_CONSISTENCY.md` and `DATA_PIPELINES.md`, both of which already forward-reference it as the authority on *how* to
test the harnesses they rely on. It answers one question those documents kept deferring: **when is a codebase tested
enough?** If a future contractor or agent asks "why isn't there a 90% coverage gate," or "why is the financial code
mutation-tested but the settings page barely tested at all," or "why can't I trust this passing AI-written suite," this
is the document that answers.

The problem it exists to prevent is concrete. Across three products there is **no shared notion of "tested enough,"**
and the coverage gates that stand in for one are uneven and incoherent: Troth gates 85% on only six hand-picked util
files, Ingle gates ~24% on its whole `src/**`, Reveal gates 85% on everything. None of those numbers are comparable,
and worse, **the number itself is a weak proxy for whether the tests would actually catch a bug** — the field's
largest study found coverage only *low-to-moderately* correlated with effectiveness once suite size is controlled for
[Inozemtseva & Holmes, Score 8.85, Level 1]. A high percentage looks rigorous and proves almost nothing. Meanwhile the
crown-jewel flows — Troth's transaction-safe import pipeline, the thing `DATA_PIPELINES.md` exists to protect — have
integration tests but no end-to-end proof of the user's actual journey. And because the AI writes most of the tests,
the suite is silently at risk of encoding what the code *currently does* (bugs included) instead of what it *should*
do. This standard replaces "what's our coverage?" with a sufficiency methodology that gates *effectiveness* where it
matters, *completeness* on every flow, and keeps a human on the assertion.

The audience is Noah, a future contractor Noah hires, and the AI agents that write most of the code. All three should
leave this document able to decide when a module is tested enough — and to defend a deliberately-thin gate as strategy,
not neglect — without re-deriving the methodology. It spans both stacks: Troth and Ingle are TypeScript (Vitest +
Playwright), Reveal is Python (pytest), and the standard is **one set of principles with per-stack tooling**, never two
divergent regimes. Claims get labeled either **evidence-backed** (drawn from the research corpus, with its citation) or
**practitioner folk wisdom** (a reasonable convention we adopt, not a measured finding) so we never quietly inherit a
vibe as if it were proof.

A note on what "evidence-backed" means here, stated plainly up front, because it differs from the sister documents.
Unlike `DATA_CONSISTENCY.md` (literature-derived best practice, no primary evidence) and `DATA_PIPELINES.md` (a single
shipped implementation), **this standard rests on unusually strong evidence**: the corpus contains **two Level-1
large-scale controlled studies** (ICSE) and **five Level-2 peer-reviewed controlled studies**. The two findings that
anchor the whole document — coverage is a weak quality signal, and mutation score is *itself* a weak proxy for
real-fault detection once you control for suite size — are *both* Level-1 results. So the recommendations rest on
measured outcomes, not just expert opinion. Citations use the corpus format `[Author, Score X.XX, Level N]`, where
Score is a 10-dimension credibility composite and Level is the evidence hierarchy (lower Level number = stronger
evidence: 1 = large-scale controlled, 2 = peer-reviewed controlled, 3 = observational, 4 = professional body / industry
report, 5 = practitioner case study, 6 = expert essay, 7 = expert opinion). The Methodology note at the end gives the
full picture.

## Quick reference — the rules in one screen

- **The tier taxonomy is a vocabulary, not a quota.** Five tiers — unit / component / integration / functional / e2e —
  each with a written boundary (below) and a per-tier expectation. Write down, per product, what each means in *that*
  codebase; do not import another team's ratios. Effort thins toward the top — e2e stays minimal. **Evidence-backed**
  [Vocke, Score 8.45, Level 5; Dodds, Score 7.20, Level 6].
- **Effectiveness is measured by mutation score on the code that matters — not line coverage.** Coverage says a line
  *ran*; mutation score says a test would *notice* if that line broke. Gate effectiveness with mutation score on
  hotspots, read as a *trend*, never a single absolute. **Evidence-backed** [Papadakis, Score 9.25, Level 1;
  Inozemtseva & Holmes, Score 8.85, Level 1].
- **Prioritize test effort by risk = churn × complexity.** Defects concentrate in code that is complex *and* changed
  often; relative churn discriminated fault-prone components at 89.0% accuracy. On young / low-signal modules with no
  churn history, fall back to complexity + impact judgment. **Evidence-backed** [Nagappan & Ball, Score 8.60, Level 2;
  Tornhill, Score 7.15, Level 5; the fallback is the logged dissent, below].
- **Coverage is advisory, not a target.** Use it to *find under-tested code*; never as the pass/fail gate (Goodhart —
  the moment a number is a target it stops measuring). It is only weakly and project-variably correlated with
  effectiveness. **Evidence-backed** [Inozemtseva & Holmes, Score 8.85, Level 1].
- **e2e completeness = a flow/failure-state traceability matrix.** Every user flow has ≥1 e2e test; every success *and*
  failure state of each critical flow is tested somewhere in the stack; mock only external services. The matrix is
  checked into the repo and CI asserts each row maps to a real test id. **Evidence-backed** [AutoE2E, Score 7.60,
  Level 3; Vocke, Score 8.45, Level 5].
- **Contract tests are for cross-system seams, NOT page-to-page consistency.** CDC tests verify separately-deployable
  services still fit together. Page-to-page agreement inside one app is `DATA_CONSISTENCY.md`'s job, not a contract
  test's. At solo scale CDC may simply not apply. **Evidence-backed** [Lehvä, Score 7.65, Level 4].
- **The human owns the oracle.** Agent-written tests tend to capture *actual* not *expected* behavior, and an agent can
  satisfy an assertion by making both sides wrong. On hotspot / financial tests, require human oracle review +
  exact-value / golden assertions; a passing AI-generated suite is **never** proof of correctness. **Evidence-backed**
  [Konstantinou, Score 8.50, Level 2; Schäfer, Score 8.65, Level 2].

The rest of this document is the receipts for those rules.

## The tier taxonomy — five tiers, each with a written boundary

Sufficiency is judged *per tier*, so the tiers must be defined in writing before any metric is computed — the
industry's own authorities concede the terms are fuzzy, and Dodds openly disavows his own 70% threshold as "no science
there" [Dodds, Score 7.20, Level 6]. The five tiers below are the shared vocabulary; each product writes down what they
mean in *its* codebase and does not import another team's numeric ratios. The governing rule across all five: **effort
thins as you go up — the more high-level the test, the fewer you should have** [Vocke, Score 8.45, Level 5].
**Evidence-backed.**

| Tier | Crisp boundary | What to mock | Per-tier expectation |
| --- | --- | --- | --- |
| **Unit** | One function / module in isolation. A pure-computation helper (`money.ts`, `engine/*.ts`, a scoring function). | Everything outside the unit — collaborators may be stubbed ("solitary") or real if cheap and pure ("sociable"). | Many, fast. The base of the stack. Hotspot units carry the mutation gate. |
| **Component** | One UI component / one rendered surface, exercised through its public props/DOM, not its internals. | Network, the data layer, and time. Render against fixtures, not a live DB. | Cover stateful/branching components; trivial presentational ones are not worth a test. |
| **Integration** | Several *real* pieces wired together — a server action calling the real ledger calling a real (local) DB; a worker stage handler against local Supabase. | Only external services (Plaid, the LLM, email). The DB is real (local), not mocked. | The confidence-per-cost workhorse. The widest band for TS (trophy); the service layer for Python (pyramid). |
| **Functional** | One complete feature/use-case end to end *below the browser* — an HTTP route or RPC driven with a real request and asserted on its real response + DB effect. | External services only. | Where a feature's success-and-failure logic lives if it doesn't need a real browser. The import-pipeline RPCs live here. |
| **e2e** | The whole app driven like a user, through the browser (Playwright) or the full CLI. | **Only external services.** Everything inside the system boundary is real. | Few, reserved for critical journeys. Governed by the traceability matrix, not by code coverage. Notoriously flaky — minimize. |

- **The shape differs by stack, the vocabulary does not.** For Troth/Ingle (TS + Vitest) the **testing trophy** maps
  cleanly: a static-analysis base (TypeScript + ESLint), then unit, a *wide* integration/functional band, then a thin
  e2e cap — integration "strike[s] a great balance on the trade-offs between confidence and speed/expense" [Dodds,
  Score 7.20, Level 6]. For Reveal (Python + pytest) the **classic pyramid** maps cleanly: many unit, fewer
  integration, "very few" e2e [Vocke, Score 8.45, Level 5]. The two models disagree only on the *width of the middle*;
  they agree the *top stays thin*. **Evidence-backed.**
- **Boundary condition — the taxonomy is a vocabulary, not a quota.** Vocke's own caveat governs: keep a higher-level
  test "if a higher-level test gives you more confidence," even if it duplicates a lower one. Do not push tests down the
  stack dogmatically when the higher test buys real confidence. **Evidence-backed** [Vocke, Score 8.45, Level 5].

## The sufficiency methodology — four gates plus one guardrail

This replaces "what's our coverage percentage?" with four gates and a guardrail. The tier taxonomy above defines the
*units* you measure in; the four parts below define the *gates* (effectiveness, risk allocation, flow completeness,
coverage-as-advisory); the guardrail is what makes AI-assisted testing safe. Each carries an explicit boundary
condition — the place where it is *not* worth its cost.

### 1. Effectiveness gate — mutation score on the code that matters

For the modules that matter most — financial domain logic, transaction/categorization code, anything on a critical or
high-churn path — the sufficiency gate is **mutation score**, not coverage. A mutation tool makes thousands of tiny
deliberate changes ("mutants") — flips a `+` to a `-`, a `>` to a `>=` — and reruns the suite; a mutant the tests
*notice* (a test fails) is "killed," one they miss "survives." The mutation score is the percent killed. It directly
measures whether a test would *catch a bug*, which coverage cannot.

- **Why it works:** it surfaces the weak tests coverage hides — and, critically for this shop, it catches a test that
  *passes without checking anything*, which is precisely the AI's failure mode (see the guardrail). A 34M-user fintech
  adopted it specifically to "encourage engineers to create tests that thoroughly examine system behaviors to reveal
  defects" [PicPay, Score 6.85, Level 4]. **Evidence-backed.**
- **The Level-1 caveat — read this before you worship the number:** mutation score is *itself* an imperfect proxy.
  Controlling for suite size, "all correlations between mutation scores and real fault detection are weak," yet reaching
  a high score still beats an equally-sized random suite, because only ~1% of mutants behave like a real fault
  [Papadakis, Score 9.25, Level 1]. So treat a high score as **useful guidance to strengthen a critical module, not a
  certificate of correctness.** Read it "as a trend over time and across granularity levels," alongside coverage,
  complexity, and criticality — never as one absolute number [van Heijningen, Score 8.55, Level 3]. **Evidence-backed.**
- **Where it is worth the cost vs. where it isn't.** Worth it: a *small set* of critical, high-churn modules — the
  fintech ran it on 14 of 100+ services before widening, and "the value provided by mutation testing must be more than
  the cost of its integration and maintenance" [van Heijningen, Score 8.55, Level 3; PicPay, Score 6.85, Level 4]. Not
  worth it: (1) the whole codebase at once — runs are "an order of magnitude" slower, 20–40+ min common [Kubowicz,
  Score 6.55, Level 7]; (2) when the tooling is unreliable for your language — in Python, two of five tools failed to
  run and "over half of the mutants generated by Poodle and Cosmic Ray were deemed incompetent," so **tool validation
  is a first-order cost** [Diallo, Score 7.45, Level 3]; (3) relying on incremental modes for speed — PIT's own docs
  warn they are "currently unproven" with "a degree of potential error" [Pitest, Score 7.80, Level 4]. The contrarian
  voice rejects gating builds on it as too slow and noisy [Kubowicz, Score 6.55, Level 7]; the resolution is
  *scope-conditional, not yes/no* — gate it on the hotspots, run it nowhere else. **Evidence-backed.**

### 2. Risk allocation — churn × complexity decides where effort goes

Finite test effort goes first to **hotspots**: code that is both complex *and* frequently changed, because that is
where defects concentrate. A `hotspots.sh` (git-log churn × file complexity) names the ~20% that earns the strong gate
— the mutation gate (Part 1) and a row in the matrix (Part 3) apply to *these* modules.

- **Why it works:** *relative* code-churn measures "discriminate between fault and not fault-prone binaries with an
  accuracy of 89.0 percent" (while *absolute* churn does not) [Nagappan & Ball, Score 8.60, Level 2]; the hotspot
  heuristic operationalizes it as "the overlap between complex code that we need to work with often" [Tornhill, Score
  7.15, Level 5]. Fault-proneness weighting beat coverage-only test ordering by 4.63% APFD (p=0.00268) [Mahdieh, Score
  7.85, Level 2]. Meta reframes sufficiency-per-change as a measured cost trade-off — catch >99.9% of regressions while
  running ~1/3 of tests [Meta, Score 7.45, Level 4]. The transferable core for a solo shop is the *cheap heuristic*
  (`git churn × file complexity`), not Meta's ML pipeline. **Evidence-backed.**
- **Boundary condition — the fallback rule, and it is the logged dissent encoded.** Fault-history weighting only pays
  off when the history is *sufficient*: Mahdieh's method *underperformed* on a project with only 26 recorded bugs
  [Mahdieh, Score 7.85, Level 2], and Nagappan & Ball's 89% is on Windows Server 2003, not a six-month-old repo. **On
  young / low-signal modules, churn × complexity will mis-identify the risky 20% precisely when the product is youngest
  and changing fastest** — so there, fall back to *complexity + impact judgment*, not the churn number. This is the
  council's mandatory dissent, carried forward as a rule rather than a footnote: never let the risk model bless leaving
  a real hotspot untested because it had no history yet. **Evidence-backed** (the caveat) / **practitioner folk wisdom**
  (the specific fallback to judgment is the dissent's mitigation, not a measured method).

### 3. Completeness gate — the flow / failure-state traceability matrix

An e2e suite is "complete enough" when a **traceability matrix** — checked into the repo — shows every user flow has
≥1 e2e test *and* every success and failure state of each critical flow is tested somewhere in the stack. The backing
notion is *feature coverage* (does the suite exercise the app's flows?), the meaningful sufficiency criterion for the
top of the stack — "a more meaningful sufficiency criterion for E2E suites than lower-level code-coverage proxies"
[AutoE2E, Score 7.60, Level 3]. CI asserts each matrix row maps to a real test id.

- **Why it works:** line coverage says nothing about whether a user can *complete a flow*; feature coverage does. It
  dovetails with the tier rule that e2e tests are few and reserved for critical journeys [Vocke, Score 8.45, Level 5].
  **Evidence-backed.**
- **Mock only external services in the e2e tier.** Everything inside the system boundary is real; only Plaid, the LLM,
  and email are stubbed. A "failure state" that doesn't cross a system boundary (a validation error, a rejected input)
  belongs in integration/functional/unit — **the matrix requires *one* e2e per flow, not e2e coverage of every state**.
  **Evidence-backed** [AutoE2E, Score 7.60, Level 3; Vocke, Score 8.45, Level 5].
- **Boundary condition — when e2e is overkill.** e2e tests are "notoriously flaky," slow, and should cover "only
  critical user journeys" [Vocke, Score 8.45, Level 5]. Do not write an e2e for a behavior an integration test can
  verify with less brittleness. And treat AutoE2E's headline numbers (79% feature coverage, +558% over baseline) as
  author-reported and unreplicated [AutoE2E, Score 7.60, Level 3]. **Evidence-backed.**

### 4. Coverage as advisory — find under-tested code, never gate on it

Coverage is measured everywhere and used to *flag* under-tested code; it is **never** the pass/fail gate.

- **Why it's advisory, not a target:** "high levels of coverage do not indicate that a test suite is effective… using
  a fixed coverage value as a quality target is unlikely to produce an effective test suite" — across 31,000 generated
  suites on five real Java systems, coverage→effectiveness was only low-to-moderate once suite size was controlled for,
  and stronger criteria (branch, MC/DC) added little over statement coverage [Inozemtseva & Holmes, Score 8.85, Level
  1]. The real-bug counterpoint found a *significant but project-dependent* correlation (r² 0.33–0.59) and called itself
  preliminary [Kochhar, Thung & Lo, Score 8.10, Level 2]. Reconciliation: a relationship exists but is **too weak and
  too project-variable to be the gate.** This is Goodhart's law in one finding — the moment coverage becomes a target,
  it stops measuring quality and starts measuring the agent's ability to write tests that touch lines.
  **Evidence-backed.**
- **The practical rule:** low coverage *flags* an inadequate suite (a real signal); high coverage proves nothing. Keep
  coverage emitting (`text + json-summary` in TS, `pytest-cov` in Python); keep an honest existing floor as a *ratchet*
  (it may never go down) but never as a *goal* (it never has to go up). **Evidence-backed** (the asymmetry) /
  **practitioner folk wisdom** (the ratchet convention).

### 5. The guardrail — the human owns the oracle

Because the AI writes most of the tests, the final component is a guardrail against the AI's specific, dangerous
failure mode. A **test oracle** is the part of a test that decides pass/fail — the assertion. LLM-based generation "is
prone on generating oracles that capture the actual program behaviour rather than the expected one," with overall
accuracy *under 50%* on oracle tasks, dropping further on buggy code [Konstantinou, Score 8.50, Level 2]. In plain
terms: an AI told to test a buggy function will assert the buggy output, and that test passes forever. **A passing
AI-generated suite is not evidence of correctness — only the human knows what the code was supposed to do.**

- **The two rules on hotspot / financial tests:** (1) the human verifies the *expected* value of every assertion — pin
  an exact value or a hand-verified golden constant, never a bare relational check the agent can satisfy by breaking
  both sides (this is the exact mitigation `DATA_CONSISTENCY.md` mandates for cross-surface assertions); (2) pay extra
  attention to branch / failure-state assertions, because even the best LLM test generators hit 70.2% statement but
  only **52.8% branch** coverage — failure logic is where AI tests are thinnest [Schäfer, Score 8.65, Level 2].
  **Evidence-backed.**
- **Mutation score is the cross-check.** Part 1 is what catches a test that passes without checking anything — exactly
  the AI's failure mode — which is why the effectiveness gate and the oracle guardrail are two halves of one defense.
  **Evidence-backed** [Papadakis, Score 9.25, Level 1; Konstantinou, Score 8.50, Level 2].
- **Boundary condition:** the guardrail is *mandatory* on hotspot/financial modules; for low-risk, low-churn code the
  cost of human oracle-verification may exceed its value, and an AI-generated characterization test that merely pins
  current behavior is an acceptable regression guard there. **Evidence-backed** [Konstantinou, Score 8.50, Level 2].

## Required artifact — the flow / failure-state traceability matrix

Every product built to this standard MUST ship this matrix as a checked-in design artifact, one row per user flow.
It is the e2e completeness contract: for each flow, where its happy path is proven, where each failure state is
proven, and which tier owns each. CI reads the `e2e test id` column and fails the build if a row points at a test that
does not exist. The table below is the canonical worked example — Troth's import pipeline, the flow `DATA_PIPELINES.md`
exists to protect — to copy.

| Flow | Success state — proven by | Failure state(s) — proven by | e2e test id (≥1, browser) | Mock boundary |
| --- | --- | --- | --- | --- |
| CSV upload → staged | Functional: route stages N rows, returns `{importId, runId, staged}` | Malformed row kept `status='error'` (integration); oversized file rejected (functional) | `e2e/import-csv-happy` | File system only |
| staged → promoted (atomic) | Functional: `promote_import` writes all rows in one txn | `NULL occurred_on` → whole txn rolls back, `txnCount===0` (integration, `import-pipeline.test.ts`) | covered by `e2e/import-csv-happy` end-state assert | None (real local DB) |
| promoted → enriched (categorize) | Integration: rules + LLM categorize; re-run filters already-categorized | LLM error → `fail_pipeline_run`, backoff, promoted rows untouched (integration) | `e2e/import-csv-categorized` | LLM service mocked |
| enriched → complete | Integration: allocations + profile inferred (best-effort) | Sub-step throw is swallowed, never blocks `complete` (integration) | covered by `e2e/import-csv-categorized` | LLM service mocked |
| worker crash mid-run | Integration: reclaim 4×, `claims===4`, `failures===0`, resumes at stage | n/a (crash is not a failure — `DATA_PIPELINES.md` invariant) | — (no system boundary crossed; integration owns it) | None (real local DB) |

Five rows: four stage transitions plus the crash-recovery invariant. Note that the crash-recovery row has **no e2e
test** and that is correct — it crosses no browser/system boundary, so the matrix routes it to integration per the
Part 3 boundary condition. The matrix's job is to make the *gaps* visible: a flow with an empty "failure state" column
is an honest TODO, not a hidden one.

## Enforcement model — per-project gates + fitness functions, one thin shared doc

The enforcement model is the brainstorm's chosen architecture: **per-project CI gates, architecture fitness functions
as the blocking enforcement, and a thin shared convention (this doc) — explicitly NO heavy shared test package.** It is
the lowest-maintenance design that still gates effectiveness where it matters, and it reuses what Troth already ships.

- **Per-project CI gates.** Each repo owns its own gate built from the same five components, run via `drone exec <proj>
  -- …`, producing a pass/fail a human or agent reads from a log. There is **no shared code** — the only shared artifact
  is this document, exactly as `DATA_CONSISTENCY.md` and `DATA_PIPELINES.md` already coordinate the other two standards.
  This respects three products at three maturity levels and keeps a change in one repo from breaking another.
  **Practitioner folk wisdom** (the doc-only coordination is a proven Stillpoint pattern, not a measured finding).
- **Architecture fitness functions are the blocking enforcement.** A fitness function is an automated test of an
  *architectural rule* that fails the build when broken — and it is the single most agent-legible mechanism we have,
  because "an agent that sees a build failure will try to fix it, while an agent that reads 'please don't…' might still"
  cross the line (the consistency research's load-bearing finding, carried over). Promote each product's existing
  fitness tests (Troth's `tests/unit/fitness.test.ts` filesystem-walk) to a **blocking** gate, and add two
  testing-specific fitness rules: (a) every matrix row maps to a real e2e test id; (b) every hotspot module has a
  mutation gate configured. Fitness functions verify *structure*, not *behavior* — they cannot tell you a calc is
  *correct*, only that it is wired right and gated — which is exactly why they sit alongside the mutation gate (behavior)
  and the oracle guardrail (correctness), not instead of them. **Evidence-backed** (agent-legibility, cross-cited from
  the consistency corpus) / **practitioner folk wisdom** (the two new testing-specific rules).
- **No shared test package — explicitly rejected (the brainstorm's Option B).** A `@stillpoint/test-kit` + a pytest
  plugin would be the strongest cross-project consistency, but for a solo dev it is premature abstraction before the
  convention has stabilized once, two release pipelines to maintain (no code shared between TS and Python anyway), and a
  coupling trap where one harness bug breaks all consumers — the same reason the consistency council killed the
  `@stillpoint/ledger` SDK. The convention travels as *prose + build-failures*, not as a dependency. **Practitioner folk
  wisdom** (a cost/coupling judgment for this scale, not a measured finding).

## Per-stack implementation notes

The five components are identical across both stacks; the tooling differs by runtime. Coverage is **advisory on both**.

### TypeScript — Troth and Ingle

- **Tiers:** the trophy — TypeScript + ESLint as the static base, then Vitest for unit/component/integration/functional,
  Playwright for the e2e rows of the matrix.
- **Effectiveness gate:** **StrykerJS** for mutation on the hotspot set only (Troth: `engine/*.ts`, `money.ts`,
  `categorizer.ts`, the import-pipeline RPC path; Ingle: the list/meal-plan core). Set a mutation-score floor read as a
  *trend*, not a single absolute. StrykerJS on TS is mature — no tool-validation gate needed (contrast Python below).
- **Risk allocation:** a `scripts/hotspots.sh` (git-log churn × file complexity) emits the risk list; on modules too
  young for churn signal, fall back to complexity + impact judgment (Part 2's boundary condition).
- **Completeness gate:** Playwright drives the matrix's e2e rows; a Vitest fitness test asserts every matrix row maps
  to a real Playwright test id.
- **Fitness functions:** promote the existing `tests/unit/fitness.test.ts` filesystem-walk to a **blocking** gate
  (import-boundary / tier-boundary rules — "no page imports a derived-state engine symbol," already a
  `DATA_CONSISTENCY.md` invariant; "edge entrypoints perform zero direct destination writes," already a
  `DATA_PIPELINES.md` invariant). ESLint import-boundary rules are the lighter-weight complement.
- **Coverage:** keep `text + json-summary` emitting but **advisory** — drop Troth's misleading 85%-on-6-files threshold;
  keep Ingle's honest ~24% as a *ratchet floor* (may not drop), not a target (need not rise).

### Python — Reveal

- **Tiers:** the classic pyramid — many pytest unit tests, fewer integration (service layer against a local DB), very
  few e2e (full-CLI / API).
- **Effectiveness gate:** **mutmut** as primary with **cosmic-ray** as fallback, **but validate competency on one
  `worker`/scoring module first** before gating on it — Diallo found two of five Python tools failed to run and >50%
  incompetent mutants, so a noisy gate that erodes trust is the failure mode here [Diallo, Score 7.45, Level 3]. Tool
  validation is a *first-order cost*, not an afterthought. **Evidence-backed.**
- **Risk allocation:** same `git churn × complexity` heuristic (e.g., a small Python/`radon` script); same young-module
  fallback to complexity + impact judgment.
- **Completeness gate:** the same matrix, checked in; a pytest test asserts every row maps to a real test id.
- **Fitness functions:** expressed as **pytest collection checks over the module tree** — Python has no ArchUnitTS, so
  a small AST/import-scan test that fails if a route/presentation module imports computation internals directly is the
  equivalent (mirrors `DATA_CONSISTENCY.md`'s Python guidance).
- **Coverage:** `pytest-cov` advisory; keep `fail_under` as a *floor not a goal*.

## Anti-patterns — the failure shapes this standard forecloses

Each of these is a real shape this standard exists to prevent. They are the negative space around the methodology.

- **Chasing 100% line coverage.** A high coverage number "do[es] not indicate that a test suite is effective"
  [Inozemtseva & Holmes, Score 8.85, Level 1]; chasing it spends effort proving lines *ran* on code that doesn't matter
  while the financial hotspot stays under-tested. The fix is the risk allocation gate (Part 2) + mutation on hotspots
  (Part 1), coverage demoted to advisory (Part 4).
- **The test ice-cream cone.** The inverted pyramid — too many slow, flaky e2e tests propping up too few unit tests
  — is the named anti-pattern [Vocke, Score 8.45, Level 5]. e2e stays thin and matrix-governed; confidence lives in the
  wide integration/functional band.
- **e2e for everything.** Writing an e2e for a failure state that crosses no system boundary. The matrix requires *one*
  e2e per flow, not e2e coverage of every state — push boundary-internal failure states down to integration/unit
  (Part 3 boundary condition). **Evidence-backed** [Vocke, Score 8.45, Level 5].
- **Trusting AI-generated relational/oracle assertions.** An agent told to assert two values agree, or to test a
  function, will a meaningful share of the time produce a test that passes while the code is wrong — it captures
  *actual* not *expected* behavior, accuracy under 50% [Konstantinou, Score 8.50, Level 2]. The fix is the human-owned
  oracle (Part 5) + mutation score as cross-check (Part 1). **Evidence-backed.**
- **Mutation-testing everything (cost) vs. nothing (no effectiveness signal).** Both extremes are wrong. Running it
  whole-codebase is an order of magnitude slower and produces noise [Kubowicz, Score 6.55, Level 7; Diallo, Score 7.45,
  Level 3]; running it nowhere leaves the financial hotspots with no effectiveness gate, exactly where the asymmetric
  product risk is. The resolution is scope-conditional: gate it on the churn × complexity hotspots, run it nowhere else.
  **Evidence-backed.**
- **Faking rigor with a high gate on a hand-picked subset.** Gating 85% on six files (or 85% on a whole `src/**` that
  is mostly boilerplate) looks rigorous and proves nothing — it is the coverage-as-target anti-pattern wearing a
  per-file disguise. Honest gates: advisory coverage everywhere, mutation on hotspots, matrix on flows.
  **Evidence-backed** [Inozemtseva & Holmes, Score 8.85, Level 1].

## Relationship to other docs

This document owns the **test-sufficiency methodology** — when a codebase is tested enough, and how to gate it. Its two
sibling standards overlap at the edges, and the boundaries are clean:

- **`DATA_CONSISTENCY.md`** (sibling, authoritative) owns the *consistency property*. Golden fixtures, property-based
  oracles, and architecture fitness functions appear in both documents because they serve consistency *and* general
  correctness. Where they overlap, **that document is the authority on *what consistency property the test must assert*
  (cross-surface agreement, with one side golden-pinned); this document is the authority on *how to decide a module is
  tested enough and how to write an effective test* (tier, mutation gate, oracle review).** The AI-oracle guardrail
  (Part 5) is the shared seam: that doc mandates the golden-pinned assertion for cross-surface checks; this doc
  generalizes the human-owns-the-oracle rule to all hotspot/financial tests.
- **`DATA_PIPELINES.md`** (sibling, authoritative) owns the *pipeline guarantees*. The integration tests it cites
  — atomicity, idempotency, SKIP-LOCKED concurrency, crash-recovery, backoff/quarantine, cross-household isolation —
  are the proof harness for that pattern, and that document's "Related standards" section already names this one as the
  authority on *how to structure* them. The boundary: **that document is the authority on *what pipeline guarantee the
  test must assert*; this document is the authority on *which tier the test belongs in and whether the flow is tested
  enough* (the import-pipeline matrix above is the worked example).**

The shared color-science standard (`docs/principles/2026-05-26-color-science-stillpoint.md`) is the format template all
three standards mirror; it is otherwise unrelated in subject.

## Methodology note

This standard is the codification of the 2026-06-08 deep-research project (methodology `deep-research v0.1.0`),
synthesized with a 5-persona Collective brainstorm (2026-06-09) whose recommendation and logged dissent it encodes.
Read this section if you are the skeptic deciding how much to trust the rules above.

- **Corpus:** 19 unique source cards retained after evaluation, scored on a 10-dimension credibility rubric (Authority,
  Evidence Quality, Currency, Intent, Bias, Logic, Corroboration, Intellectual Honesty, Specificity, Relevance) and
  classified on a 9-level evidence hierarchy.
- **Evidence strength — stated plainly, and this is the load-bearing difference from the sister standards:** the corpus
  contains **two Level-1 sources** (large-scale controlled empirical studies at ICSE — Inozemtseva & Holmes on
  coverage, Papadakis on mutation score) and **five Level-2 sources** (peer-reviewed controlled studies). So this is
  *not* a "no primary evidence" corpus: the two anchoring findings rest on measured outcomes, not expert opinion. The
  rest descend through Level 3 (observational), Level 4 (industry / professional-body), Level 5–6 (practitioner case
  study / expert essay), and Level 7 (the single contrarian voice, Kubowicz). Throughout, claims are labeled
  *evidence-backed* (cited, with score and level) vs *practitioner folk wisdom* (a convention adopted without a measured
  citation).
- **Verification:** all 19 source cards were sampled for citation verification (100%); 17 verified outright + 2
  corrected-before-tally (a dropped word in the PicPay quote; a repointed Tornhill URL), **0% unresolved failure rate**
  (≤5% gate band). No verbatim quote in this document is invented — every quoted string traces to a verified source
  card.
- **The council and the logged dissent.** The enforcement architecture (per-project gates + fitness-functions-as-
  enforcement + a thin shared doc, rejecting a shared package) is the Recommender's synthesis of a five-persona review.
  The **mandatory dissent** (the User Advocate) is encoded directly into the methodology, not buried: shipping
  "accept low coverage everywhere outside the risky 20%" *as the standard* is dangerous on young, fast-changing
  codebases where churn × complexity has weak signal [Mahdieh, Score 7.85, Level 2, underperformed at 26 recorded bugs].
  The mitigation — adopt the risk-prioritization *method* but never its accept-low-coverage *headline*, and fall back to
  complexity + impact judgment on young modules — is Part 2's boundary condition.
- **Known limitations, carried forward honestly:** (1) **stack-transfer gap** — no source measures sufficiency on a
  *solo, AI-assisted* TS/Vitest or Python/pytest codebase specifically; the evidence is JVM/Kotlin, C++ safety-critical,
  Java benchmarks, JS/npm, and fintech microservices, so transfer to this shop is by analogy; (2) **single-source
  themes** — e2e completeness (AutoE2E) and contract-testing scope (Lehvä) each lean on one primary source, less
  triangulated than the coverage/mutation/risk themes; (3) **author-reported headline figures** — AutoE2E's 79%/+558%
  and Meta's >99.9% carry positive-result bias and are unreplicated; (4) the per-stack Python notes go beyond the corpus
  in places and are marked as prediction/folk wisdom where they do.

The one thing to remember: *Tested-enough is not a coverage percentage. It is — every flow and failure state has a
test, the critical and high-churn code has tests proven effective by mutation score, and a human, not the AI, has
confirmed the assertions encode what the code is supposed to do.* Sufficiency is a methodology you gate where the risk
is — not a number you chase everywhere.
