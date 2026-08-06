# Phase 1 — Research Design (Cold-Start Trust)

*Auto-approved 2026-05-25. The 7 HOW questions in the brief are taken as the canonical research questions; this document fixes scope, audience, inclusion criteria, and the topic map.*

## Research questions (the 7 HOWs)

1. **HOW do users actually decide whether to trust a new app in the first session?** First 60 seconds, first 5 minutes, first session. Activation-to-retention curve. Sirdeshmukh, Fogg.
2. **HOW does Stillpoint earn enough trust to keep the user past the first session, given no surveys and no surveillance?** Pattern study of Spotify Daylist debut, Pinterest visual-pin pick, Netflix taste sampler, Headspace first-session, Calm, Robinhood deferred-funding.
3. **HOW does Stillpoint handle the first-30-days cold-start window where behavioral data is thin?** Editorial defaults, locale pre-warming, public-data pre-fills, social-proof anchors, one-tap confirmations.
4. **HOW does "I don't know you yet" get communicated WITHOUT breaking editorial-defaults confidence?** Linguistic + design pattern for starting-from-defaults without surfacing anxiety. Headspace, Spotify Daylist first-day mood text.
5. **HOW do anti-tracking / privacy-first products earn trust without surveillance signals?** DuckDuckGo, ProtonMail/Pass, Signal, Posthog. Is "we don't track you" enough or paired with competence demos?
6. **HOW does trust evolve day 1 → day 30 → day 90?** Milestones: trusted-enough-to-retain → disclose-sensitive-context → act-on-suggestions-without-confirmation. Empirical curve.
7. **HOW does trust differ between Troth (financial, high stakes, trauma common) and Ingle (meal/grocery, lower stakes)?** Plaid trust, Mint vs. Yummly/Mealime/Plan to Eat onboarding.

## Scope boundaries

**In scope:** Consumer-facing software onboarding patterns from 2018 onward (post-smartphone-mature, post-GDPR); academic literature on online trust (Sirdeshmukh, Fogg, McKnight), satisficer/maximizer choice psychology (Simon, Schwartz, Iyengar), trust formation in financial-services contexts (Mint history, Plaid trust patterns, Robinhood), trust formation in low-stakes utility apps (Spotify, Pinterest, Netflix, Headspace, Calm), privacy-first product onboarding (DuckDuckGo, Proton, Signal, Posthog), the cold-start problem in recommender systems and the cold-start problem in trust formation (treated as DISTINCT problems), editorial defaults / smart-default literature, the activation-to-retention curve.

**Out of scope:** Clinical psychology of trust (attachment-style, betrayal trauma — too far from product surface); B2B SaaS trust building (different stakeholder set); deep social-engineering / phishing-trust literature (defensive trust, not constructive trust); pre-2018 product post-mortems where smartphone defaults were different; voice-only / smart-speaker trust; web-3 / crypto wallet trust patterns; AI-chatbot anthropomorphic-trust (Ingle / Troth are not chatty assistants); developer-tool / IDE trust.

## Inclusion / exclusion criteria (pre-registered, PRISMA-style)

- **Accepted source types:** peer-reviewed academic papers; institutional reports (NN/G, government, professional bodies); engineering blogs from named teams at scaled products; long-form journalism from established outlets; practitioner essays where the practitioner is named and verifiable; structured boots-on-the-ground commentary (Reddit / HN threads with multiple corroborating posts); contrarian voices arguing trust is overrated.
- **Excluded source types:** anonymous Medium SEO; affiliate-link "best onboarding" listicles; vendor sales pages presented as research; content-mill rewrites; pre-2018 product post-mortems unless the underlying principle is timeless (Fogg credibility work qualifies as timeless).
- **Date range:** Primary sources 2018-2026. Foundational psychology / persuasion literature (Sirdeshmukh 2002, Fogg 2002) accepted with timeless-bonus.
- **Geography / language:** English-language sources; US/UK/EU consumer market primarily. Sources from other markets accepted if the pattern translates (Spotify is Swedish, Tinder is global, etc.).
- **Triangulation rule:** No load-bearing claim accepted from a single source category. Each of the 7 HOW questions must be answered by sources from at least 3 of the 5 perspective categories.

## Target audience

- **Primary:** Noah (founder, decision-maker on principle 11 candidate).
- **Secondary:** Future Code agents implementing the cold-start trust features in Troth and Ingle.
- **Tertiary:** Future beta users — never directly read but every recommendation must be defensible to them.

## Topic map

Seven topic areas, one per HOW question:

| # | Topic area | Sub-questions |
|---|------------|---------------|
| T1 | First-session trust decision mechanics | What signals matter in the first 60s / 5min / 1 session? What's the Fogg "prominence-interpretation" actually do? What's the gap between "use again" and "trust with sensitive data"? |
| T2 | Trust-earning moves at scaled consumer products | What did Spotify, Pinterest, Netflix, Headspace, Calm, Robinhood each do at first session? Common pattern? Editorial-defaults role? |
| T3 | First-30-days cold-start window mechanics | Editorial defaults, locale pre-warming, public-data pre-fills, one-tap confirmations, social-proof anchors. |
| T4 | "Don't-know-you-yet" communication without breaking confidence | The linguistic & design pattern. How does Headspace / Spotify-Daylist speak the day-1-confidence + "we're starting from defaults" duality? |
| T5 | Anti-tracking trust earning | DuckDuckGo, Proton, Signal, Posthog. Is "we don't track" enough? Paired with competence? |
| T6 | Trust evolution day 1 → 30 → 90 | Empirical curve. Milestones. State-dependence of trust. |
| T7 | Trust-stakes asymmetry: financial vs. meal/grocery | Plaid, Mint, Yummly, Mealime, Plan to Eat. Different trust math. |

## Output structure

Standard deep-research v0.1.0 template: §1 Recommendations, §2 Summary, §3 Framework (the cold-start trust framework that emerges), §4 Analysis (per-topic deep dives), §5 Research, §6 Methodology, §7 Bibliography.

Plus the brief's required §6 feedback (draft Principle 11 text) and §7 new spin-out questions. These will live in §1 (the principle draft as a recommendation block) and §6.

## Prior research carry-forward contract (Gate 12)

Each of the 5 prior docs named in the brief must produce at least one explicit carryforward into §2 or §3 of the final analysis:

1. **`docs/principles/2026-05-25-stillpoint-operating-principles.md`** — §6 (no questionnaires), §8 (no surveillance), §4 (Elisabeth as satisficer), §6 closing (data flow stays user-facing).
2. **`~/dev/ingle/docs/research/2026-05-25-personalization-mechanics/analysis.md`** — §4.1 (reorder-frequency + time-since-last do 70% of personalization for groceries), the 4-question 60-second triage as minimum explicit intake.
3. **`~/dev/troth/docs/research/household-finance-research.md`** — state-dependence framework; no-triage decision; dimensions surface from behavior.
4. **`~/dev/troth/docs/positioning/failure-is-not-a-character-issue.md`** (cited in principles) — trust-frame for users with prior financial-tool trauma.
5. **`~/dev/ingle/docs/research/2026-05-22-positioning-refresh/analysis.md`** — Logistics Laura installer-vs-user split; trust gap between channel buyer and daily user.

## Methodology version

deep-research v0.1.0 + v3 principles applied throughout (term definition on first use; prior-research carry-forward as Phase 0 contract; HOW-shaping verification; ELI10 readability with paragraph caps in §1, §2, §3).
