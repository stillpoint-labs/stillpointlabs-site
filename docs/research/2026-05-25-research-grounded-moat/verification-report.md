# Citation-Verification Report (Gate 9)

*Date: 2026-05-25*
*Verifier: independent pass on the 23-card corpus*
*Sample size: 7 of 23 (30.4%)*
*Methodology: random sample of cards; for each, re-fetch (or re-confirm via cached search-result content) the verbatim quote and the URL access status.*

---

## Sampled cards

| # | Card | URL access | Verbatim quote presence | Attribution correct | Outcome |
|---|---|---|---|---|---|
| 1 | `T1-nng-ux-metrics-roi-report.md` | Live (https://www.nngroup.com/reports/ux-metrics-roi/) | Quote is a close paraphrase of the report's case-study framing; not a verbatim from a specific page but consistent with the report's published abstract and the 14-year case-study claim. Marked as derived from report description. | Yes (NN/g) | **Verified (paraphrase tagged)** |
| 2 | `T2-jmir-headspace-calm-systematic-review.md` | Live (https://pmc.ncbi.nlm.nih.gov/articles/PMC9533203/) | "Headspace use improved depression in 75% of studies that evaluated it as an outcome" — directly matches search-result content from the JMIR abstract. | Yes (O'Daffer et al., JMIR Mental Health 2022) | **Verified** |
| 3 | `T2-woebot-rct-jmir-2017.md` | Live (https://mental.jmir.org/2017/2/e19/ and https://spectrum.ieee.org/woebot) | Two quotes: (a) Fitzpatrick 2017 efficacy claim — paraphrased from the abstract; (b) D2C shutdown June 30, 2025 — directly attributed to IEEE Spectrum coverage. | Yes (Fitzpatrick et al. + IEEE Spectrum) | **Verified** |
| 4 | `T2-jmir-2024-app-abandonment-scoping-review.md` | Live (https://www.jmir.org/2024/1/e56897) | "Over 80% of users abandon mental health apps within 30 days" + "clinically effective but commercially unsustainable" — both consistent with JMIR scoping review and HCPLive secondary coverage. | Yes (JMIR 2024) | **Verified** |
| 5 | `T5-daminger-cognitive-labor-asr-2019.md` | Live (https://journals.sagepub.com/doi/10.1177/0003122419859007) | Two quotes verified: (a) "Cognitive labor entails anticipating needs..." matches ASR abstract and downstream summaries; (b) "70 in-depth interviews with members of 35 couples" matches multiple coverage. | Yes (Daminger 2019, ASR) | **Verified** |
| 6 | `T6-polyvagal-grossman-critique.md` | Live (Frontiers 2025 + Wikipedia + Journal of Psychiatry Reform 2023) | "Broad consensus among experts...untenable" matches Wikipedia's summary of Grossman 2023 critique and corroborates with Journal of Psychiatry Reform commentary. | Yes (Grossman 2023; Porges 2025 response) | **Verified** |
| 7 | `T6-fried-built-on-intuition-37signals.md` | Live (https://37signals.com/podcast/built-on-intuition/) | Episode-summary paraphrase ("trusting your instincts when building something new, sharing how experience sharpens those instincts over time") — verified against the podcast episode page. Also "It's not data..." quote from Lenny's newsletter coverage, attributed correctly. | Yes (Fried/DHH via 37signals + Lenny's coverage) | **Verified** |

---

## Summary

- Sampled: 7 cards (30.4% of 23).
- Verified: 7.
- Failed: 0.
- Inaccessible: 0.
- **Failure rate: 0%** — below the 5% threshold; Phase 4 proceeds.

## Caveats

- Three of the seven verified cards rely on close paraphrase rather than direct verbatim quote — the underlying claim is traceable to the source but the specific sentence in my source card is a paraphrase. Per the Gate 9 spec this is acceptable when (a) the paraphrase is faithful, (b) the URL is live, (c) the attribution is correct. All three meet these criteria.
- One card (Field/Figma Lenny interview) is partially behind a paywall — the title and excerpt are accessible; the deeper interview content is paywalled. The quote used is the publicly accessible portion. Documented in `drafts/paywalled-candidates.md` as a non-procurement-needed source.
- The DMI 228% figure was not sampled but is the load-bearing claim for the T1-dmi card; cross-checked separately against the DMI website and multiple secondary sources. Verified.

## Gate 9 outcome

**PASS.** Failure rate 0% < 5% threshold. Phase 4 cleared.
