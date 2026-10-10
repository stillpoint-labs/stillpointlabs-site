# Source: Smart Defaults + Pre-Fill Onboarding (Shopify / Atticus Li / Krasotin synthesis)

**Full citation:** Krasotin, S. "The quickest way to ruin onboarding? Wrong default state." Humble Team / Medium, 2026-03. Atticus Li. "How to Build AI-Powered Onboarding Flows That Personalize Automatically." Plus practitioner aggregation citing Shopify's design discipline ("default to the 95% choice").
**URL:** https://humbleteam-agency.medium.com/the-quickest-way-to-ruin-onboarding-wrong-default-state-558eed4ab37a · https://atticusli.com/blog/posts/build-ai-powered-onboarding-flows-personalize-automatically/
**Date accessed:** 2026-05-25
**Evidence level:** 7 (practitioner thought leadership; corroborated by Shopify design system docs)
**Research topic area:** T3 — First-30-days cold-start window + T4 — Don't-know-you-yet communication

## Credibility Scores

| # | Dimension | Score | Justification |
|---|-----------|-------|---------------|
| 1 | Authority | 5/10 | Practitioner Medium posts; the Shopify quote ("default to the 95% choice") is widely cited and traces back to Shopify's design org. |
| 2 | Evidence Quality | 4/10 | Practitioner observation; no controlled experiments cited inline. |
| 3 | Currency | 9/10 | 2025-2026 publications. |
| 4 | Intent | 4/10 | Content marketing for onboarding tools (Atticus Li runs a consultancy; Humble Team is an agency). |
| 5 | Bias & Objectivity | 5/10 | Advocacy-shaped; doesn't surface counterexamples where wrong defaults hurt. |
| 6 | Logic & Coherence | 7/10 | Internally consistent. The "show one pre-filled answer at a time, frame each step as quick confirmation" pattern is logically sound. |
| 7 | Corroboration | 7/10 | Pattern corroborated by NN/G's "power of defaults" work and by Shopify's published design discipline. |
| 8 | Intellectual Honesty | 5/10 | Doesn't address what happens when the default is wrong for the user's specific case. |
| 9 | Specificity | 6/10 | Names the "95% rule," progressive disclosure thresholds (drop-off at 10 questions), the pre-fill mechanics (timezone from IP, industry from email domain). |
| 10 | Relevance | 9/10 | Direct: HOW does Stillpoint pre-fill defaults from free signals during cold-start? |

**Composite score:** 5.50

## Bias Guard Check

- [x] I agree with this source's conclusions → scored harder on dims 5, 6, 8
- [ ] I disagree with this source's conclusions → scored more generously on dims 5, 6, 8
- [ ] Neutral / no strong reaction

## Key Findings

1. **Shopify's design discipline: default to the choice 95% of users would make if asked explicitly.** This is the operational rule for picking a default. If the system can identify the 95% case, ship that as the default; don't ask.
2. **Pre-fill from free signals.** Timezone from IP. Industry / cohort from email domain. Locale from device locale. Language from browser. Household size from invite count. None of these require asking the user.
3. **Show one question at a time with the answer pre-filled.** Each step becomes a confirmation rather than a fill-in-the-blank. This is dramatically faster, less effortful, and gets through 90% completion vs. 60% for explicit forms.
4. **Drop-off accelerates past question 10.** A 3-question initial setup followed by behavioral inference completes at >90%; a 10-question setup completes at <60%.

## Inclusion Decision

**Decision:** Supporting (Rule 4 — moderate composite, 3 factors favor: relevance, actionability, unique insight on the 95% rule and free-signal pre-fill list)
**Rationale:** The "95% rule" is the operationalizable version of editorial defaults. Worth citing.

**Perspective category:** Practitioner
