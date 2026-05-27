# Handoff — no current PROJECT_PLAN, candidate initiatives to pick from

**Created:** 2026-05-27
**For:** borg + Claude Code CLI continuation
**Parent plan:** none

## Current state

The parent marketing site is on `main` at `926449f` ("feat(devcontainer): scaffold dev environment
for drone up (#1)") — clean working tree relative to its committed state, with four untracked items
awaiting individual triage (`.borg-project`, `CLAUDE.md`, `docs/principles/2026-05-26-color-science-stillpoint.md`,
and the `docs/` directory itself). Site is two committed features deep: the initial scaffold + brand
tokens + v1 marketing pages, and the devcontainer scaffolding. No `PROJECT_PLAN.md`. The May 26
session synthesized two principles docs — the Stillpoint operating principles (already at
`docs/principles/2026-05-25-stillpoint-operating-principles.md`, tracked) and a shared color-science
reference (`docs/principles/2026-05-26-color-science-stillpoint.md`, untracked) that both the Ingle
and Troth design-system repos cite as their canonical reference.

## What's blocked

Borg's `borg-next` returns nothing actionable until a `PROJECT_PLAN.md` exists, but the right scope
isn't obvious from the current code state alone — the site is a small marketing site without a clear
next milestone in the repo itself. The decision has to come from Noah; this handoff exists to make
the decision cheap by listing the candidate plans inline rather than forcing a re-investigation
each time the project gets touched.

## Next action

Noah picks one of the following candidate plans, or names a different one. Whichever wins, draft
`PROJECT_PLAN.md` with an objective and 3-6 verifiable acceptance criteria and mark "proposed,
awaiting Noah confirmation."

### Candidate A — Color-science doc promotion to public

The shared color-science reference is already cited by Ingle and Troth's design systems as
canonical, but it lives in `docs/principles/` (internal/working). Promote it to a public-facing
page on the marketing site so external consumers (designers evaluating Stillpoint products,
contributors, future hires) can read the same source-of-truth the products cite.

Plausible acceptance criteria:

- Citation URLs in `docs/principles/2026-05-26-color-science-stillpoint.md` re-fetched live and
  verified (current text was pulled from prior knowledge, not re-grounded).
- Editorial pass: tighten the doc for public consumption — currently written in internal voice.
- Astro page added at `/principles/color-science` (or similar) rendering the markdown.
- Cross-links updated in `~/dev/ingle/docs/design/...` and `~/dev/troth/docs/design/...` to point
  at the public URL instead of the internal `docs/principles/` path.
- Production deploy verified at `https://stillpointlabs.dev/principles/color-science`.

### Candidate B — Operating-principles doc disposition

The 7,143-word Stillpoint operating-principles doc at
`docs/principles/2026-05-25-stillpoint-operating-principles.md` is tracked and substantial. The
May 26 checkpoint flagged it as "synthesized from internal raw material and may contain phrasing
not yet meant for public consumption" — so it's currently neither truly internal (it's in a public
repo) nor truly published (no marketing-site page). Pick a disposition.

Plausible acceptance criteria:

- Decision recorded: public page, internal-only (move to private repo or gitignore), or hybrid
  (publish a distilled version, keep the long-form internal).
- If public: editorial pass on the 10 sections, page added under `/principles/operating`, deploy
  verified.
- If internal: move out of `stillpointlabs-site` to wherever internal docs actually live.
- If hybrid: distilled version drafted (~1,500 words), reviewed against the long-form, published;
  long-form moved to internal-only location with the public page citing it as "internal source."

### Candidate C — Parent-site visual restraint pass

`CLAUDE.md` (untracked) codifies the working principle that the parent site's identity is
"restrained" — it defers to Reveal, Ingle, and Troth for warmth and personality. The current v1
marketing pages were built in the initial scaffold commit before this principle was made explicit.
A pass to verify the live site actually embodies that principle would be a small, scoped initiative
that ends in observable changes.

Plausible acceptance criteria:

- `CLAUDE.md` committed (codifies the principle).
- Each v1 marketing page audited against the restraint principle — list of changes documented.
- Changes made: typography, spacing, color usage, motion all reviewed for "less is more."
- Each product-card accent verified against the source palette in `src/styles/global.css` (Reveal
  uses `--golden-hour`, Ingle uses `--ember`, Troth uses `--heartwood`).
- Visual diff (or screenshots) captured before/after and reviewed.

### Other candidates worth flagging

- **Cloudflare Pages deploy automation** — the README references the production domain and the
  preview URL, but the actual deploy mechanism isn't visible in the repo. If it's manual today,
  formalizing it (Pages Git integration + preview-on-PR) is a small, scoped initiative.
- **Product launch tile for Reveal / Ingle / Troth** — the parent site is supposed to link to its
  products, but with the products still pre-launch the cards may be placeholders. As any of the
  three launches publicly, the corresponding card needs real copy + a real link.

## Open questions

- Does the color-science doc need re-grounded citations before any public promotion, or is the
  current text "good enough for internal" and only needs verification at promotion time?
- Should the operating-principles doc live in `stillpointlabs-site` at all? It's about Stillpoint
  Labs as a company, not about the marketing site specifically — a separate `stillpointlabs-ops`
  or similar repo might be a better home.
- Is the `.devcontainer/` now-tracked state correct? It arrived via `feat/devcontainer` PR #1 on
  origin/main and the local untracked copy was byte-identical — but worth confirming the PR's
  merge was intentional and not a duplicate of the local scaffold.
- Are there other Stillpoint products beyond Reveal / Ingle / Troth that need marketing-site
  representation? `CLAUDE.md` enumerates three; if a fourth is on the roadmap, the parent-site
  layout may need rework before launch.
