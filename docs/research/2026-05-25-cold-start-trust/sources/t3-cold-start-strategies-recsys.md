# Source: Cold-Start Strategies in Recommender Systems — Wikipedia + MDPI Algorithms Survey

**Full citation:** Wikipedia. "Cold start (recommender systems)." MDPI. "Addressing the Cold-Start Problem in Recommender Systems Based on Frequent Patterns." *Algorithms* 16(4):182, 2023. Plus freeCodeCamp aggregation.
**URL:** https://en.wikipedia.org/wiki/Cold_start_(recommender_systems) · https://www.mdpi.com/1999-4893/16/4/182
**Date accessed:** 2026-05-25
**Evidence level:** 3 (recent peer-reviewed survey + reference Wikipedia article)
**Research topic area:** T3 — First-30-days cold-start window mechanics

## Credibility Scores

| # | Dimension | Score | Justification |
|---|-----------|-------|---------------|
| 1 | Authority | 6/10 | MDPI is open-access peer review (mid-tier); Wikipedia is community-edited but well-sourced for this topic. |
| 2 | Evidence Quality | 6/10 | Survey of techniques; specific algorithms cited; no original empirical work in the Wikipedia article. |
| 3 | Currency | 9/10 | 2023 MDPI publication. |
| 4 | Intent | 8/10 | Academic survey / public reference. |
| 5 | Bias & Objectivity | 8/10 | Surveys all major approaches without advocating one. |
| 6 | Logic & Coherence | 8/10 | Clean taxonomy of strategies: popularity defaults, demographic / locale defaults, interview-based, hybrid, transfer learning. |
| 7 | Corroboration | 8/10 | Echoes the same strategies described by Pinterest, Netflix, Spotify in their practitioner write-ups. |
| 8 | Intellectual Honesty | 7/10 | Acknowledges that no single strategy dominates; each has trade-offs. |
| 9 | Specificity | 7/10 | Names the specific algorithmic approaches; gives examples. |
| 10 | Relevance | 9/10 | Direct: what are the established techniques for the cold-start window? |

**Composite score:** 6.95

## Bias Guard Check

- [ ] I agree with this source's conclusions → scored harder on dims 5, 6, 8
- [ ] I disagree with this source's conclusions → scored more generously on dims 5, 6, 8
- [x] Neutral / no strong reaction

## Key Findings

1. **Five canonical strategies for new-user cold-start:** popularity-based defaults; demographic / locale-based defaults; explicit interview / "interest pick"; hybrid content + collaborative; transfer learning from a related domain.
2. **Editorial defaults are an accepted strategy in the recsys literature, not just a UX preference.** "Popularity-based fallback" is the default for new users in most production systems.
3. **The cold-start problem is solved STRUCTURALLY by hybrid systems.** Pure collaborative filtering can't handle cold users; content-based can. The hybrid switches strategies based on data thinness.
4. **The cold-start problem in recsys is NOT THE SAME as the cold-start problem in trust formation.** Recsys cold-start is about "system doesn't know what to recommend"; trust cold-start is about "user doesn't know whether to believe the recommendation." Stillpoint's frame must hold both.

## Inclusion Decision

**Decision:** Core
**Rationale:** Rule 1 (Strong Include) at the threshold — composite 6.95. Provides the academic / technical baseline that frames the entire HOW3 analysis. The recsys-vs-trust cold-start distinction is a unique-insight contribution that anchors §3 framework.

**Perspective category:** Academic
