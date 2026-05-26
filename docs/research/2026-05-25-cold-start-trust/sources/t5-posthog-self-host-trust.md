# Source: PostHog — Self-Host as Trust Signal for Developer Tools

**Full citation:** PostHog. "8 best open source analytics tools you can self-host." PostHog. "FAQ." PostHog. "Self-host PostHog Docs." Plus GitHub repository.
**URL:** https://posthog.com/blog/best-open-source-analytics-tools · https://posthog.com/faq · https://posthog.com/docs/self-host · https://github.com/PostHog/posthog
**Date accessed:** 2026-05-25
**Evidence level:** 7 (vendor documentation; the open source code IS the audit)
**Research topic area:** T5 — Anti-tracking trust earning

## Credibility Scores

| # | Dimension | Score | Justification |
|---|-----------|-------|---------------|
| 1 | Authority | 6/10 | PostHog is a credible open-source analytics vendor; well-regarded in dev community. |
| 2 | Evidence Quality | 4/10 | Vendor docs; the architectural claims are inspectable via GitHub. |
| 3 | Currency | 9/10 | 2024-2026 documentation. |
| 4 | Intent | 5/10 | Vendor marketing for the cloud product (which has the real adoption); self-host option is a trust hedge. |
| 5 | Bias & Objectivity | 6/10 | PostHog acknowledges that self-hosting "is not recommended" for most users; surprisingly honest about the trade-offs. |
| 6 | Logic & Coherence | 8/10 | The trust mechanism is clear: "we offer self-host because you might not trust our cloud, even though our cloud is what we mostly want you to buy." |
| 7 | Corroboration | 7/10 | Similar pattern in GitLab, Mattermost, Plausible, Umami. |
| 8 | Intellectual Honesty | 7/10 | Acknowledges that self-hosted deployments miss new features and require infrastructure expertise. |
| 9 | Specificity | 7/10 | Names what self-host offers vs. cloud, what it gives up. |
| 10 | Relevance | 8/10 | Direct: how do developer-trust products earn trust through OPTIONAL data-control? |

**Composite score:** 5.95

## Bias Guard Check

- [x] I agree with this source's conclusions → scored harder on dims 5, 6, 8
- [ ] I disagree with this source's conclusions → scored more generously on dims 5, 6, 8
- [ ] Neutral / no strong reaction

## Key Findings

1. **Self-host is a trust signal even when most users don't use it.** "You COULD inspect and run our code yourself; the fact that we let you do that is the trust commitment." A user who never self-hosts still derives trust from the option being available.
2. **Open-source verifiability is the structural trust mechanism.** Like Signal, Proton — the architecture supports a path-out, which makes the cloud path-in more trusted.
3. **The "you can leave any time" affordance is structurally trust-building.** Lock-in destroys trust; reversibility builds it. This is the inverse of the Mint trust-loss pattern (Intuit's reversibility was effectively closed by integration depth).
4. **Stillpoint analog: data-export-on-demand, user-owned data primitives.** A user who can export their entire household state at any time has been given the "you can leave any time" affordance. The fact that most won't doesn't matter; the option earns trust.

## Inclusion Decision

**Decision:** Supporting (Rule 4 — 3 factors favor: relevance, unique insight on reversibility-as-trust, contextual fit)
**Rationale:** The "self-host option" pattern translates to "data export on demand" for Stillpoint's consumer products.

**Perspective category:** Practitioner
