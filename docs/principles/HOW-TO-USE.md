# How to Use the Stillpoint Operating Principles Doc

*A short companion. Read this before referencing the principles doc for the first time. Re-read whenever a new project joins the portfolio or whenever the team feels the principles are drifting.*

## What This Is

The principles doc (`2026-05-25-stillpoint-operating-principles.md`) is the canonical operating manual for everything Stillpoint Labs builds. The one-page summary (`2026-05-25-stillpoint-operating-principles-summary.md`) is the wall poster. This file is the meta-guide.

The principles are not the brand guide, the design system, or the engineering standard. Those documents live in each product's own repo and cover the surface details (colors, typography, framework choices, repo conventions). The principles doc covers the *posture* behind those documents: what the products are for, who they serve, what we never do, how we hold the line when product pressure pushes the other way.

## When to Reference It

**Before a new product brief is written.** Read the principles doc from the top before drafting any product brief, positioning document, or strategic memo. The thesis (§1) and the moat thesis (§10) bracket the product's purpose. The archetype framework (§5) and the target-user section (§4) bracket who the product is for. If a brief is being written without consulting this doc first, the brief is going to drift.

**Before any copy ships externally.** The brand voice section (§2) and the product voice section (§3) are the gate. Read them before drafting marketing copy, onboarding strings, error messages, notifications, or anything else a user will read. The banned-word list in the summary is the cheap check; the do/don't lists in the principles doc are the real audit.

**Before adding a feature that involves measurement, tracking, alerts, or scoring.** Read the mental-load principle (§7) and the anti-surveillance principle (§8). Most feature regressions on the Stillpoint posture come from this category. The do/don't lists are explicit because the failure mode is sneaky: a streak feature does not announce itself as a streak feature on the spec doc.

**Before onboarding a new contractor, agent, or collaborator.** Hand them the one-page summary first. Hand them the full principles doc when they are ready to do work that touches product surface. Their first piece of output gets read against the do/don't lists; gaps go into the feedback round, not into shipped product.

**When a feature is being argued about.** The principles doc settles arguments that would otherwise relitigate every sprint. If the question is *"should we add a daily check-in nudge,"* the question is answered by §7 (mental load) and §8 (anti-surveillance) without rerunning the debate.

## How to Use the Do/Don't Lists

Every section ends with two lists. They are not aspirational; they are operational. Use them as checklists.

**The do list is the affirmative behavior the product surface should demonstrate.** A piece of copy, a feature, a flow, or a notification gets checked against the do list to see whether it actually does the thing the principle requires. If it does not, it is not done, even if the spec is complete.

**The don't list is the failure modes the principle is meant to prevent.** A piece of copy, a feature, a flow, or a notification gets checked against the don't list to see whether it has slipped into any of the patterns the principle bans. The don't list is more important than the do list, because the do list maps to the spec; the don't list catches the slip the spec missed.

**Use the lists as a checklist on shipped work.** Print them out, paste them into the PR description, or post them in the team channel. Walk the surface against the list before merge.

## The Quarterly Audit

Once a quarter, every shipped product gets walked against this document. The audit is the moat-protection exercise (§10): the principles only produce the moat if they are executed consistently, and consistency only happens if it is checked.

**Mechanics of the audit:**

1. Pick a shipped product. Pick a sample of surfaces: the landing page, the onboarding flow, the most-used screen, the most recent notification copy, the most recent feature.
2. Walk each surface against §2 (brand voice), §3 (product voice), §4 (target user), §6 (personalization), §7 (mental load), §8 (anti-surveillance). The other sections are also relevant but tend to be set at the strategy level rather than the surface level.
3. Note every gap. The gap goes into a research-or-design ticket; the audit does not have to fix the gap immediately.
4. Aggregate the gaps across the portfolio. If three products show the same gap, the principles doc may need a clarifying revision rather than three independent fixes.

**Audit cadence:** the first week of each quarter (January, April, July, October). The audit fits inside a single working day per product if the principles doc has been actively referenced during the quarter. If it has not, the audit takes longer because the gaps have piled up.

## How to Update This Document

The principles doc will be wrong about specific things. The research it cites will be superseded. The products will surface findings that change the framing. When that happens, the document gets updated. The mechanics:

**Date the change.** The current document is `2026-05-25-stillpoint-operating-principles.md`. The next version supersedes this one and gets the next date. Older versions stay in the repo.

**Record the rationale.** Every change to a principle gets a short note explaining what evidence moved the principle. The note can live in the commit message or in a `CHANGES.md` file in this directory. The history matters; future product teams will need to see what changed and why.

**Get a second read.** The principles doc is high-stakes; a one-person edit risks introducing a regression that propagates across the portfolio. Even an obvious change benefits from a second read.

**Update the summary together with the full doc.** The one-page summary is derived from the full doc; if the full doc changes, the summary changes in the same commit. Otherwise the summary becomes stale infrastructure.

## When to Revisit

**When a new product joins the portfolio.** The new product gets walked against this doc as part of its initial brief. Gaps the new product surfaces become candidate updates to the doc.

**When a new piece of research lands.** Any deep-research output in the portfolio that touches the principles directly (user voice, personalization patterns, mental-load patterns, anti-surveillance evidence) gets reflected in the doc.

**When a competitor's posture shifts.** §10 is the moat thesis. If a major competitor moves toward our posture (Anthropic ships shame-free language, Monarch removes streaks, MyFitnessPal bans calorie shaming), the moat narrows and we need to find the next structural distinction. The principles doc updates accordingly.

**When a principle starts feeling soft.** If the team is repeatedly arguing about whether a feature violates §8, the section is not clear enough. Sharpen the do/don't lists rather than relitigating the principle.

**At minimum, once a year.** Even if nothing has shifted, the doc gets re-read once a year and the date gets updated. The act of re-reading catches drift the team did not notice.

## What This Doc Is Not

The principles doc is not the strategy. Strategy is the choice of which products to build, in which order, for which markets, at which prices. The principles doc is the *how*, meaning the posture the products take regardless of the strategic choice.

The principles doc is not the design system. Colors, typography, component library, animation timing, accessibility audit, all of that lives in each product's own design repo.

The principles doc is not the brand guide. Each product has its own brand guide with the product-specific voice, the product-specific palette, the product-specific tagline. The principles doc is the umbrella under which those brand guides have to fit.

The principles doc is not a marketing document. It is internal infrastructure. The user-facing version of this work is the product itself. If we have to point a user at this document to explain why the product feels different, the product is failing.

## A Final Caution

The principles doc only works if it is read, referenced, and held to. A doc that nobody opens is worse than no doc, because it creates the appearance of alignment without the reality. The team's job is to make this document the actual operating constraint, not a relic of one sprint's writing exercise.

When in doubt: read §1. Lift the human. Reduce the load. Refuse the shame frame.
