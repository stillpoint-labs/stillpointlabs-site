# Phase 1 — Research Design

*Project: Is Stillpoint Labs' "build UX from research first" approach a defensible strategic moat — and HOW can it be deepened?*
*Date: 2026-05-25*
*Methodology: deep-research v0.1.0 + v2 gates 9-10 + v3 gates 11-14 (term-definition, prior-research-integration, HOW-not-WHAT, readability)*
*Status: AUTO-APPROVED (Noah unavailable; instructed to proceed without checkpoint)*

---

## Research questions

Five HOW-shaped questions. Each has a falsifiable answer and a decision attached.

1. **HOW defensible is "deep research as the UX foundation" as a strategic moat?** Is there empirical evidence that products built from behavioral/psychological/physiological research outperform products built from intuition or feature parity, controlling for execution quality? What is the magnitude of the effect? What is the half-life before the moat erodes?

2. **HOW unique is Stillpoint's approach right now?** Map the competitive landscape across behavior-change (Whoop, Levels), couples/marital-tech (Lasting, Paired, Relish), mental-health (Calm, Headspace, Woebot, Bloom), financial-wellness (Monarch, Copilot), category leaders (Notion, Linear). For each: is "research-grounded" framing real (applied at the UX-decision level) or marketing (applied only at messaging level)? What patterns do the actually-research-grounded ones share?

3. **HOW likely is it that competitors move into this space?** What would it take for a typical SaaS team to adopt this approach? What is the binding barrier — research expertise, time, framing, organizational structure? Are there hiring patterns, conference talks, framework releases suggesting movement? When does "research-grounded UX" commoditize?

4. **HOW can Stillpoint deepen this moat further?** What specific practices, infrastructure, or organizational choices would make this harder to replicate? Candidates surfaced by Noah: proprietary research methodology (the deep-research skill itself), longitudinal user-research corpus, academic partnerships, public research-publishing cadence, hiring patterns, IP strategy.

5. **HOW does Stillpoint's specific blend (physiology + psychology + marital/family dynamics, applied to consumer SaaS) compare to adjacent approaches?** Most "research-grounded" companies focus on one discipline. Stillpoint integrates several. Is this a strength or a weakness vs specialist competitors? What is the empirical support either way?

---

## Scope boundaries

**In scope:**
- Consumer SaaS / consumer apps where UX choices visibly differ from competitor norms
- Evidence that research-grounded UX changes retention, satisfaction, harm, or unit economics
- Competitive landscape May 2025 → May 2026 (with longer history for academic anchors)
- The specific tri-disciplinary blend (physiology + psychology + marital/family dynamics) as applied
- Organizational and tooling moves that deepen the moat (publishing cadence, methodology productization, hiring shape)

**Out of scope:**
- Pure-B2B SaaS positioning (Noah's seed question 3 acknowledges this is unanswered; we surface it as future research, not as primary scope)
- Specific product decisions for any single Stillpoint property — this research is portfolio-level
- The mechanics of any individual research method (e.g., diary studies vs. card sorts) — those are an implementation layer below this research
- Choice of research-team org structure beyond moat-deepening (recruiting playbooks, comp structure)

---

## Topic map

Six topic areas. Each maps to one or more research questions.

| # | Topic area | RQs answered | Why it matters |
|---|---|---|---|
| T1 | Empirical evidence that research-grounded design outperforms intuition or feature-parity | RQ1 | The load-bearing empirical question. If there's no signal in the data, the moat is illusory. |
| T2 | Competitive landscape — who is actually research-grounded vs marketing-grounded | RQ2 | Tells us which competitors are already in the same quadrant and which are bluffing. |
| T3 | Diffusion and commoditization timeline for research-grounded UX practices | RQ3 | Tells us the half-life. Names the binding barriers and the leading indicators of erosion. |
| T4 | Moat-deepening mechanisms — methodology productization, publishing, partnerships, hiring | RQ4 | Decision-shaped. Each mechanism either ships or doesn't. |
| T5 | Tri-disciplinary integration (physiology + psychology + marital/family) vs single-discipline specialists | RQ5 | The differentiator that needs empirical defense or honest acknowledgment as a hypothesis. |
| T6 | Contrarian / null findings — when research-grounded UX is overhyped, intuition wins, or the moat is a story | All RQs | Gate v1 requires it. Disconfirming evidence is the source of intellectual honesty. |

---

## Inclusion / exclusion criteria (PRISMA-style pre-registration)

**Accepted source types:**
- Academic: peer-reviewed journals (HCI, behavioral economics, applied psychology, family studies, behavior change), university lab publications, working papers (NBER, SSRN), dissertations
- Institutional: NN/g (Nielsen Norman Group), IDEO, ACM CHI, McKinsey/Forrester/Gartner reports, government UX guidance, professional bodies (UXPA, IXDA)
- Practitioner: long-form blogs and talks by senior designers/PMs at companies that publish their work (Spotify R&D, Intercom Inside, Basecamp, Linear, Notion, Headspace blog)
- Boots-on-the-ground: indie hackers, founders writing about what actually worked (or didn't), small-team retrospectives, postmortems
- Contrarian: voices arguing research is overhyped, intuition wins, agile beats research, MVP/iteration outperforms ethnography, etc.

**Date range:** 2018-01-01 → present. Older works admitted only if they are foundational anchors (Cooper, Norman, NN/g older pieces). Anchor-class works get the timeless bonus per the rubric.

**Geography:** Primarily US/UK/EU consumer-app literature; Asian markets admitted where directly comparable.

**Languages:** English only (acknowledged limitation).

**Exclusions:**
- Vendor whitepapers selling research tools where the entire claim is "look how much we found" without methodology disclosure (score them as marketing-tier; admit only if a unique data point with explicit caveat)
- Listicles, content-mill blog posts with no byline or with bylines from contract content shops
- AI-generated SEO content (identifiable by characteristic structure / banned-buzzword density)
- Any source where the original claim cannot be sourced to a method (no raw data, no method description, no citations of its own)

---

## Target audience

This research informs every current and future Stillpoint product. Primary readers:

1. Noah (founder) — making moat-deepening investment decisions over the next 6–18 months
2. Future Stillpoint hires (when applicable) — onboarding on why the company makes the calls it makes
3. Subagent Code tasks downstream — Recommendations §1 must be specific enough that a future Code task can act on them

Expertise level assumed: smart-but-non-specialist. ELI10 throughout per Gate 11.

---

## Output structure

Standard §1–§7 per `research-document-template.md`. Path:

```
docs/research/2026-05-25-research-grounded-moat/
├── analysis.md                  # §1 Recommendations through §7 Bibliography
├── sources/                     # one card per evaluated source
├── drafts/
│   ├── phase-1-design.md        # this file
│   ├── search-log.md            # cumulative search log
│   └── paywalled-candidates.md  # Gate 10 surfacing file
└── verification-report.md       # Gate 9 30%-sample report
```

§3 will be a framework section — "the right way to think about research-grounded UX as a moat" — required by the user's prompt regardless of what emerges. If a typology genuinely emerges from the sources, the framework will be that. If not, the framework will be the layered-moat decomposition Noah's prompt anticipates (one moat or several? how do physiology, psychology, marital/family dynamics interact in product decisions?).

---

## Prior research carried forward (Gate 12 — name and use, don't just reference)

Each prior artifact gets a paragraph naming the specific finding being carried forward into this research.

### 1. Stillpoint operating principles (`stillpointlabs-site/docs/principles/2026-05-25-stillpoint-operating-principles.md` on `feat/stillpoint-principles-2026-05-25`)

The canonical doc. §10 is the moat thesis this research builds on. The specific claim being carried forward: "Stillpoint's moat is not the technology, not the AI capability, not the feature checklist, not the pricing, and not even the brand. The moat is the operating principles in this document, executed consistently, across a portfolio of products, in a market where every well-capitalized competitor is structurally barred from doing the same thing." The principles doc identifies the moat as *structural misalignment* between Stillpoint's choices and competitor business models — token-billed pricing, engagement-driven valuations, content-engine dependence on the discipline-problem framing. This research interrogates whether that thesis holds up against external evidence (RQ1, RQ3), and whether "research-grounded UX" is the load-bearing input that produces those choices — i.e., the upstream moat that makes the downstream structural-misalignment moat possible.

### 2. Troth household-finance research (`troth/docs/research/household-finance-research.md`)

Two specific findings carry forward. First: **state-dependence > trait-stability**. The same person who is a Glancer at baseline becomes an Avoider under sufficient overwhelm (Sicherman 2016; Hilbert 2024; Xin 2023). This is the physiological substrate of the research-grounded UX claim — products that assume trait-stability produce abandonment because they can't respond to the state shift. Second: **dimensional archetypes beat categorical types** (Klontz Money Script Inventory KMSI-R 2025: CFI = .762 on diverse samples). Categorical types fail empirically; dimensional patterns surface from behavior. Both findings are evidence that research-grounded products make architectural calls (state layer, dimensional rendering) that competitors structurally cannot copy without abandoning their existing data models.

### 3. Troth positioning addendum (`troth/docs/positioning/failure-is-not-a-character-issue.md`, commit `7449728`)

The specific finding carried forward: **systems-not-character is the moat**. Six independent research programs (ostrich effect — Galai & Sade 2006, Sicherman 2016; Yerkes-Dodson revisited 2024; Lazarus-Folkman threshold model; Mullainathan & Shafir scarcity bandwidth tax; Porges polyvagal theory; Xin 2023 financial-anxiety multiplier) converge on the claim that financial avoidance is a state with a physiological substrate, not a character defect. Ramsey, YNAB, and the broader personal-finance industry are structurally locked into the discipline-problem framing because their entire content engine depends on it. The moat is *the choice the competitor cannot copy even when they want to*. This research checks whether "research-grounded" is the upstream skill that produces these unmakeable choices, or whether it's downstream window dressing.

### 4. Ingle personalization mechanics (`ingle/docs/research/2026-05-25-personalization-mechanics/analysis.md`)

Two findings carry forward. First: **the editorial-default pattern** — the product makes a choice on the user's behalf (single Tonight Card, one proposed dinner, one primary action) because choice overload intensifies at low executive function (Bollen 2010 RecSys; Thaler & Sunstein; EF-depletion literature). This is the operationalized form of "we use research to make choices users would otherwise have to make under load." Second: **the system-is-the-only-assignor principle** — Ingle is the only entity that can route anticipation cards; either spouse can claim them, but the system never silently routes to one spouse. This dissolves the Daminger asymmetry (women lead cognitive labor in 26 of 32 hetero couples) at the architectural level. Both findings are research-grounded UX in action; this project asks whether the upstream research methodology is itself a defensible asset.

### 5. Reveal positioning refresh (`reveal/docs/research/2026-05-22-positioning-refresh/analysis.md`)

The specific finding carried forward: **judgment is the moat**. Every adjacent tool (Adobe Generative Remove, Google Reimagine, Luminar GenSwap) chose generative invention while Reveal alone chose curatorial restraint. The Conjointly 2025 finding (AI marketing-image agreement fell from 55% to 36% YoY) documents an accelerating consumer-trust tailwind. The Reveal thesis names the judgment call — "we don't invent, we develop" — as the load-bearing positioning, and the judgment call is *itself* a research-grounded choice (S06 anti-AI backlash, S11 Magnum-vs-Leibovitz debate). This project asks whether the upstream research that produced the judgment is the durable asset, or whether the moat lives at the judgment-output layer.

### 6. Borg-collective positioning refresh (`borg-collective/docs/research/2026-05-22-positioning-refresh/analysis.md`)

The specific finding carried forward: **cognitive load is the moat**. Three independent 2026 sources converged: HBR 14% AI-induced mental fog, METR 19% developer slowdown, Anthropic's own 17% skill-mastery reduction. The wedge isn't the orchestration code (six OSS clones at architectural parity) — it's the skill layer that explicitly refuses engagement (`adhd-guardrails`, scope-locking, work/life boundaries) which Anthropic and Cursor cannot ship because token-billed pricing punishes them. The moat structure mirrors Troth's: the choice the competitor cannot copy because their business model punishes it. This project asks whether "research that finds these structural-misalignment opportunities" is itself the productizable, defensible practice.

### 7. Borg-collective agent teams research (`borg-collective/docs/research/2026-05-23-agent-teams/analysis.md`)

The specific finding carried forward: **the architecture debate is settled for orchestrator + isolated subagents on breadth-first tasks** (Anthropic 90.2% improvement on internal research eval). Deep research is itself a breadth-first task. The deep-research skill (which produced four of the prior artifacts above) is plausibly the productizable methodology Noah's seed-question 4 names — this research will examine whether productizing it is a moat-deepening move or a giveaway.

---

## Source-diversity targets per topic

Each topic must hit at least 3 of 5 categories. Contrarian is mandatory per Gate v1.

| Topic | Academic | Institutional | Practitioner | Boots | Contrarian |
|---|---|---|---|---|---|
| T1 (empirical moat) | ✓ (HCI, behavioral econ) | ✓ (NN/g, McKinsey) | ✓ (sr designers) | ✓ (indie founders) | ✓ (intuition-wins) |
| T2 (competitive landscape) | – | ✓ (analyst reports) | ✓ (vendor blogs) | ✓ (user reviews, retros) | ✓ (vendor skeptics) |
| T3 (diffusion / half-life) | ✓ (innovation diffusion) | ✓ (hiring trend reports) | ✓ (designer talks) | ✓ (founder hiring decisions) | ✓ (commoditization writers) |
| T4 (moat-deepening) | ✓ (organization studies, IP) | ✓ (consulting practice studies) | ✓ (founder essays) | ✓ (research-published indies) | ✓ (anti-publication) |
| T5 (tri-disciplinary blend) | ✓ (interdisciplinary, T-shaped) | ✓ (design-thinking) | ✓ (multi-disc practitioners) | – | ✓ (specialization wins) |
| T6 (null findings) | ✓ (failed-replications) | – | ✓ (anti-research designers) | ✓ (intuition-first founders) | ✓ (whole category) |

Gaps are acceptable where genuinely empty after good-faith search; they will be documented in §6 Methodology Limitations.

---

## Search strategy

- Minimum 3 queries per topic, varied across factual / evaluative / contrarian / experiential framings
- Target ~25-40 sources evaluated, ~15-25 included (matches scope and prior-research norms in this codebase)
- Search log: cumulative table in `drafts/search-log.md`, copied to §6 Methodology at end
- Paywalled-source candidates: surfaced to `drafts/paywalled-candidates.md` per Gate 10

---

## Gates applied

- **v1 Gates 1-8:** source diversity (5 perspectives mandatory), decision-shaped recommendations, no orphaned claims, bias guards, triangulation, manifest verification, perspective balance, ELI10
- **v2 Gate 9:** citation verification, 30% sample after Phase 3, failure rate must be ≤5%
- **v2 Gate 10:** paywall surfacing mid-Phase 2, file at `drafts/paywalled-candidates.md`
- **v3 Gate 11:** term-definition — every technical term defined on first use, ELI10 standard
- **v3 Gate 12:** prior-research-integration — each prior artifact named and used in this design (above), and referenced specifically in §4 Analysis with the carry-forward
- **v3 Gate 13:** HOW-not-WHAT — §1 Recommendations are decision-shaped; each one tells a future Code/Cowork task what to *do*
- **v3 Gate 14:** readability — §1 must read aloud in under 3 minutes total; max ~600 words across all recommendations

---

## Auto-approval note

Per the user's instruction (Noah unavailable, full autonomy on Phase 1, no checkpointing), this design is auto-approved and Phase 2 begins immediately. Any judgment calls on scope or depth are documented in `§6 Methodology > Limitations`.
