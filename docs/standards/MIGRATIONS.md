---
date: 2026-06-25
title: Portfolio database migration standard — Stillpoint Labs
audience: Noah, future contractors and AI agents building Troth, Ingle, Reveal
status: Draft (v0.1, for review)
---

# Portfolio database migration standard — Stillpoint Labs

> **Status: Draft (v0.1, for review)**

This is the single canonical migration standard for every Stillpoint product. It unifies the rules
from the stillpoint consolidation ADRs (`stillpoint/DECISIONS.md`, ADR-001 through ADR-007) and the
ingle destructive-DDL gate (`ingle/docs/MIGRATIONS.md`). Where any product-level doc conflicts with
this standard, this standard wins and the product doc must be updated.

The problem it exists to prevent is concrete: schema drift and unreproducible production schemas,
caused by hand-applied changes and inconsistent CI enforcement. The rules below make every schema
change version-controlled, CI-gated, and forward-only before it touches production.

---

## Quick reference — the rules in one screen

- **One Supabase project.** All three products share `jmgjznimsrlcgyevefqm`; schema-per-app isolates
  tenant data.
- **Supabase CLI is the sole migration runner.** No hand-apply via MCP, Dashboard, or psql (ADR-004).
- **Forward-only.** No down-migrations. The data tier never claims auto-rollback.
- **Filename convention is the schema ledger.** `<14-digit-ts>_<schema>_<desc>.sql` — CI lint enforces
  it on every PR.
- **No cross-schema DDL or FKs.** Tenant files must not touch `stillpoint.*`. Service-layer integrity
  only (ADR-003).
- **Destructive DDL requires an ack marker.** `DROP`/`TRUNCATE` without `-- expand-contract-ack:
  <reason>` fails CI in every repo.
- **Shared-schema DDL requires a human gate.** `stillpoint` base-table changes must be additive,
  nullable, lock-timeout-guarded, and approved by a human before apply.
- **Baseline, not repair.** Pre-consolidation history is snapshotted; the ledger starts clean from
  the baseline date (ADR-005).

---

## Principles

### One Supabase project + schema-per-app + shared identity

All three products run in a single Supabase project. Schema isolation is by convention, not by
separate projects:

| Schema       | Owner                                                          |
|--------------|----------------------------------------------------------------|
| `reveal`     | Reveal photo app                                               |
| `troth`      | Troth household-finance app                                    |
| `ingle`      | Ingle grocery app                                              |
| `stillpoint` | Shared identity — `identities`, `households` base tables       |
| `xschema`    | Cross-schema grants or views spanning multiple schemas         |
| `grants`     | Role grants only (no DDL)                                      |

The global ledger (`supabase_migrations.schema_migrations`) records all migrations across all schemas
in a single ordered list. This is a feature — it gives deterministic cross-schema apply order — and a
risk: a failed migration in any schema blocks all schemas until repaired. The naming convention and the
human gate on `stillpoint` DDL are the mitigations (ADR-001).

### Single global ledger via Supabase CLI (ADR-001)

The Supabase CLI (`supabase migration` / `supabase db push`) is the sole migration runner. Two
alternatives were evaluated and rejected:

- **Atlas versioned** — disqualified because Atlas Pro is required for RLS-as-code; the free tier is
  blind to RLS drift, which is a structural hole in the Supabase security model.
- **dbmate per-schema + Atlas-as-gate** — disqualified because dbmate's ledger placement is unstable
  across major versions, and the approach adds three binaries (worst on "fewest moving parts").

### Forward-only / expand-contract; no down-migrations

Migrations are applied forward-only. There are no down-migrations and no auto-rollback at the data
tier. The data tier's rollback story is:

1. **Application code rolls back** (a revert deploy) while the schema stays forward.
2. **Additive changes are always safe to leave in place.** A nullable column in a table the old code
   ignores causes no harm.
3. **Destructive changes use expand-contract** (see below) to sequence the risk across deploys, never
   inside a single migration.

This is not a limitation — it is the correct mental model. Database rollback is dangerous because it
destroys data written by the code that ran against the new schema. Deploy the old code; let the schema
stay new; fix forward.

### Baseline-not-repair (ADR-005)

The two projects (stillpoint and ingle) had different version schemes and accumulated hand-applied
drift. Replaying legacy migrations against a fresh DB would likely fail. The chosen approach:

1. `supabase db dump --linked --schema <schema>` — capture current state as SQL.
2. `supabase migration repair --linked --status applied <version>` — register the snapshot in the
   ledger as "already applied" without running it against the live DB.
3. Commit baseline files under `supabase/baselines/` for auditability.

Pre-baseline history is preserved only in `supabase/baselines/` — it is not replayable. New migrations
applied after the baseline are the forward-only history.

---

## Naming convention and schema-scoping

### Filename format

```
<14-digit-timestamp>_<schema>_<description>.sql
```

Examples:

```
20260623000001_stillpoint_v_identities_v1.sql
20260623120000_reveal_add_photos_note.sql
20260701090000_troth_add_payment_method.sql
20260715083000_ingle_drop_legacy_brand_id.sql
```

Rules:

- **14-digit timestamp** — `YYYYMMDDHHmmss` in UTC. Generate with `date -u +%Y%m%d%H%M%S`.
- **Schema tag** — must be one of the allowlisted values (table above).
- **Description** — kebab-case or snake_case, no spaces.
- **Extension** — `.sql` only.

The CI lint enforces the filename pattern on every PR and push (Check A). A file that does not match
the pattern fails CI immediately — it cannot be applied.

### Schema allowlist

The allowlist is: `reveal`, `troth`, `ingle`, `stillpoint`, `xschema`, `grants`.

Any migration file whose schema tag is not in this list fails CI (Check A). Adding a new schema tag
requires updating both `scripts/lint-migrations.sh` and this document.

### App/tenant tag vs. target Postgres schema

The filename's schema field is an **app/tenant tag** — a routing label, not a literal `search_path`
target. For most apps the tag equals the target Postgres schema 1:1, but the two are distinct concepts
and MUST NOT be conflated. The one divergence today is **reveal: the `reveal` tag maps to the `public`
schema** (Reveal still owns `public`; the `public`→`reveal` schema move is parked — see the conformance
ledger below).

| App/tenant tag | Target Postgres schema (today)            |
|----------------|-------------------------------------------|
| `reveal`       | `public` (tag → `public`; move to `reveal` parked) |
| `troth`        | `troth`                                   |
| `ingle`        | `ingle` (on the separate ingle project)   |
| `stillpoint`   | `stillpoint`                              |
| `xschema`      | (spans multiple schemas)                  |
| `grants`       | (role grants only; no schema DDL)         |

When the reveal `public`→`reveal` move lands, this mapping — not the tag — is what changes; the tag
stays `reveal`.

---

## Load-bearing rules

### Rule 1 — Pipeline-only applies; hand-apply prohibited (ADR-004)

All schema changes are applied exclusively via `supabase db push` in the CI/CD pipeline. Applying
by hand — via MCP tools, the Supabase Dashboard SQL editor, psql, or any other mechanism — is
**prohibited**. This includes "quick fixes." If a fix is needed in production, it ships as a migration
through the pipeline.

The one exception is the snapshot-as-baseline bootstrap step (ADR-005), which is a one-time
human-gated operation, not a recurring pattern.

> **Note:** the production applier MUST pin the Supabase CLI to a fixed version (never `latest`) so
> apply behaviour is reproducible across runs and a silent CLI bump can never change how a migration is
> applied.

**Why:** hand-applied changes produce migration-history drift and an unreproducible production schema.
The current stillpoint state was caused by exactly this pattern. Every schema change must acquire a
version-controlled history, a CI lint pass, and a dry-run gate before touching production.

### Rule 2 — No cross-schema DDL or foreign keys (ADR-003)

Tenant migrations (`reveal`, `troth`, `ingle`) MUST NOT contain:

- `ALTER TABLE stillpoint.*`
- `REFERENCES stillpoint.*`

Cross-schema referential integrity is enforced at the **service layer**: each app must write and test
its own consistency checks (e.g. verify that `auth_user_id` in a tenant record corresponds to a live
`stillpoint.identities` row before committing the record).

The CI lint enforces this as a hard failure (Check B). A tenant migration that touches the
`stillpoint` schema fails CI immediately.

**Why:** Supabase's type generation already omits cross-schema FKs (issue #3334), signalling that this
pattern is unsupported. DB-level cross-schema coupling would structurally couple the schemas — the
versioned-view contract (Rule 5) provides a cleaner integration surface. The blast-radius reduction
from decoupled schemas outweighs the integrity trade-off at solo-founder scale. The obligation to
write and test service-layer checks is **non-negotiable** — it is part of the schema change acceptance
criteria for every phase.

### Rule 3 — Destructive DDL requires an explicit ack marker

Any migration containing destructive DDL — `DROP TABLE`, `DROP COLUMN`, `DROP SCHEMA`, `DROP VIEW`,
`DROP CONSTRAINT`, or `TRUNCATE` — MUST carry an explicit acknowledgment marker anywhere in the file:

```sql
-- expand-contract-ack: <reason why this drop is safe>
```

One marker per file covers all destructive statements in that file. Be specific about what happened to
the data:

```sql
-- expand-contract-ack: brand_id column replaced by derived join through canonical_item_id;
--   data relocated to items.brand_id before this drop; lossless expand-contract move.
ALTER TABLE store_items DROP COLUMN IF EXISTS brand_id;
```

A migration with destructive DDL and no ack marker fails CI in every product repo. This is the gate
that was shipped first in ingle (`ingle/scripts/lint-migrations.sh`) and is now promoted
portfolio-wide.

**The marker documents the contract step of the expand-contract sequence.** If the drop is truly
destructive with no migration path for the data, the ack reason must say so explicitly — that makes
future audits unambiguous.

### Rule 4 — Shared `stillpoint` schema DDL: additive/nullable + lock timeout + human gate

Every migration tagged `stillpoint` that contains `CREATE TABLE` or `ALTER TABLE` on a **base table**
(not a view) MUST:

1. **Be additive and nullable:** only add columns. No renames, no type changes, no drops in the same
   migration. Columns added to `identities` or `households` must be `DEFAULT NULL` or have a default
   that does not require a table rewrite.
2. **Set `SET lock_timeout = '2s';`** at the top of the migration to prevent long lock waits from
   blocking the entire application.
3. **Be gated by the human prod approval** before it is applied. The CI lint prints a `NOTICE` for
   these files; the pipeline must pause for human sign-off (via GitHub Environment protection rules on
   the `production` environment).

**Why:** the global ledger means a failed shared-schema migration blocks all tenant schemas until
repaired. A shared-schema change has portfolio-wide blast radius. Additive/nullable + lock_timeout
minimise the risk; the human gate ensures a second pair of eyes before the global ledger is advanced.

### Rule 5 — Versioned-view contract on the `stillpoint` schema (ADR-002)

Tenant apps (reveal, troth, ingle) MUST read `stillpoint` data through **versioned views only**:

- `stillpoint.v_identities_v1` — universal identity fields (excludes `employee_role`, which belongs to
  `troth.employee_profiles`)
- `stillpoint.v_households_v1` — universal household fields

Grants on base tables are NOT given to `authenticated` or `service_role` for cross-tenant reads.
Grants are on the views only. New view versions (v2, v3, …) are created alongside the prior version;
old versions are dropped only after all tenants have migrated — expand-contract applies at the view
level too.

**Why:** the view contract absorbs additive base-table changes (a nullable column added behind the
view causes zero change to the tenant read path). It also scopes the blast radius of future
`stillpoint` schema evolution — tenants are insulated from base-table changes until they choose to
migrate their view pinning forward.

### Rule 6 — PostgREST schema exposure is a platform step, NOT a migration

Exposing a schema via PostgREST (`exposed_schemas` in `supabase/config.toml` or the Dashboard) is NOT
done via a migration file. It is a one-time platform configuration step performed in the Supabase
Dashboard by the project owner. Reference: Supabase issue #45904 (stale-cache race → PGRST106 if done
via migration).

---

## Destructive-DDL / expand-contract discipline

The preferred approach for any column, constraint, or table removal is **expand-contract**. The
expand-contract-ack marker documents the "contract" step and confirms the data was safely migrated
before the drop.

### The four-phase soak sequence

The troth identity cutover (ADR-007) is the worked example; apply this sequence for any change that
removes a shared surface:

1. **Expand** — add the replacement (new column, new table, new view version, new RPC). Deploy. Both
   old and new surfaces exist simultaneously.
2. **Migrate reads** — update all application code to read from the new surface. Deploy. No reads
   touch the old surface.
3. **Soak in production** — leave both surfaces live for a defined period (minimum 7 days for shared
   `stillpoint` surfaces; typically 1 deploy cycle for tenant-only surfaces). Monitor logs for any
   residual reads of the old surface.
4. **Contract** — drop the old surface once the soak proves no live path reads it. Add
   `-- expand-contract-ack:` to the migration. Gate on human approval for `stillpoint` DDL.

**Troth identity cutover as the worked example (ADR-007):**

- **Expand (PR-A / PR-B):** `troth.employee_profiles` created, keyed on `auth_user_id`; troth app
  reads migrated onto `stillpoint.v_identities_v1` (self) + new SECURITY DEFINER RPCs (cross-user
  paths). No cross-schema FK, per ADR-003.
- **Soak:** 7-day production soak; fitness test blocks any `authenticated`/browser-client read of
  `stillpoint.identities` (`.from` + `identities!inner` embeds).
- **Contract (PR-C — deferred):** `REVOKE SELECT ON stillpoint.identities FROM authenticated`, then
  `ALTER TABLE stillpoint.identities DROP COLUMN employee_role`. Both statements carry
  `-- expand-contract-ack:` and are gated by human approval.

---

## Dos and Don'ts

| Do | Don't |
|----|-------|
| Name every migration `<14-digit-ts>_<schema>_<desc>.sql` | Rename or reorder existing migration files |
| Apply migrations via `supabase db push` in CI only | Apply via MCP, Dashboard SQL editor, or psql |
| Add `-- expand-contract-ack: <reason>` before any DROP/TRUNCATE | Drop columns or tables without an ack marker |
| Add columns as `DEFAULT NULL` or with a non-rewrite default | Add NOT NULL columns without a default to large tables |
| Set `SET lock_timeout = '2s';` on all `stillpoint` base-table DDL | Omit lock timeout on shared-schema changes |
| Read `stillpoint` data through versioned views (`v_identities_v1`) | Read `stillpoint` base tables from tenant app code |
| Enforce cross-schema integrity in the service layer with tests | Add `REFERENCES stillpoint.*` in tenant migrations |
| Sequence removals as expand → soak → contract across deploys | Rename, drop, or retype columns in a single migration |
| Commit baseline snapshots under `supabase/baselines/` | Rewrite or delete pre-baseline migration files |
| Register the baseline via `migration repair --status applied` | Replay all legacy migrations on a fresh DB |

---

## CI enforcement / fitness functions

Both lint rule-sets MUST run in every product repo's CI on every PR and push to `main`.

### Rule-set A — Filename + schema-scope lint (stillpoint origin)

**Script:** `scripts/lint-migrations.sh <migrations-dir>`

**Checks:**
- **Check A:** every `*.sql` filename matches
  `^[0-9]{14}_(reveal|troth|ingle|stillpoint|xschema|grants)_.+\.sql$`. Violation → immediate CI
  failure.
- **Check B:** for files tagged `reveal`, `troth`, or `ingle`, the body must not contain (case-
  insensitive) `ALTER TABLE stillpoint.` or `REFERENCES stillpoint.`. Violation → immediate CI
  failure.
- **Notice:** files tagged `stillpoint` or `xschema` that contain `CREATE TABLE` or `ALTER TABLE`
  print a human-gate notice (informational, not a failure).

**Currently enforced in:** `stillpoint` (CI: `db-lint.yml` + `db-migrate.yml`).
**Not yet enforced in:** `ingle`, `reveal`, `troth` (consolidation gap — see below).

### Rule-set B — Destructive-DDL gate (ingle origin)

**Script:** `scripts/lint-migrations.sh <migration-files>`

**Checks:**
- Any migration containing `DROP TABLE`, `DROP COLUMN`, `DROP SCHEMA`, `DROP VIEW`, `DROP
  CONSTRAINT`, or `TRUNCATE` without an `-- expand-contract-ack:` marker fails CI.
- Comment lines (starting with `--`) are excluded from matching — the ack marker itself never
  triggers the gate.

**Currently enforced in:** `ingle` (CI: `migrate.yml`).
**Not yet enforced in:** `stillpoint`, `reveal`, `troth` (consolidation gap — see below).

### Consolidation gap — action required

The two rule-sets currently run in different repos and enforce different subsets:

| Repo       | Schema-scope lint (A) | Destructive-DDL gate (B) |
|------------|-----------------------|--------------------------|
| stillpoint | Yes                   | No                        |
| ingle      | No                    | Yes                       |
| reveal     | No                    | No                        |
| troth      | No                    | No                        |

**The gap to close:** both rule-sets should run together in every repo. Until this is done, the
portfolio does not have a uniform migration safety baseline. The recommended path is to consolidate
the two scripts into one combined `lint-migrations.sh` per-repo (or a shared script from a common
location if the repos share tooling). This is the highest-priority follow-up from this standard.

---

## Local verification

Run the lint locally before pushing. The exact invocation differs by rule-set:

**stillpoint (rule-set A — all files in the migrations directory):**

```bash
bash scripts/lint-migrations.sh supabase/migrations
bash scripts/test-lint.sh
```

**ingle (rule-set B — specific files, or all via glob):**

```bash
bash scripts/lint-migrations.sh supabase/migrations/0090_my_new_migration.sql
bash scripts/lint-migrations.sh supabase/migrations/*.sql
```

After consolidation, both rule-sets will run via a single invocation.

---

## Per-app conformance ledger

This table is the live snapshot of how each product currently conforms to this standard. It separates
the filename **app/tenant tag** from the **target Postgres schema** (they diverge for reveal today) and
records ledger ownership, lint coverage, the apply-gate, and the cutover state. The single canonical
ledger repo is **`stillpoint-labs/stillpoint`**.

| App | Target Postgres schema | Ledger ownership | Lint status | Apply-gate | Current cutover state |
|-----|------------------------|------------------|-------------|------------|-----------------------|
| **stillpoint** | `stillpoint` (+ shared identity base tables) | Authoritative single ledger (owns all schemas) | Strict (rule-sets A + B) | `workflow_dispatch` (human-gated apply) | Live — steady state / authoritative |
| **troth** | `troth` | stillpoint (troth migrations frozen) | Governed by the stillpoint ledger lint | `workflow_dispatch` via stillpoint pipeline | In soak → PR-C (contract) ~2026-07-02 |
| **reveal** | `public` (filename tag is `reveal`) | stillpoint (reveal migrations frozen) | Alembic retained test-only (not an applier) | Frozen — no live applies | `public`→`reveal` schema move **PARKED** |
| **ingle** | `ingle` (separate live project) | Separate ingle ledger (own project + CI) | Destructive-DDL gate (rule-set B) | **Auto-apply on push — NON-conformant (ADR-009)** | Fold-in applied; live cutover ~2026-07-28 |

Notes:

- **ingle runs on a separate live Supabase project** (`muyxsjvgybpyaapccmaz`), not the shared
  `jmgjznimsrlcgyevefqm` project, with its own migration ledger and CI. This is the main standing
  divergence from this standard.
- **ingle auto-applies migrations on push**, which is NON-conformant to ADR-009 (pipeline applies must
  be human-gated, not automatic). Reconciliation is tracked as part of the ingle fold-in; the live
  cutover onto the shared project is targeted for ~2026-07-28.
- **reveal's `reveal` tag maps to the `public` schema today.** Its migrations are frozen and Alembic is
  retained for tests only — it does not apply schema changes. The `public`→`reveal` move is parked.
- **troth migrations are frozen**; the stillpoint ledger owns them. Troth is in a production soak ahead
  of the PR-C contract step (the `stillpoint.identities` lockdown described above), targeted ~2026-07-02.

## Relationship to other standards

This standard owns the **schema evolution and migration execution** layer. Sibling standards:

- **DATA_CONSISTENCY.md** — owns how derived values are computed and exposed consistently across
  surfaces. The service-layer integrity checks required by Rule 2 (no cross-schema FKs) are
  application-code patterns that DATA_CONSISTENCY.md governs.
- **TESTING.md** — owns the testing methodology layer. Service-layer integrity checks (ADR-003)
  must exist as tests in the affected app's test suite; TESTING.md specifies how to write them.
- **DATA_PIPELINES.md** — owns import/ETL and raw-fact ingestion. Migrations that create or alter
  pipeline-target tables must coordinate with the pipeline owner.

---

## ADR provenance

Every rule in this document traces to a ratified ADR in `stillpoint/DECISIONS.md`:

| Rule | ADR |
|------|-----|
| Supabase CLI sole runner; global ledger; filename-convention schema-scoping | ADR-001 |
| Versioned-view contract on `stillpoint` | ADR-002 |
| No cross-schema FKs; service-layer integrity | ADR-003 |
| Pipeline-only applies; hand-apply prohibited | ADR-004 |
| Snapshot-as-baseline; no history repair | ADR-005 |
| Phase 1 identity-surface lockdown + reveal FK strategy | ADR-006 |
| troth identity migration; expand-contract worked example | ADR-007 |
| Destructive-DDL gate; expand-contract-ack marker | ingle/docs/MIGRATIONS.md |
