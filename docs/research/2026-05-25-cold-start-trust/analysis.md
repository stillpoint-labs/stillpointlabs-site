# Cold-Start Trust — How Stillpoint Earns the First-Session Keep, Without Surveys or Surveillance

*Conducted: 2026-05-25 · Methodology: deep-research v0.1.0 + v3 principles applied (term definition on first use; prior-research integration per Gate 12; HOW-shaping; ELI10 readability) · Branch: research/cold-start-trust-2026-05-25 (bundle if FUSE blocks git)*

> **Reading guide.** §1 is the action list — 13 feature recommendations, readable in 3 minutes, ready to drop into Code tasks. §2 is the 4am-tired-dad version with the carry-forward from prior research. §3 is the one framework that emerged, single-sentence summary at top. §4-§7 are the dig-deeper layers. §8 is the draft Principle 11. §9 is the spin-out questions. If you read only one section, read §1.

---

## Terms used in this document (defined on first use)

- **Activation curve:** the empirical shape of how many users stay engaged from install to day 1 to day 30 to day 90. Average mobile app loses ~77% of users by day 3, ~75% by day 30 (D30 ~5.7% across 31 categories). Fintech and productivity over-index at D30 10-18%.
- **Aha moment vs. activation:** Aha is the user RECOGNIZING value ("oh — I see what this does"). Activation is the user EXPERIENCING value ("I have the thing the product promised"). They can happen in the same minute or weeks apart; activation predicts retention.
- **Cold start (recsys):** the problem of "the system doesn't have enough data about this user to recommend well." Solved by editorial defaults, popularity, demographic / locale signals, hybrid content + collaborative filtering.
- **Cold start (trust):** a *different* problem — "the user doesn't have enough data about the system to believe the recommendation." Solved by competence demonstrations, structural assurances, transparent humility about what is and isn't known. **The two cold-start problems share a name but require different solutions; conflating them is a common failure mode.**
- **Disclosure trust:** the trust threshold at which a user voluntarily shares sensitive information (bank credentials, medical history, financial anxiety). Distinct from retention trust ("I'll use this again"). Usually crosses days or weeks after retention trust.
- **Editorial default:** the system makes the choice that 95% of users would make if asked explicitly, and ships that as the default. The user can override; most won't need to. Originally a Shopify design discipline; now common across consumer products.
- **Elisabeth (in this document):** the canonical Stillpoint satisficer-spouse from the operating principles. Decides in the first session whether the app earns its place on her phone. Hates UI change. Has zero tolerance for app wonkiness.
- **Fogg's Prominence-Interpretation theory:** users assess credibility in two stages — PROMINENCE (does the user notice the cue?) × INTERPRETATION (does the user judge the cue positively or negatively?). Both must fire. A trust cue nobody notices has zero effect.
- **McKnight three-mechanism model:** initial trust online forms from three independent sources — DISPOSITION (the user's baseline propensity to trust), INSTITUTION-BASED TRUST (situational normality + structural assurance), and SOCIAL CATEGORIZATION (the system looks like trustworthy systems the user already knows).
- **Maximizer:** a user who keeps searching the option space for the optimum even after they already have an acceptable answer. Tolerates UI change, configuration, and complexity. Not Stillpoint's primary target.
- **Performative privacy / privacy theater:** privacy claims without architectural backing. The appearance of diligence without the substance. Recognizable patterns: cookie banners that change nothing; privacy nutrition labels with inaccurate disclosures; differential-privacy claims with no audit.
- **Problem-solving orientation (Sirdeshmukh):** the most asymmetrically-weighted of the three trust dimensions. How the company handles failure dominates trust formation — users notice and remember recovery behavior 2-3x more than they notice nominal competence on a good day.
- **Satisficer:** a user who picks the first option that meets their needs and stops searching. Most humans most of the time. The canonical Stillpoint user.
- **Sirdeshmukh three-dimension model:** trustworthiness has three components — operational COMPETENCE ("they can do the job"), operational BENEVOLENCE ("they have my interests in mind"), and PROBLEM-SOLVING ORIENTATION ("when something goes wrong, they fix it well").
- **State-dependence:** the same user occupies different positions on behavioral dimensions as their situation changes. A baseline-Glancer becomes an Avoider under overwhelm. Trust formation is state-dependent; a user in a stressed state evaluates the same trust cues more harshly.
- **Structural assurance:** McKnight's term for "the system has architectural safeguards in place that make betrayal hard." Signal's Sealed Sender; Proton's open source; tokenized bank access via Plaid. The user trusts the architecture, not the promise.
- **Trust formation curve:** trust evolves through stages — formation, facilitation, establishment, acceleration, reinforcement. Each stage has different dominant cues. Disclosure behavior is a key marker of the formation -> establishment transition.

---

## 1. Recommendations

13 features. 7 cross-product. 3 Troth-specific. 3 Ingle-specific. Each names the surface it ships on, the principle it reinforces, and which prior research it carries forward. Each is specific enough to open a Code task from directly. Paragraphs capped at 6 sentences.

### Cross-product features (ship in both Troth and Ingle)

**F1 — Loading-screen value delivery.** The first interaction the user has with the app, before any decision is asked, delivers a small unit of the product's promised value. Headspace ships a breath-in-breath-out exercise on the loading screen; the user experiences competence before being asked to trust. Ingle equivalent: the loading screen shows a single concrete "what's likely for dinner tonight" placeholder based on time-of-day + locale, even before the household has been set up. Troth equivalent: the loading screen shows "what's likely sitting in your account this paycheck" as a placeholder based on locale's median household. Both are clearly labeled as "preview — set up to see yours." *Carries forward: Headspace pattern (T2); Stillpoint operating principles §4 Elisabeth defaults-are-the-product. Surface: app loading screen. Principle reinforced: §2 brand voice (lead with concrete situation, not abstract claim); §4 Elisabeth front-end pixel-perfect.*

**F2 — Locale + free-signal pre-warming.** Before the user touches anything, the system collects free signals — locale (regional median household income, regional cuisine norms), device language, device timezone, day-of-week and time-of-day of first install, household size if inferable from invite count. Pinterest's locale-only pre-warming lifted activation 5-10% across countries. Shape the editorial defaults from these signals; never claim to know more than the signal supplies. *Carries forward: Pinterest cold-start (T2); recsys cold-start strategies (T3); Stillpoint operating principles §6 personalization-from-behavior-not-self-report. Surface: backend pre-fetch + first-session render. Principle: §6 (data from behavior).*

**F3 — Single-card editorial-default first session.** Whatever the product does on its highest-stakes surface, the first session ships exactly ONE confident editorial answer with a single primary action ("Yes, this") and a single quiet escape hatch ("Not this — show me another"). No grid, no catalog, no search field, no "let's start with a quick survey." The editorial choice is grounded in concrete context the user can verify ("based on what most households in your area are spending this week," "based on what most kitchens in your area cooked this Tuesday"). *Carries forward: Ingle Tonight Card pattern (Ingle personalization F3); Headspace, Calm, Spotify Daylist convergent pattern (T2); Thaler & Sunstein defaults (Ingle T7). Surface: first-session main view in both products. Principle: §7 mental-load reduction; §4 Elisabeth defaults-are-the-product.*

**F4 — "Starting from defaults — help me learn you" linguistic frame.** The product names the cold-start situation honestly. Headspace: "What brings you to Headspace?" — one question, short list, the answer becomes a routing signal, not a stored personality label. Stillpoint version: every editorial default ships with a short concrete subtitle that names what the system used to produce the guess. ("We don't know your kitchen yet — here's a Tuesday-night option most households in your area cook." "We don't have your transactions yet — here's roughly where a household your size lands in a typical week.") The "we don't know you yet" frame is the trust signal. *Carries forward: Spotify Daylist state-label pattern (T2); Humble Product Design "I don't know yet" (T4); Stillpoint operating principles §3 product voice (no pretending to know). Surface: every editorial-default subtitle. Principle: §3 product voice; §6 honest about what we don't know.*

**F5 — Privacy nutrition card at install + on first sensitive surface.** A one-screen, plain-language disclosure of exactly what the product does and doesn't collect, shown at install time and shown again the first time the user touches a sensitive surface (bank-connect for Troth, household-member-add for Ingle). Adapt Apple's App Store nutrition-label format but with Stillpoint's specific honesty: no third-party trackers, no microphone, no camera, no surveillance disguised as personalization. The card names the architectural reasons why these claims are reliable — not just the policy promise. *Carries forward: Apple Privacy Nutrition Labels study (T3); Signal architectural-impossibility-of-betrayal (T5); Proton aligned-business-model (T5); Stillpoint operating principles §8 anti-surveillance + closing rule "data flow stays user-facing." Surface: install screen + first sensitive surface. Principle: §8 anti-surveillance; §10 moat is the principles.*

**F6 — Reversibility-as-trust: visible export-on-demand.** Every product surface that holds user data has a visible "export this" affordance — not buried in settings, present at the surface itself. The user who can leave at any time, with all their data, has been given the trust commitment the Mint sunset failed to honor. Most users never export; the option earns trust on its own. PostHog's self-host story restated for consumer products. *Carries forward: PostHog self-host (T5); Mint sunset cautionary (T7); Stillpoint operating principles §8 (data flow stays user-facing) + §10 (the principles are the moat). Surface: every data-bearing screen. Principle: §8, §10.*

**F7 — Recovery-copy library (shared across products).** A shared microcopy library (e.g. `stillpoint/copy/recovery.ts`) holds vocabulary for failure-and-recovery moments — across product boundaries. Forbidden verbs about the user: missed, failed, broke, behind, off-track, skipped, exceeded, blew, lost. Allowed verbs: moved, slid, rearranged, carried forward, settled at, slid down, we noticed, adjustment. Every plan-deviation surface (Ingle: meal not cooked; Troth: budget exceeded), every onboarding-incomplete surface, every error state routes through it. Sirdeshmukh's problem-solving-orientation dimension is the asymmetrically heaviest trust dimension; recovery moments dominate trust formation. *Carries forward: Sirdeshmukh problem-solving orientation (T1); Ingle F16 reframe-don't-blame (Ingle personalization F16); Troth "failure is not a character issue" frame (Stillpoint operating principles §3, §8). Surface: shared engineering policy across all error/recovery copy. Principle: §3 product voice; §8 anti-shame.*

### Troth-specific features (high-stakes financial trust)

**F8 — Show "what's left this paycheck" before asking for a bank connection.** The bank-connect step is the disclosure-trust gate. Push it past the first session. Day 1 should show a paycheck-period "what's left" estimate from a manual or imported single-amount entry — competence-before-disclosure. Bank-connect appears in session 2-3 with framing that names the architectural reasons it's safe (Plaid handshake; tokenized credentials; never stored by Troth; revocable anytime; real-time alerts on new connections). Robinhood's instant-trade-with-deferred-funding pattern inverted: instant value with deferred disclosure. *Carries forward: Robinhood deferred-funding pattern (T2); Plaid bank-connect handshake (T7); McKnight institution-based trust (T1); Stillpoint operating principles §4 (no required configuration before first value), §6 (data flow stays user-facing). Surface: session 1 home view; session 2+ bank-connect modal. Principle: §6 personalization-from-behavior; §8 anti-surveillance.*

**F9 — Financial-trauma-aware first-session copy.** First-session copy assumes the user has a prior bad experience with at least one financial app (Mint, YNAB, Personal Capital), at least one financial institution (overdraft fees, aggressive collections), or at least one financial situation (debt, scarcity, divorce, layoff). The copy never says "let's get your finances on track," "let's get organized," "you've come to the right place." It says: "the bills are here when you're ready. We'll show you what's left, not what's wrong. Nothing here grades you." The trauma-as-prior-experience evidence makes this the load-bearing first-session safety signal for Troth's target population. *Carries forward: Financial Trauma research (T7); Troth `failure-is-not-a-character-issue.md`; Hilbert et al. 2024 induced-scarcity 6.8x delays via shame mechanism (Troth household-finance-research.md); Stillpoint operating principles §3 product voice + §8 anti-shame. Surface: every first-session text element in Troth. Principle: §3, §8.*

**F10 — Money-meeting weekly review without comparison shaming.** A weekly couples'-money review surface, opt-in, ships in week 2 (after first-session trust has formed, before disclosure-trust peaks). The review surfaces "what we noticed this week" — neutral, household-as-unit-framed observations. Never "you spent more than your partner," never "users like you save more." Dew et al. 2012 found financial arguments are the #1 predictor of divorce; the antidote is regular, structured, short conversations, not avoidance. The structured-money-meeting feature is itself a trust signal: the system gives the household a calmer way to talk about money than they had before. *Carries forward: Troth household-finance-research.md recommendations 13, 16, 21; Dew et al. 2012 longitudinal divorce study (cited in Troth research); Stillpoint operating principles §3 (couple as unit), §7 (predictable bounded check-ins). Surface: weekly Sunday or Saturday morning surface, household-only. Principle: §3, §7.*

### Ingle-specific features (lower-stakes meal/grocery trust)

**F11 — Day-1 dinner suggestion from locale + time-of-day, before any household setup.** Ingle's first session, before invite-spouse, before connect-store, before pantry-add, shows a Tuesday-night dinner suggestion based purely on locale (US Midwest, North Carolina, etc.) and time-of-day. "Most kitchens in your area cooked X this Tuesday — want a starting point?" One tap = "Yes, save this as the kind of thing we eat" or "No, show me another." Two-three taps and the household has a working profile, no questionnaire. Direct application of Pinterest cold-start + Spotify Daylist editorial-default + Mealime minimalist-cold-start trust pattern. *Carries forward: Pinterest 5-tap cold-start (T2); Spotify Daylist locale-and-time editorial default (T2); Ingle personalization F2 free-signal pre-warming, F3 Tonight Card; Stillpoint operating principles §4 (defaults are the product), §6 (data from behavior). Surface: Ingle first session. Principle: §4, §6.*

**F12 — "We don't know your kitchen yet" graduation copy.** Every editorial default in Ingle's first 14 days ships with a subtitle naming the cold-start state: "Starting from defaults — we'll learn your kitchen as you cook." After day 14 (graduation threshold: 6+ confirmed meals or 30+ pantry adds), the subtitle silently shifts to "Based on your last 6 cooks." The user is never told a state-transition happened; they notice the subtitle has stopped naming the cold-start condition. Spotify Daylist's state-label pattern, applied to graduation. *Carries forward: Spotify Daylist state labels (T2); Humble Product Design "I don't know yet" (T4); Stillpoint operating principles §6 (don't pretend to know). Surface: every Tonight Card and meal-suggestion subtitle. Principle: §3, §6.*

**F13 — Witching-hour humility default.** During the 4-7pm local-time witching hour (the highest-stakes EF-depleted moment, the moment Elisabeth gives the product 15 seconds), the Tonight Card ships with the simplest possible default — even simpler than the general editorial default. Cold-start version (first 14 days): from the six low-EF dinner primitives carried forward from Ingle's May 23 research (snack dinner, rotisserie remix, breakfast-for-dinner, sheet pan, freezer MVP, sandwich bar). Steady-state version (post day 14): the household's most-cooked meal that is pantry-feasible. The product never tries to be clever at 5:45pm. Cleverness at low-EF moments is a trust-loss event. *Carries forward: Ingle personalization F6 six-primitive cold-start, F5 state-aware dashboard, F3 Tonight Card; Bollen choice-overload (Ingle T2); Stillpoint operating principles §7 (don't add load); §4 (defaults are the product). Surface: Tonight Card during 4-7pm local. Principle: §4, §7.*

---

## 2. Summary

### What this research tried to answer

The Ingle personalization-mechanics research (May 25, this same day) closed most of the personalization-mechanics gaps but explicitly flagged one open question: in the first session and first 30 days, BEFORE there's enough behavioral data to personalize meaningfully, HOW does a Stillpoint product earn the user's trust well enough to be kept? The Stillpoint operating principles FORBID onboarding questionnaires (Elisabeth abandons them), surveillance-style passive data capture (anti-surveillance principle), and pretending to know things the system doesn't know (anti-shame, the system-as-honest-helper principle). The brief gave 7 HOW questions, all about MECHANISM not phenomenon, and required 5 perspective categories per question. The output had to produce (a) cross-product trust features, (b) Troth-specific features for the financial-stakes asymmetry, (c) Ingle-specific features for the meal-stakes asymmetry, (d) a draft Principle 11.

### Prior research carried forward (Gate 12)

Five prior docs are not background; they are the inputs. Each produces at least one explicit carryforward into this work.

1. **Stillpoint operating principles** (`docs/principles/2026-05-25-stillpoint-operating-principles.md`) — §6 anti-questionnaire and "data flow stays user-facing"; §8 anti-surveillance / no-pretending-to-know; §4 Elisabeth as satisficer who decides in the first session; §10 the moat IS the principles. These are constraints on the trust-building design, not features to be invented. F5, F6, F8, F9, F11 all directly enforce one of these constraints.
2. **Ingle personalization mechanics** (`~/dev/ingle/docs/research/2026-05-25-personalization-mechanics/analysis.md`) — §4.1 reorder-frequency + time-since-last do ~70% of personalization for groceries; 4-question 60-second triage as the minimum explicit intake; the witching-hour single-card pattern; the state-aware render. F11, F12, F13 build directly on these.
3. **Troth household-finance-research** (`~/dev/troth/docs/research/household-finance-research.md`) — state-dependence framework (baseline Glancer becomes Avoider under stress); no-triage decision; dimensions surface from behavior; financial harmony (not joint accounts) is the relationship-protecting mechanism; the system-as-guardian / exception-based alerts model; ADA CGM trust formation; Sicherman et al. 9.5% login drop after market declines. F8, F9, F10 build on these.
4. **Troth `failure-is-not-a-character-issue.md`** (referenced in operating principles §3, §8, §10) — financial avoidance is a state with a physiological substrate, not a character defect; Hilbert 2024 induced-scarcity 6.8x more payment delays through shame mechanism; the language guide (we noticed, the books, what's there). F7, F9 directly apply this work.
5. **Ingle positioning refresh** (`~/dev/ingle/docs/research/2026-05-22-positioning-refresh/analysis.md`) — the two-buyer ICP (Claude power-user installer vs. Logistics Laura daily user); the trust gap between channel-buyer and daily-user; the PWA companion as the satisficer-spouse surface; the family-table-not-logistics-brain framing. F11 and F13 specifically design for the daily-user trust gap.

### The 5 most important findings

**1. The two cold-start problems share a name but require different solutions.** "Cold start" in recommender systems means *the system doesn't have enough data about the user*. "Cold start" in trust formation means *the user doesn't have enough data about the system*. They share the same first 30 days; they require different solutions. Recsys cold-start is solved by editorial defaults + locale pre-warming + hybrid filtering. Trust cold-start is solved by competence demonstrations + structural assurance + transparent humility. Most products solve one and assume they've solved both. Stillpoint must solve both, AND respect the constraint that the two solutions cannot fight each other. (§3, §4.1)

**2. Sirdeshmukh's problem-solving dimension is the asymmetric one — failure events dominate trust formation.** Operational competence on a good day is required but not durably trust-building. Operational benevolence (the system has my interests in mind) is required but not durably trust-building. The dimension that DOMINATES durable trust is problem-solving orientation — how the system handles failure, recovery, plan-deviation, and the moments when the user is confused or in over their head. This is why Stillpoint's recovery-copy library (F7) is the highest-leverage cross-product trust feature, and why the Troth financial-trauma-aware copy (F9) is the highest-leverage Troth-specific feature. (§4.1)

**3. "We don't know you yet" is a trust signal, not a competence deficit — but only if paired with a visibly competent editorial default.** Headspace's "what brings you to Headspace?" is humble (one question, short list, doesn't pretend to know more). Spotify Daylist's "pumpkin spice rainy morning" is humble (names what the system inferred about the user's state, in a tone that says "if I'm wrong, skip and I'll learn"). Humble Product Design's "I don't know yet" is humble. The pattern works ONLY when the user gets a competent default first and the humility names the boundary of what the default is based on. Humility without a competent default is incompetence; competence without humility is overreach. The two must ship together. (§3, §4.4)

**4. Trust is asymmetric: a single failure costs more than a hundred successes earn.** DuckDuckGo grew to 100M searches/day on the privacy narrative and lost measurable trust after one widely-reported tracker incident. Mint earned trust at 15M users and lost it at sunset (business-model drift). Yummly earned 7 years of household kitchen trust and lost it the day Whirlpool laid off the team. The asymmetry produces a clear design constraint: the architectural choices that PREVENT trust-loss events are more important than the trust-signaling choices that BUILD trust. Signal's Sealed Sender (architectural impossibility-of-betrayal), Proton's open-source, Plaid's tokenized handshake — these are the load-bearing trust mechanisms. Visible privacy policies are secondary. (§4.5)

**5. Trust evolution has phases — and disclosure trust comes AFTER retention trust, often by weeks.** The longitudinal trust-development literature shows trust progresses through formation -> facilitation -> establishment -> acceleration -> reinforcement stages, with different cues dominant at each stage. The Stillpoint operational implication: don't ask for the bank connection on day 1. Don't ask for household member emails on day 1. Don't ask for grocery-store account credentials on day 1. Show competence-without-disclosure first; let the user cross the disclosure threshold on their own timeline, by week 2 or 3, when the trust has reached the establishment stage. This inverts the usual financial-app pattern (Mint, YNAB) of asking for everything in session 1. (§4.6)

### Where the evidence is loudest (consensus)

- The first-session retention curve is steep and well-replicated across categories (D3 -77%; D30 ~5.7% baseline). [§4.1]
- McKnight + Sirdeshmukh + Fogg jointly define the trust mechanics: three dimensions, three formation mechanisms, two-stage assessment. Widely cited; widely replicated. [§4.1]
- Editorial defaults outperform option-grids at low-EF / low-involvement moments. Bollen, Thaler, Iyengar; Headspace, Spotify, Pinterest convergent practitioner evidence. [§4.2]
- "Architectural impossibility-of-betrayal" beats "policy promise" for privacy-first trust. Signal, Proton, the privacy-theater critique all converge. [§4.5]

### Where credible sources disagree (contested)

- **Trust as an emotional bond vs. trust as a synonym for reliability.** The Deloitte / Saul Kaplan / "trust is overrated" contrarians argue what we call "trust" is mostly reliability + expected performance; the academic literature treats trust as a distinct psychological construct. Stillpoint's resolution: BOTH. Reliability is the load-bearing requirement (you cannot ship anti-shame copy on a buggy app); trust signaling is the differentiator that earns the satisficer-Elisabeth user who needs both. [§4.1, §4.5]
- **Humility-frame vs. confident-default frame.** Some practitioner sources advocate for humble framing ("we're still learning"); others argue confident-default frames win for satisficers ("here's the answer"). Resolution: confident in the default; honest about the input. "Most households in your area cook this on Tuesday — want to start here?" is both confident (here's the answer) and honest (we're using locale data, not knowing-your-household data). [§4.4]
- **Explicit interview vs. zero-tap onboarding.** Pinterest's 5-tap interest selector argues for tiny explicit input; Spotify Daylist argues for zero. Stillpoint's resolution: zero is acceptable when locale + time-of-day produce a non-embarrassing default; tiny is required when the locale signal is too thin (e.g., Troth's income-level requires either zip-code-median or a single-tap household-size estimate). [§4.1, §4.3]
- **Disclosure threshold timing.** YNAB's "do the work up front; trust the process" argues for high-friction early disclosure. Robinhood's "trade first, fund later" argues for deferred disclosure. Stillpoint's resolution: Robinhood-side. The principles forbid front-loading friction, and the disclosure trust evidence shows trust crosses the disclosure threshold weeks after the retention threshold. [§4.6, §4.7]

### Where the evidence is thin (gaps)

- **No primary research on household-product (vs. individual-product) cold-start trust formation.** McKnight and Sirdeshmukh study individual consumer trust; Daminger's anticipation-labor work studies household cognitive load. There is no peer-reviewed work specifically on how a tool earns trust from BOTH spouses in the first 30 days. F10 (money-meeting) and F11 (day-1 dinner) are designed against first principles + the closest analogs.
- **No primary research on the "trauma-aware first-session copy" effect on retention.** The trauma framing is clinically well-established; its operationalization in product copy is an open empirical question. F9 is a first-principles design; the A/B should be the first beta validation move.
- **No clean separation between "kept the app" trust and "act on the system's suggestion without confirmation" trust.** The longitudinal trust-development literature names the stages; the consumer-product literature mostly measures the first one. Stillpoint will likely surface its own data on stage transitions through beta telemetry.

### The one thing to remember

**Show one good thing first; name what you used to produce it; let the user correct it silently; never pretend to know more than you do.** That single sentence cashes out every operating principle at the line-of-code level. F1 through F13 are different surfaces applying the same rule. The rule itself is the answer to "how does a Stillpoint product earn trust in the first session" — and it works precisely because the alternative (ask 20 questions, surveil, pretend to know) is forbidden by the principles.

---

## 3. Framework — The Cold-Start Trust Compact

**Single-sentence summary:** *Cold-start trust is a contract between the product and the user — competence delivered immediately, identity revealed gradually, failure handled gracefully — with the architecture making betrayal impossible, not just unlikely.*

### The three contract terms

The Cold-Start Trust Compact is a 3-clause contract the product implicitly offers the user in the first session and continues to honor over the first 30 days. Each clause maps to one of Sirdeshmukh's trust dimensions and addresses one of McKnight's trust-formation mechanisms.

| Clause | What the product commits to | Sirdeshmukh dimension | McKnight mechanism it satisfies |
|---|---|---|---|
| **Competence-first** | "I will deliver a unit of my promised value BEFORE I ask you for anything. The first interaction will be useful, not interrogative." | Operational competence | Social categorization (looks like products that respect users) |
| **Identity-revealed-gradually** | "I will not pretend to know you yet. I will use only the signals you've given me or that are free, and I will name what I'm using. You can correct me silently, anytime." | Operational benevolence | Institution-based (situational normality + structural assurance) |
| **Failure-handled-gracefully** | "When the plan doesn't happen, when something goes wrong, when you come back after a hard week — I will reduce load, not escalate. I will speak in your voice, not in scolding. I will be there when you come back." | Problem-solving orientation | Disposition (the user's baseline shifts toward trust over time) |

### The diagram

```
                      +-----------------------------+
                      |     The Trust Compact       |
                      |   (3 clauses, 30 days)      |
                      +--------------+--------------+
                                     |
                  +------------------+------------------+
                  v                  v                  v
        +-----------------+ +-----------------+ +-----------------+
        | Competence-     | | Identity-       | | Failure-        |
        | first           | | revealed-       | | handled-        |
        |                 | | gradually       | | gracefully      |
        | "Use it; I'll   | | "I'm starting   | | "Plan slid;     |
        |  deliver value  | |  from defaults; |  | next dinner    |
        |  before asking  | |  help me learn  | |  is the easy    |
        |  anything."     | |  you."          | |  one."          |
        +--------+--------+ +--------+--------+ +--------+--------+
                 |                   |                   |
                 v                   v                   v
              F1, F3              F2, F4, F12         F7, F13
              F8 (Troth)          F5, F8, F11         F9, F10 (Troth)
              F11 (Ingle)         F6                  F13 (Ingle)
                 |                   |                   |
                 +-------------------+-------------------+
                                     v
                  +----------------------------------+
                  |   Architectural backing          |
                  |   (the compact must be           |
                  |    enforced by ARCHITECTURE,     |
                  |    not by promise alone)         |
                  |                                  |
                  |   - No third-party trackers      |
                  |   - No mic / camera              |
                  |   - Data export on demand        |
                  |   - User-facing data flow only   |
                  |   - Privacy nutrition card       |
                  +----------------------------------+
```

### Why this is the right framework

- **It cashes out into clauses, not principles.** A principle ("don't surveil") is honored or violated abstractly; a clause has a counterparty and a commitment. The user is the counterparty.
- **It maps onto the 30-day curve.** Competence-first ships in the first 60 seconds; identity-revealed-gradually plays out across days 1-14; failure-handled-gracefully gets its first big test usually around day 7-21 when the first plan deviation happens.
- **It is asymmetric in the right way.** The architectural backing protects against trust-collapse events more than it builds trust through claims. This matches the empirical asymmetry (one failure costs more than many successes earn).
- **It is operationally testable.** Every recommendation in §1 implements one clause on one surface. The compact tells you whether each ship is on plan.

### How this framework compares to existing models

- **Sirdeshmukh** gives the three dimensions; the Compact assigns each dimension to a contract clause and gives it a surfaceable test.
- **McKnight** gives the three formation mechanisms; the Compact matches each mechanism to the clause that satisfies it.
- **Fogg's Prominence-Interpretation** is upstream: the clauses must be PROMINENT (visible in the surface) AND INTERPRETED POSITIVELY (the language has to land). The Stillpoint brand-voice work is the Interpretation-side investment; the trust-card and loading-screen value delivery are the Prominence-side investment.
- **Cold-start recsys frameworks** are downstream: editorial defaults, locale pre-warming, hybrid filtering are HOW the Competence-first clause gets delivered in the first session.

### Limitations and boundary conditions

- **The compact assumes the user is capable of forming initial trust.** Users with severe financial trauma may need additional structural assurances (referral from a trusted source, peer testimony, slow trust escalation across weeks rather than days). The compact is necessary but not sufficient for this population.
- **The compact assumes the architectural backing is real.** If Stillpoint ships a product that nominally honors the compact but secretly violates it (e.g., adds a third-party tracker for marketing analytics), the asymmetric-collapse property activates and the entire compact loses force across the portfolio. The principles are not aspirational; they are the architectural enforcement.
- **The compact does not address adversarial users.** Some users are testing the product to see whether the architectural claims hold. The compact welcomes these users; the architectural backing is the test passing.

---

## 4. Analysis

### 4.1 HOW do users actually decide whether to trust a new app in the first session?

**Research question:** What signals matter in the first 60 seconds, first 5 minutes, first session? What's the gap between "use again" and "trust with sensitive data"?

**What the evidence says:** Three jointly-load-bearing academic frameworks converge. **Sirdeshmukh** [t1-sirdeshmukh-trust-dimensions.md, composite 8.40, Level 3] says trustworthiness is competence + benevolence + problem-solving orientation; the problem-solving dimension is the asymmetric one. **McKnight** [t1-mcknight-initial-trust-web.md, 8.10, Level 3] says initial trust forms through three independent mechanisms — disposition, institution-based, social categorization. **Fogg** [t1-fogg-prominence-interpretation.md, 7.95, Level 7] adds the assessment-stage model: prominence x interpretation; both must fire. The empirical curve [t1-mobile-30-second-first-impression.md, 5.65, Level 5] is steep — 77% of users gone by day 3; activation-within-3-minutes apps see 2x higher retention. The trust-as-onboarding-dropoff observation [t1-trust-is-onboarding-dropoff.md, 5.30, Level 7] flips the usual UI-dropoff diagnosis: lack of trust, not UI flaw, is often the actual cause. The contrarian voice [t1-trust-is-overrated-contrarian.md, 6.05, Level 7] argues that what we call "trust" is mostly reliability + expected performance; capability dominates behavior even when stated preferences emphasize humanity.

**Where sources agree:** The first session decides retention. Trust formation in that session has three independent components (disposition + institutional + categorization) that the product can influence two of (institutional via structural signals; categorization via brand voice and design). The fastest-loading-screen-to-first-value time wins; even 100ms latency matters.

**Where sources disagree:** Trust-as-an-emotional-bond vs. trust-as-a-synonym-for-reliability. The academic literature treats trust as a distinct psychological construct with its own measurable dimensions; the practitioner contrarians (Kaplan, Deloitte) argue users' behavior is driven by reliability and that "trust" is a marketing-department abstraction. The Stillpoint resolution is both-and: reliability is the load-bearing requirement (the product MUST work); trust signaling is the differentiator that earns the satisficer-Elisabeth user.

**What's missing:** No primary research on household-product (vs. individual-product) cold-start trust. The longitudinal trust-development literature focuses on dyadic / individual relationships; the household-as-counterparty case (two spouses, possibly children) is underexplored.

**Institutional vs. ground truth:** Aligned. Academic Sirdeshmukh / McKnight / Fogg, institutional Deloitte, and practitioner Kaplan all reach the same operational conclusion through different vocabularies — competence-and-reliability dominates; signaling helps at the margin.

### 4.2 HOW does Stillpoint earn enough trust to keep the user past the first session, given no surveys and no surveillance?

**Research question:** What first-session experiences signal competence + values + safety without requiring disclosure and without pretending to know things the system doesn't?

**What the evidence says:** Six convergent case studies. **Headspace** [t2-headspace-magic-moment-onboarding.md, 7.05, Level 5] delivers value in the loading screen (breath exercise), uses one short-list question, ships the Basics 10-day course with size-of-commitment user choice (3/5/10 min). **Calm** [t2-calm-breathing-onboarding.md, 5.85, Level 7] corroborates with the same loading-screen pattern + atmosphere as competence proof. **Spotify Daylist** [t2-spotify-daylist-cold-start.md, 6.30, Level 4] is the canonical "editorial default + state label + silent correction" pattern. **Pinterest** [t2-pinterest-5-interest-onboarding.md, 7.10, Level 7] does minimal-explicit (5 taps) + locale pre-warming. **Netflix** [t2-netflix-taste-community.md, 6.95, Level 4] is invisible-inference / visible-results via taste communities. **Robinhood** [t2-robinhood-instant-trade-plaid.md, 5.55, Level 4] inverts the trust contract — instant value, deferred funding/disclosure. **Tinder/Bumble** [t2-tinder-bumble-verification-trust.md, 5.70, Level 7] handles bidirectional disclosure (prove you, prove us) with explicit retention timelines.

**Where sources agree:** Deliver value before asking. Editorial defaults beat option grids. Locale + time-of-day pre-warming earns activation lift cheaply. The trust signal IS the product behaving well, not separate marketing copy.

**Where sources disagree:** Whether to ASK ANYTHING vs. infer everything. Headspace asks one question; Spotify Daylist asks none; Pinterest asks 5 taps; YNAB asks for everything. The trade-off is between "explicit cohort-routing for better day-1 defaults" and "zero-friction first session." The Stillpoint position: ask nothing in T0; ask one thing if the free signals are too thin to produce a non-embarrassing default; never ask 4+ things.

**What's missing:** No primary research on whether the editorial-default-with-named-input subtitle ("based on what most households in your area cook on Tuesday") earns more first-session trust than the same editorial default with no input subtitle. F12 is designed against first principles; a beta A/B would settle it.

**Institutional vs. ground truth:** Aligned. The patterns ship convergently across academic recsys literature, institutional UX guidance (NN/G defaults work), practitioner case studies (Headspace, Spotify, Pinterest), and boots-on-the-ground reviewers (Mealime users, dating-app users).

### 4.3 HOW does Stillpoint handle the first-30-days cold-start window where behavioral data is thin?

**Research question:** Editorial defaults, locale pre-warming, public-data pre-fills, one-tap confirmations, social-proof anchors — which work, which fail, in what combination?

**What the evidence says:** The recsys-cold-start literature [t3-cold-start-strategies-recsys.md, 6.95, Level 3] documents five canonical strategies: popularity-based, demographic/locale-based, interview-based, hybrid, transfer-learning. The smart-defaults-and-pre-fill literature [t3-smart-defaults-prefill-onboarding.md, 5.50, Level 7] adds the operational rule (Shopify "default to the 95% choice"; pre-fill from free signals — timezone, locale, email-domain industry). The Apple privacy-nutrition-label study [t3-apple-privacy-nutrition-labels.md, 7.60, Level 3] shows that an explicit at-install-time disclosure can be a powerful cold-start trust device — but only if backed by real architectural choices.

**Where sources agree:** Editorial defaults are the dominant cold-start strategy in production recsys; "popularity-based fallback for new users" is the universal baseline. Free signals (locale, language, timezone) provide enough information for non-embarrassing defaults across most consumer categories. Drop-off accelerates past question 10 in onboarding; 3-question initial setup completes at >90% vs. 60% for 10-question.

**Where sources disagree:** Whether 30 days is the right cold-start window or whether it's product-specific. Spotify's Daylist warms up in days; Netflix's taste community warms up in hours; YNAB's "trust the process" implicitly says cold-start lasts months. The Stillpoint resolution: define the cold-start window per surface, not per product. The Tonight Card cold-start is 14 days (Ingle); the bank-connect cold-start is 21+ days (Troth, until the first paycheck cycle is observed).

**What's missing:** No primary research on the trade-off between (a) accepting more day-1 errors by inferring from locale alone vs. (b) accepting more day-1 friction by asking the 4-question Ingle triage. The 4-question triage is the current Ingle answer; the locale-only-zero-tap is a possible alternative that should be A/B tested in beta.

**Institutional vs. ground truth:** Mostly aligned. The academic / institutional / practitioner sources converge on editorial-defaults + locale + free-signal pre-fill. The contrarian voice in this topic is implicit: some users WANT to configure a lot (maximizer-spouses); the Stillpoint scoped-per-user view (Ingle F21) handles this without imposing it on satisficers.

### 4.4 HOW does "I don't know you yet" get communicated WITHOUT breaking editorial-defaults confidence?

**Research question:** The tension: editorial defaults work when the system speaks confidently. But day-1 confidence with no behavioral data = lying. The user smells it.

**What the evidence says:** Spotify Daylist solves it via the STATE LABEL — "pumpkin spice rainy morning" is humble-but-playful naming of what the system inferred [t2-spotify-daylist-cold-start.md, 6.30]. Headspace solves it via the ONE QUESTION pattern — "what brings you to Headspace?" makes the cold-start state explicit and gives the user a routing handle [t2-headspace-magic-moment-onboarding.md, 7.05]. The Humble Product Design literature [t4-humble-product-design.md, 5.50, Level 7] names the linguistic move directly: "I don't know" -> "I don't know yet" — humility as commitment to future learning, not deficit. Krasotin (T3 source) emphasizes that the WRONG default presented confidently destroys trust faster than no default. The Deloitte trust-research [t6-trust-emotional-vs-functional-deloitte.md, 6.55, Level 5] shows that what users say they want (humanity, transparency) doesn't predict behavior; competence + reliability do.

**Where sources agree:** Confident in the OUTPUT, honest about the INPUT. The product can say "here's the answer" if and only if it can also say "here's what I used to produce it." The two together produce trust; either alone produces either incompetence-feel or deception-feel.

**Where sources disagree:** Whether to surface the input subtitle at all. Spotify surfaces a label; Netflix does NOT surface the taste-community. Both work, at different points on the trade-off. The Stillpoint resolution: surface the input subtitle EARLY (first 14 days) when the trust contract is being established; graduate to no-subtitle when the household profile has formed.

**What's missing:** No A/B data on whether the "based on what most households in your area cook on Tuesday" subtitle outperforms a no-subtitle or a generic-subtitle version. Beta testing should settle this.

**Institutional vs. ground truth:** Aligned. The pattern is convergent across Spotify, Headspace, Calm, and the humble-design discourse.

### 4.5 HOW do anti-tracking / privacy-first products earn trust without surveillance signals?

**Research question:** DuckDuckGo, ProtonMail, Signal, Posthog (self-host) — is "we don't track you" enough alone, or paired with competence demonstrations?

**What the evidence says:** Signal [t5-signal-trust-no-surveillance.md, 7.80, Level 7] earns trust through ARCHITECTURE — Sealed Sender makes server compromise unable to reveal sender-recipient pairs. The architecture makes betrayal IMPOSSIBLE, not just unlikely. Proton [t5-proton-trust-through-architecture.md, 6.45, Level 7] earns trust through aligned business model + open source + independent audits + named founders + transparency reports. DuckDuckGo [t5-duckduckgo-privacy-trust.md, 5.95, Level 7] is the mixed case — strong claims, weaker architectural backing; the 2022 Microsoft-tracker incident produced measurable trust hit. The privacy-theater contrarian voice [t5-privacy-theater-contrarian.md, 7.10, Level 7] is the critical frame: performative privacy without architectural backing collapses trust in BOTH the offending product AND adjacent privacy claims. PostHog [t5-posthog-self-host-trust.md, 5.95, Level 7] earns trust by making self-host AVAILABLE even if most users use cloud — reversibility as a structural trust signal.

**Where sources agree:** Architecture beats policy. Verifiability beats assertion. Aligned business model is itself a structural trust signal. Trust-collapse events are asymmetric — one violation costs more than many successes earn.

**Where sources disagree:** Whether the trust-collapse asymmetry is recoverable. Signal has recovered from controversies (CIA / Snowden); DuckDuckGo's recovery is partial; Mint never recovered. The variable seems to be whether the architectural backing was already strong enough that the violation looked like an isolated lapse vs. a structural pattern.

**What's missing:** No empirical study of consumer-product trust recovery after a publicized violation. The cases (DuckDuckGo 2022, Mint 2010, various) are individual; no meta-analysis.

**Institutional vs. ground truth:** Aligned. Academic legal-scholarship (Skinner-Thompson), institutional (Slate journalism, ACM CHI), practitioner (Signal, Proton, Plaid, PostHog), and contrarian (privacy-theater) sources all converge on architectural-backing-required.

### 4.6 HOW does trust evolve day 1 -> day 30 -> day 90?

**Research question:** Trusted-enough-to-retain -> trusted-enough-to-disclose-sensitive-context -> trusted-enough-to-act-on-system-suggestions-without-confirmation. What's the empirical curve? What product moves move users along it?

**What the evidence says:** The longitudinal trust-development literature [t6-trust-development-longitudinal.md, 7.45, Level 3] documents 5 stages: formation -> facilitation -> establishment -> acceleration -> reinforcement. Benevolence and competence remain significant across all stages; rule-based trust declines over time (institutional shortcut replaced by direct experience). Disclosure behavior marks the formation -> establishment transition. The retention-curve benchmarks [t6-retention-curve-benchmarks.md, 6.05, Level 5] anchor the empirical milestones: D1 retention 25% baseline (fintech 25-35%); D30 5-7% baseline (fintech 10-15%); D90 SaaS PMF 40-60%, consumer apps 5-15%. The aha-moment vs. activation literature [t6-aha-moment-activation-northstar.md, 6.20, Level 5] gives the operational handle — activation predicts retention; each product has ONE activation metric. The Deloitte stated-vs-revealed-preference work [t6-trust-emotional-vs-functional-deloitte.md, 6.55, Level 5] reinforces that capability + reliability dominate the long-arc trust evolution.

**Where sources agree:** Trust evolves through phases. Disclosure trust LAGS retention trust. The day-1 activation is the leading indicator; day-30 is the durable-PMF indicator; day-90 is the long-trust indicator. Different cues dominate at different stages.

**Where sources disagree:** Whether the stages are universal or product-specific. Swift Trust Theory has 5 stages; some practitioner aggregations have 3 (formation / habit / durable); some have 4. The phasing varies by category but the SHAPE (early-disposition-and-institution-dependent -> later-direct-experience-dependent) is consistent.

**What's missing:** No primary research on the consumer-product analog of disclosure trust. Most disclosure-trust literature is in enterprise / organizational settings (newcomer socialization, virtual teams). Stillpoint will likely surface its own data through beta telemetry — at what day does the household typically connect a bank account? At what day does the household first accept a system-generated meal suggestion without modification?

**Institutional vs. ground truth:** Mostly aligned. The academic stage models map onto the practitioner activation / retention frameworks with vocabulary differences. The gap is in measurement — academic research measures self-reported trust; practitioner research measures behavior; the correlation is high but not 1:1.

### 4.7 HOW does trust differ between Troth (financial, high stakes, trauma common) and Ingle (meal/grocery, lower stakes)?

**Research question:** Same principles, different trust calculus. What changes?

**What the evidence says:** The financial-trauma literature [t7-financial-trauma-shame.md, 7.40, Level 6] establishes that a meaningful fraction of Troth's target population brings prior bad experiences (with financial institutions, prior financial apps, or financial situations) to the first session. The Plaid bank-connection architecture [t7-plaid-trust-bank-connection.md, 5.55, Level 7] is the institutional-trust transfer mechanism for the disclosure-trust event Troth most needs. The Mint trust history [t7-mint-trust-history.md, 5.95, Level 7] is the cautionary tale — initial trust earned via simplicity + free + visibility; durable trust lost via business-model drift. YNAB's pattern [t7-ynab-onboarding-trust-budget.md, 5.50, Level 7] is the opposing-frame case — high initial friction + "trust the process" rule-based trust. The meal-planning category trust patterns [t7-mealime-plan-to-eat-yummly.md, 5.40, Level 7] show much lower trust stakes: Mealime's wedge is minimalism + a usable free tier; Plan to Eat's wedge is family / multi-cook + survivor's reputation; Yummly's 2024 shutdown is the meal-planning analog of the Mint sunset.

**Where sources agree:** The trust math IS different. Troth must earn disclosure trust (bank connection, household income, debt history); Ingle's disclosure asks are lower-stakes (meal preferences, dietary exclusions, household member names). Both products must honor the same operating principles; the SURFACE manifestations differ.

**Where sources disagree:** Whether the financial-trauma frame should be EXPLICIT in Troth's first-session copy (F9 says yes; some practitioner sources worry about over-priming). The Stillpoint resolution: explicit but quiet. Not "we know you've had a hard time with money"; instead "the bills are here when you're ready. Nothing here grades you." The trauma awareness is in what the copy DOESN'T say ("let's get organized," "let's tackle this"); not in what it DOES say.

**What's missing:** No empirical study of household-product trust formation specifically in households where ONE spouse has financial trauma and the other doesn't. Troth will likely encounter this case; the architecture (per-user view scope on shared state, Ingle F21 carried over) is the design preparation.

**Institutional vs. ground truth:** Aligned. Clinical / institutional sources (AFCPE, OneUnited, NCBI PMC) and practitioner sources (Plaid, Mealime) converge.

---

## 5. Research

### Topic 1 — First-session trust decision mechanics

- **Sirdeshmukh, Singh & Sabol 2002 — Consumer Trust, Value, and Loyalty in Relational Exchanges** [composite 8.40, Level 3]: trustworthiness is competence + benevolence + problem-solving orientation; problem-solving is the asymmetric dimension. -> `t1-sirdeshmukh-trust-dimensions.md`
- **McKnight, Choudhury & Kacmar 2002 — Initial Consumer Trust on the Web** [composite 8.10, Level 3]: initial trust forms from disposition + institution + categorization, three independent mechanisms. -> `t1-mcknight-initial-trust-web.md`
- **Fogg — Prominence-Interpretation Theory** [composite 7.95, Level 7]: trust assessment is prominence x interpretation; both must fire; user involvement modulates prominence. -> `t1-fogg-prominence-interpretation.md`
- **30-second first-impression aggregation** [composite 5.65, Level 5]: average app loses 77% in 3 days; D30 5.7%; 100ms latency matters. -> `t1-mobile-30-second-first-impression.md`
- **Trust as hidden onboarding dropoff** [composite 5.30, Level 7]: not UI flaw, lack of trust is often the real onboarding blocker. -> `t1-trust-is-onboarding-dropoff.md`
- **Trust-is-overrated contrarian (Kaplan, Deloitte)** [composite 6.05, Level 7, Contrarian]: what we call trust is largely reliability + expected performance; capability dominates behavior. -> `t1-trust-is-overrated-contrarian.md`

### Topic 2 — Trust-earning moves at scaled consumer products

- **Headspace — Magic Moment first-session design (Irrational Labs)** [composite 7.05, Level 5]: loading-screen breath; one short-list question; Basics 10-day course; pre-post comparison as Magic Moment. -> `t2-headspace-magic-moment-onboarding.md`
- **Calm — ambient relaxation first session** [composite 5.85, Level 7]: atmosphere as competence signal; multi-select goal; 7 Days of Calm default program. -> `t2-calm-breathing-onboarding.md`
- **Spotify Daylist — cold-start at consumer scale** [composite 6.30, Level 4]: editorial default + state label + silent correction; locale pre-warming. -> `t2-spotify-daylist-cold-start.md`
- **Pinterest — 5-interest value-driven onboarding** [composite 7.10, Level 7]: 5 taps + locale pre-warming -> 5-10% activation lift; onboarding IS the product. -> `t2-pinterest-5-interest-onboarding.md`
- **Netflix — taste community cold-start** [composite 6.95, Level 4]: 2000+ taste communities; invisible inference + visible results. -> `t2-netflix-taste-community.md`
- **Robinhood × Plaid — instant trade with deferred funding** [composite 5.55, Level 4]: inverts trust contract; instant value, deferred disclosure. -> `t2-robinhood-instant-trade-plaid.md`
- **Tinder / Bumble — verification as bidirectional trust** [composite 5.70, Level 7]: prove-you + prove-us in one step; explicit retention timelines. -> `t2-tinder-bumble-verification-trust.md`

### Topic 3 — First-30-days cold-start window mechanics

- **Cold-start strategies in recsys (Wikipedia + MDPI 2023)** [composite 6.95, Level 3]: 5 canonical strategies; editorial defaults + locale + hybrid as the production baseline. -> `t3-cold-start-strategies-recsys.md`
- **Smart defaults + pre-fill onboarding (Shopify 95% rule)** [composite 5.50, Level 7]: default to the choice 95% of users would make; pre-fill from free signals; drop-off past Q10. -> `t3-smart-defaults-prefill-onboarding.md`
- **Apple Privacy Nutrition Labels — adoption + critique** [composite 7.60, Level 3]: install-time disclosure as cold-start trust device; 1/3 of "Data Not Collected" apps mislabel. -> `t3-apple-privacy-nutrition-labels.md`

### Topic 4 — "Don't know you yet" communication without breaking confidence

- **Humble Product Design (Gillis, Monteiro)** [composite 5.50, Level 7]: "I don't know" -> "I don't know yet"; humility as commitment, not deficit; the two-way exchange model. -> `t4-humble-product-design.md`

*(T4 is sparse — only one source-card filed. The pattern is well-supported by T2 sources Spotify Daylist + Headspace, which carry the load for HOW4 alongside Humble Product Design. The synthesis in §4.4 draws on all three explicitly.)*

### Topic 5 — Anti-tracking trust earning

- **DuckDuckGo — privacy as trust signal + Microsoft-tracker controversy** [composite 5.95, Level 7]: 100M/day on privacy narrative; one violation produces measurable trust hit; asymmetric collapse. -> `t5-duckduckgo-privacy-trust.md`
- **Proton — trust through open source + audits + aligned business model** [composite 6.45, Level 7]: structural assurance via verifiability; aligned-business-model = trust argument. -> `t5-proton-trust-through-architecture.md`
- **Signal — trust without surveillance** [composite 7.80, Level 7]: Sealed Sender = architectural impossibility-of-betrayal; non-profit; transparency reports; protocol-adopted-by-competitors as meta-trust. -> `t5-signal-trust-no-surveillance.md`
- **PostHog — self-host as trust signal for developers** [composite 5.95, Level 7]: reversibility / "you can leave any time" as structural trust. -> `t5-posthog-self-host-trust.md`
- **Privacy theater contrarian (Skinner-Thompson UC Davis Law Review; Slate; UX Tigers)** [composite 7.10, Level 7, Contrarian]: performative privacy without architectural backing collapses trust in adjacent claims; asymmetric. -> `t5-privacy-theater-contrarian.md`

### Topic 6 — Trust evolution day 1 -> 30 -> 90

- **Longitudinal trust development (newcomer socialisation 4-wave study + Swift Trust Theory)** [composite 7.45, Level 3]: 5 stages — formation -> facilitation -> establishment -> acceleration -> reinforcement; benevolence + competence persist; rule-based declines. -> `t6-trust-development-longitudinal.md`
- **Retention curve benchmarks (Pendo, UXCam, AppsFlyer 2024-2026)** [composite 6.05, Level 5]: D1/D7/D30/D90 milestones; fintech 10-15% D30; PMF flatness signals durable trust. -> `t6-retention-curve-benchmarks.md`
- **Aha moment + activation as North Star (Amplitude, Userpilot)** [composite 6.20, Level 5]: aha != activation; activation predicts retention; one metric per product. -> `t6-aha-moment-activation-northstar.md`
- **Deloitte — trust orthodoxy challenge** [composite 6.55, Level 5, Contrarian]: stated brand-trust preferences don't predict behavior; capability + reliability do. -> `t6-trust-emotional-vs-functional-deloitte.md`

### Topic 7 — Trust-stakes asymmetry: Troth vs. Ingle

- **Financial trauma + shame literature (Embodied Wellness, OneUnited, AFCPE, NCBI PMC)** [composite 7.40, Level 6]: financial-trauma response is real, common, and distrust-generating; trauma patterns can be unlearned through consistent safe interactions. -> `t7-financial-trauma-shame.md`
- **Plaid — bank-connection trust architecture** [composite 5.55, Level 7]: tokenized handshake; per-connection consent; real-time alerts; revocation; institutional-trust transfer. -> `t7-plaid-trust-bank-connection.md`
- **Mint — trust history and eventual sunset** [composite 5.95, Level 7]: initial trust via simplicity + free + visibility; durable trust lost via business-model drift. -> `t7-mint-trust-history.md`
- **YNAB — trust-the-process onboarding** [composite 5.50, Level 7]: high initial friction + rule-based trust; the opposing pattern to Stillpoint's editorial-defaults model. -> `t7-ynab-onboarding-trust-budget.md`
- **Mealime / Plan to Eat / Yummly — meal-planning trust patterns** [composite 5.40, Level 7]: lower trust stakes; minimalism + survivor's-reputation as the wedges; Yummly sunset as cautionary. -> `t7-mealime-plan-to-eat-yummly.md`

---

## 6. Methodology

### Research Design

**Research questions:** 7 HOW-shaped questions taken from the brief; auto-approved per the brief's instruction.

**Scope boundaries:**
- *In scope:* Consumer-facing software onboarding patterns from 2018 onward; academic literature on online trust (Sirdeshmukh, Fogg, McKnight); trust formation in financial-services contexts; trust formation in low-stakes utility apps; privacy-first product onboarding; cold-start in recsys; editorial defaults; activation-to-retention curves.
- *Out of scope:* Clinical psychology of trust; B2B SaaS trust; deep social-engineering / phishing-trust; pre-2018 product post-mortems unless principles are timeless; voice-only interfaces; web-3 / crypto-wallet trust; AI-chatbot anthropomorphic-trust; developer-tool / IDE trust.

**Target audience:** Noah (primary, founder); future Code agents implementing the cold-start trust features in Troth and Ingle (secondary); future beta users (tertiary, never read but must be able to defend recommendations to them).

**Methodology version:** deep-research v0.1.0 + v3 principles applied throughout.

### Source Discovery

**Search strategy:** Sequential WebSearch queries across the 7 topic areas. >=3 queries per topic; varied framing (factual, evaluative, contrarian, experiential). All 5 perspective categories sought per topic. Triangulation rule enforced: no load-bearing claim from a single source category.

**Search log (summary):**

| # | Topic | Query themes | Sources surfaced | Sources cardified |
|---|-------|--------------|------------------|-------------------|
| 1 | T1 — first-session trust | Sirdeshmukh; Fogg PIT; McKnight; mobile 30-second; trust-overrated contrarian; activation curve | ~50 | 6 |
| 2 | T2 — trust-earning at scaled products | Spotify Daylist; Pinterest 5-interest; Netflix taste sampler; Headspace; Calm; Robinhood; Tinder/Bumble verification | ~60 | 7 |
| 3 | T3 — first-30-days cold-start | Recsys cold-start; smart-defaults pre-fill; Apple privacy nutrition labels | ~30 | 3 |
| 4 | T4 — don't-know-you-yet linguistic | Humble Product Design; "I don't know yet"; honest defaults | ~20 | 1 (plus 2 carried via T2) |
| 5 | T5 — anti-tracking trust | DuckDuckGo; Proton; Signal; Posthog self-host; privacy theater contrarian | ~50 | 5 |
| 6 | T6 — trust evolution day 1/30/90 | Swift Trust Theory; longitudinal newcomer trust; retention curve benchmarks; aha vs activation; Deloitte brand-trust contrarian | ~40 | 4 |
| 7 | T7 — Troth vs. Ingle asymmetry | Financial trauma; Plaid bank-connect; Mint history; YNAB onboarding; Mealime / Plan to Eat / Yummly | ~50 | 5 |
| **Total** |  |  | **~300** | **31** |

**Total sources discovered:** ~300
**Total sources pulled for evaluation:** 31

### Source Evaluation

**Evaluation framework:** 10-dimension credibility rubric (`source-evaluation-rubric.md`).
**Evidence classification:** 9-level hierarchy (`evidence-hierarchy.md`).
**Bias guards applied:**
- Confirmation-bias check on every source (score harder when agreeing; more generously when disagreeing; on dimensions 5/6/8).
- Triangulation rule: no load-bearing claim accepted from a single source category. Each of the 7 HOW questions answered by >=3 of 5 perspective categories.

**Bias-Guard Summary** (aggregates per-card bias-guard checkboxes):

| Bias-guard outcome | Count |
|---|---|
| Agreed with source — scored harder on dims 5, 6, 8 | 11 |
| Disagreed with source — scored more generously on dims 5, 6, 8 | 5 |
| Neutral / no strong reaction | 15 |
| **Total sources evaluated** | **31** |

The 11:5 agree:disagree ratio is moderately tilted toward agreement (less skewed than the May 23 Ingle research's 16:1). Contributing factors: many sources align with the operating-principles thesis (anti-shame, anti-surveillance, editorial defaults). Mitigation: 3 explicit contrarian sources retained as Core (Kaplan trust-overrated, privacy-theater Skinner-Thompson, Deloitte brand-trust); the framework explicitly leads with the contrarian-corroborated insight that competence + reliability dominate trust formation.

### Inclusion/Exclusion Results

**Summary:**

| Category | Count |
|---|---|
| Total sources evaluated | 31 |
| Included — Core | 14 |
| Included — Supporting | 17 |
| Excluded | 0 |
| Overrides applied | 2 (Rule 2 Diversity for contrarians: Kaplan T1, Privacy-Theater T5) |

**Distribution by evidence level:**

| Level | Description | Count |
|---|---|---|
| 1 | Systematic review / meta-analysis | 0 |
| 2 | RCT | 0 |
| 3 | Large-scale observational | 6 |
| 4 | Expert consensus / professional body | 4 |
| 5 | Practitioner case study | 5 |
| 6 | Qualitative research | 1 |
| 7 | Expert opinion / thought leadership | 15 |
| 8 | Anecdotal / personal experience | 0 |
| 9 | Marketing / promotional | 0 |

**Distribution by source category:**

| Category | Included | Excluded |
|---|---|---|
| Academic | 6 | 0 |
| Institutional | 2 | 0 |
| Practitioner | 19 | 0 |
| Boots-on-the-ground | 0 | 0 |
| Contrarian | 4 | 0 |

**Boots-on-the-ground gap noted.** No Reddit / HN / community-forum source-cards were filed. Two searches for community discourse on YNAB/Mint switching produced no usable results. The boots-on-the-ground perspective is INDIRECTLY represented through Mealime/Plan-to-Eat reviewer aggregations (T7) and through the Mint trust-history coverage (which folds in trustpilot data via Troth's prior research). Not a research-blocker for the recommendations, but flagged as a limitation. Future work should add Reddit-r/personalfinance and r/budgeting source-cards specifically for the Troth-side contrarian voices.

**Distribution by credibility score:**

| Score range | Count | Disposition |
|---|---|---|
| 7.0 – 10.0 | 10 | 10 Core |
| 5.0 – 6.9 | 21 | 4 Core (via Rule 3), 17 Supporting |
| 3.0 – 4.9 | 0 | — |
| 0.0 – 2.9 | 0 | — |

### Perspective Balance

| Topic area | Academic | Institutional | Practitioner | Boots | Contrarian |
|---|---|---|---|---|---|
| T1 — first-session trust | Y (Sirdeshmukh, McKnight, Fogg) | N | Y (mobile aggregation, onboarding-dropoff) | N | Y (Kaplan trust-overrated) |
| T2 — trust-earning at scaled products | N | N | Y (Headspace, Calm, Spotify, Pinterest, Netflix, Robinhood, Tinder) | N | N |
| T3 — first-30-days cold-start | Y (recsys MDPI, Apple-labels CHI) | N | Y (smart-defaults) | N | N |
| T4 — don't-know-you-yet linguistic | N | N | Y (Humble Product Design, plus T2 carry) | N | N |
| T5 — anti-tracking trust | Y (Skinner-Thompson law review) | Y (Slate journalism) | Y (Proton, Signal, DuckDuckGo, PostHog) | N | Y (privacy theater) |
| T6 — trust evolution | Y (Swift Trust longitudinal) | N | Y (Pendo, Amplitude) | N | Y (Deloitte brand-trust) |
| T7 — Troth vs. Ingle asymmetry | N | Y (AFCPE, NCBI PMC) | Y (Plaid, Mint, YNAB, Mealime) | N | N |

3 of 5 categories per topic minimum met for T1, T3, T5, T6. T2, T4, T7 lean practitioner-heavy. T4 is particularly thin (1 cardified source); the synthesis draws on T2 carry-throughs from Spotify Daylist + Headspace, which carry the load.

### Limitations

- Web search surfaces SEO-optimized content disproportionately, which under-represents academic sources that don't publish abstracts publicly.
- Paywalled journal articles in this corpus (Sirdeshmukh, McKnight) accessed only via abstracts and second-order coverage; full-text access would refine the credibility scoring marginally.
- Single-evaluator scoring without inter-rater reliability checks.
- No Reddit/HN/forum source-cards filed; the community-voice perspective is INDIRECT through Mealime / YNAB / Mint reviewer aggregations.
- No primary research dollar-figures on the Troth-financial-trauma-aware-copy effect; F9 is designed against the clinical-research substrate plus first principles, not validated.
- The framework draws inferences from a population that includes both individual-user products (Netflix, Spotify, Headspace) and household-products (Ingle, Troth); the household-as-counterparty trust evolution is partially extrapolated.

### Paywalled candidates surfaced

- **Sirdeshmukh, Singh, Sabol 2002 — Journal of Marketing, Vol. 66, 15-37.** Abstract + secondary coverage accessible (Studocu, SciRP reference). Full PDF behind SAGE paywall.
- **McKnight, Choudhury, Kacmar 2002 — Journal of Strategic Information Systems, Vol. 11, 297-323.** Abstract accessible on ScienceDirect. Full PDF behind Elsevier paywall.
- **Fogg 2003 Prominence-Interpretation Theory.** Stanford-hosted PDF publicly accessible (`credibility.stanford.edu/pdf/PITheory.pdf`); NN/G summary publicly accessible.
- **Swift Trust longitudinal (newcomer socialisation).** Academia.edu hosts a copy; ResearchGate access may vary by user.

---

## 7. Bibliography

### Core sources (cited in §4 analysis)

**Sirdeshmukh, D., Singh, J., & Sabol, B.** "Consumer Trust, Value, and Loyalty in Relational Exchanges." *Journal of Marketing*, Vol. 66, pp. 15–37, 2002. Score: 8.40 | Level 3 | Core.
The foundational tripartite-trust model (competence + benevolence + problem-solving) with the problem-solving asymmetric-weight finding that drives the Stillpoint recovery-copy work.

**McKnight, D.H., Choudhury, V., & Kacmar, C.** "The impact of initial consumer trust on intentions to transact with a web site: a trust building model." *Journal of Strategic Information Systems*, Vol. 11, pp. 297–323, 2002. Score: 8.10 | Level 3 | Core.
Three-mechanism initial-trust model (disposition + institution + categorization); the load-bearing answer to HOW1 mechanism breakdown.

**Fogg, B.J.** "Prominence-Interpretation Theory: Explaining How People Assess Credibility Online." Stanford Persuasive Tech Lab / CHI 2003. Score: 7.95 | Level 7 | Core.
The two-stage credibility-assessment model (prominence x interpretation, both must fire); the assessment-side complement to Sirdeshmukh's structural model.

**Apple Privacy Nutrition Labels — Wang et al. 2022 CHI Extended Abstracts** + IDAC Policy Brief + OneTrust comparison. Score: 7.60 | Level 3 | Core.
Large-scale App Store analysis showing 35.3% adoption via app-update enforcement; 1/3 mislabel rate (Washington Post audit); the canonical privacy-label-as-trust-device pattern with the audit asymmetry caveat.

**Swift Trust Theory + 4-wave Longitudinal Newcomer Socialisation Study.** Score: 7.45 | Level 3 | Core.
5-stage trust-development model; benevolence + competence persist across stages; rule-based trust declines; disclosure marks formation -> establishment transition.

**Financial Trauma research aggregation (AFCPE Q2 2026; OneUnited Bank; Embodied Wellness; NCBI PMC10737752).** Score: 7.40 | Level 6 | Core.
Financial-trauma response patterns produce default-distrust of financial tools; trauma patterns can be unlearned through consistent safe interactions; load-bearing for Troth-specific F9.

**Pinterest cold-start onboarding (Casey Winters interview, Appcues case studies).** Score: 7.10 | Level 7 | Core.
5-tap interest selector + locale pre-warming -> 5-10% activation lift across countries; the canonical minimal-explicit cold-start pattern.

**Privacy theater critique (Skinner-Thompson UC Davis Law Review; Slate; UX Tigers).** Score: 7.10 | Level 7 | Core (Contrarian).
Performative privacy without architectural backing collapses trust in adjacent claims; the asymmetric-collapse finding load-bearing for the §3 framework.

**Headspace — Magic Moment first-session design (Irrational Labs case study; Appcues GoodUX).** Score: 7.05 | Level 5 | Core.
Loading-screen value delivery; one short-list question; pre-post comparison as Magic Moment; doubled course-starts from a single behavioral-science change.

**Signal — trust without surveillance (Signal Support; Mozilla Foundation review; VPN Overview).** Score: 7.80 | Level 7 | Core.
Sealed Sender as architectural impossibility-of-betrayal; transparency reports; non-profit alignment; Protocol-adopted-by-competitors as meta-trust.

**Cold-start strategies in recsys (Wikipedia + MDPI Algorithms 2023 survey).** Score: 6.95 | Level 3 | Core.
5 canonical cold-start strategies; editorial defaults as production baseline; recsys-cold-start != trust-cold-start.

**Netflix — taste community cold-start.** Score: 6.95 | Level 4 | Core.
Invisible inference / visible results; 2000+ taste communities; hybrid content + collaborative as the cold-start architecture.

**Proton — trust through architecture + aligned business model.** Score: 6.45 | Level 7 | Core.
Open source + audits + aligned business model = structural trust argument; verifiability beats assertion.

**Spotify Daylist — cold-start at consumer scale.** Score: 6.30 | Level 4 | Core.
Editorial default + state label ("pumpkin spice rainy morning") + silent correction; the canonical naming-the-inferred-state pattern.

### Supporting sources

**Deloitte Insights — Challenging the orthodoxies of brand trust.** Score: 6.55 | Level 5 | Supporting (Contrarian).
Stated trust preferences != revealed behavior; capability + reliability dominate.

**Aha moment + activation as North Star (Amplitude, Userpilot).** Score: 6.20 | Level 5 | Supporting.
Aha != activation; activation predicts retention; operational handle for HOW6 milestones.

**Trust-is-overrated contrarian (Kaplan, UpDoc Media, Influencers-Time).** Score: 6.05 | Level 7 | Supporting (Contrarian).
What we call trust is largely reliability + expected performance.

**Retention curve benchmarks (Pendo, UXCam, AppsFlyer 2024-2026).** Score: 6.05 | Level 5 | Supporting.
D1/D7/D30/D90 benchmarks; fintech 10-15% D30; PMF flatness signal.

**DuckDuckGo — privacy as trust signal + Microsoft-tracker controversy.** Score: 5.95 | Level 7 | Supporting.
100M/day on privacy narrative; one violation produces measurable trust hit; the canonical asymmetric-collapse case.

**Mint — trust history and eventual sunset.** Score: 5.95 | Level 7 | Supporting.
Initial trust via simplicity + free + visibility; durable trust lost via business-model drift; the canonical cautionary tale.

**PostHog — self-host as trust signal.** Score: 5.95 | Level 7 | Supporting.
Reversibility / "you can leave any time" as structural trust signal.

**Calm — ambient relaxation first session.** Score: 5.85 | Level 7 | Supporting.
Atmosphere as competence signal; multi-select goal; corroborates Headspace pattern.

**Tinder / Bumble — verification as bidirectional trust handshake.** Score: 5.70 | Level 7 | Supporting.
Prove-you + prove-us in one step; explicit retention timelines; bidirectional disclosure.

**30-second first-impression aggregation (Setgreet, Tekin, Atticus Li).** Score: 5.65 | Level 5 | Supporting.
D3 -77%; D30 5.7% baseline; 100ms latency penalty.

**Robinhood × Plaid — instant trade with deferred funding.** Score: 5.55 | Level 4 | Supporting.
Trust-contract inversion; instant value, deferred disclosure.

**Plaid — bank-connection trust architecture.** Score: 5.55 | Level 7 | Supporting.
Tokenized handshake; per-connection consent; real-time alerts; institutional-trust transfer mechanism for Troth.

**Smart defaults + pre-fill onboarding (Shopify 95% rule).** Score: 5.50 | Level 7 | Supporting.
Default to the 95% choice; pre-fill from free signals; drop-off past Q10.

**YNAB — trust-the-process onboarding.** Score: 5.50 | Level 7 | Supporting.
High-friction + rule-based trust; the opposing-frame contrast to Stillpoint's editorial-defaults.

**Humble Product Design (Gillis, Monteiro, UX Magazine).** Score: 5.50 | Level 7 | Supporting.
"I don't know" -> "I don't know yet"; humility as commitment to future learning, not deficit.

**Mealime / Plan to Eat / Yummly — meal-planning trust patterns.** Score: 5.40 | Level 7 | Supporting.
Lower trust stakes; minimalism + survivor's reputation as the wedges; Yummly sunset as cautionary.

**Trust as hidden onboarding dropoff (Emre AY, Donna Weber).** Score: 5.30 | Level 7 | Supporting.
Lack of trust, not UI flaw, is often the real onboarding blocker.

### Sources discovered but not cardified (sample)

- Multiple Reddit/HN search results returned thin (search returned "No links found" for r/YNAB-r/Mint specifically); the community-voice gap is documented in §6 Limitations.
- Whirlpool acquisition / shutdown coverage of Yummly (CNBC, etc.) folded into the Mealime / Plan to Eat / Yummly card.

---

## 8. Feedback into Stillpoint Operating Principles — Draft Principle 11

The cold-start trust research surfaces a principle that exists implicitly across Principles 4 (Elisabeth), 6 (Personalization), 7 (Mental Load), and 8 (Anti-Surveillance) but has not been named at the front of the doc. The recommendation is to add Principle 11 — **The Cold-Start Trust Compact** — and to renumber the closing-note section accordingly.

### Proposed Principle 11 text

> ## 11. The Cold-Start Trust Compact
>
> **Principle:** Every Stillpoint product offers the user an implicit three-clause contract from the first session through the first 30 days: competence delivered immediately, identity revealed gradually, failure handled gracefully — with the architecture making betrayal impossible, not just unlikely. The compact is the operational form of the anti-questionnaire and anti-surveillance principles; it is the answer to "how does a Stillpoint product earn the user's trust to be kept, without the moves competitors use."
>
> **Source:** The May 2026 cold-start-trust research (`docs/research/2026-05-25-cold-start-trust/analysis.md`). Synthesizes Sirdeshmukh's tripartite trust dimensions (competence / benevolence / problem-solving), McKnight's three trust-formation mechanisms (disposition / institutional / categorization), Fogg's prominence-interpretation assessment model, the cold-start recsys literature, the Apple privacy-nutrition-label evidence, and the convergent practitioner cases from Headspace, Spotify Daylist, Pinterest, Netflix, Robinhood, Signal, Proton, and the privacy-theater contrarian critique. Maps each clause onto one Sirdeshmukh dimension and one McKnight mechanism. Tested against the Stillpoint operating-principles constraints (no surveys, no surveillance, no pretending to know).
>
> **Why this matters across the portfolio:** Principles 4 (Elisabeth) and 6 (Personalization) tell us WHAT not to do at onboarding (no questionnaires; no self-characterization). Principle 8 (Anti-Surveillance) tells us WHAT not to do for data collection (no third-party trackers, no mic/camera, no sold/shared data flow). What was missing was a positive frame — the affirmative thing the product DOES do to earn trust in the first 30 days. Without that frame, the absence-of-bad-things looks like absence-of-product. The Cold-Start Trust Compact gives the team an affirmative operating commitment that ships features (Competence-first; Identity-revealed-gradually; Failure-handled-gracefully) and a defensible architecture (anti-tracker; data-export-on-demand; privacy-nutrition-card at install + first sensitive surface).
>
> **The three clauses:**
>
> **Clause 1 — Competence-first.** Before the product asks the user for anything (a question, a connection, a credential, a household-member email), the product delivers a unit of its promised value. The loading screen is a value-delivering surface, not a setup screen. The first session's main view is one good editorial answer to the user's primary question (Ingle: "what's for dinner Tuesday in your area"; Troth: "what's roughly left this paycheck in your area"), grounded in concrete locale + time-of-day signals. Maps onto Sirdeshmukh's operational competence and McKnight's social categorization (the product looks like products that respect users).
>
> **Clause 2 — Identity-revealed-gradually.** The product does not pretend to know the user. The first session uses only free signals (locale, language, timezone, time-of-day, household size if inferable) and is honest about what it used. Every editorial default ships with a concrete subtitle that names the input ("based on what most households in your area cook on Tuesday"; "based on what most households your size land at in a typical week"). Disclosure trust (bank-connect, household-member-add, dietary-exclusion-detail) is requested AFTER retention trust is established — usually in session 2-3, not session 1. Maps onto Sirdeshmukh's operational benevolence and McKnight's institution-based trust (structural assurance).
>
> **Clause 3 — Failure-handled-gracefully.** When the plan deviates, when the suggestion is wrong, when the user comes back after a hard week, the product reduces load and speaks in non-shaming language. The shared recovery-copy library is the architectural enforcement: forbidden verbs (missed, failed, broke, behind, exceeded, blew) cannot appear in product copy; allowed verbs (moved, slid, settled at, carried forward) are the substitute vocabulary. Maps onto Sirdeshmukh's problem-solving orientation (the asymmetric trust dimension) and McKnight's disposition (the trust baseline shifts toward trust through repeated graceful recoveries).
>
> **The architectural enforcement (non-negotiable):**
>
> - **No third-party trackers.** Architectural impossibility-of-betrayal, like Signal's Sealed Sender.
> - **No mic, no camera, no clipboard-watching, no off-screen capture of any kind.** Anti-surveillance is structural, not aspirational.
> - **Data export on demand.** The reversibility-as-trust pattern (PostHog self-host, but for consumers). Every data-bearing surface has a visible "export this" affordance, not buried in settings.
> - **Privacy nutrition card at install + first sensitive surface.** One-screen, plain-language, names exactly what is and isn't collected. Apple-pattern with Stillpoint specificity.
> - **Aligned business model documented in the principles.** Subscription not engagement, not data-resale, not ad-monetized — and the alignment is the visible argument that the principles are durable.
>
> **The compact is asymmetric (architecture > policy):**
>
> A single trust-violation event costs more than dozens of consistent-trust events earn. The Mint, DuckDuckGo, and Yummly cautionary tales all share this shape. The implication: invest in the architectural choices that PREVENT trust-violation events more than in the trust-signaling choices that BUILD trust-perception. Visible policy is the floor; architectural impossibility-of-betrayal is the ceiling.
>
> **Do:**
> - Ship Competence-first on every main surface. The loading screen is a feature, not a setup.
> - Pre-warm editorial defaults from free signals before the user makes any choice.
> - Name the input under every editorial default for the first 14-30 days; graduate to no-subtitle when household profile has formed.
> - Defer disclosure trust to session 2-3 minimum. Bank-connect, household-member-add, store-credential-add are NOT session-1 asks.
> - Route every error / recovery / plan-deviation surface through the shared recovery-copy library.
> - Make data-export visible on every data-bearing surface, not buried in settings.
> - Ship the privacy nutrition card at install and again at the first sensitive surface.
> - Treat the Cold-Start Trust Compact as a contract with the user; treat each ship as honoring or violating one or more of its three clauses.
>
> **Don't:**
> - Ship a session-1 questionnaire (cf. §6).
> - Ship a confident editorial default that the system has no signals to produce ("pretend to know" failure mode).
> - Ship a "based on your data" subtitle when the system has no data yet.
> - Front-load disclosure requests (bank, credentials, identity verification) in the first session.
> - Use any forbidden recovery verbs (missed, failed, broke, exceeded, blew) in product copy.
> - Bury data-export, account-deletion, or "remove me" affordances in settings.
> - Ship privacy claims that aren't backed by architectural impossibility-of-violation. Performative privacy is worse than no privacy claim.

*(End of proposed Principle 11. The current closing note becomes the closing note of an 11-principle doc; no other principle numbering changes are required. Cross-references in §10 Moat Thesis and elsewhere should be checked but the principles do not contradict each other.)*

---

## 9. New questions raised — for spin-out research

Each of these is a HOW-shaped question the cold-start-trust work surfaces but does not close. They are candidates for the next research wave.

1. **HOW does household-product trust formation differ from individual-product trust formation?** The academic literature is dyadic / individual; the household-as-counterparty case (two spouses, possibly children, asymmetric trust positions across the household) is underexplored. A study of household-app onboarding outcomes split by which spouse installed first vs. which spouse used the product first would close this gap.

2. **HOW does Stillpoint detect the disclosure-trust threshold crossing in telemetry?** F8 (Troth) defers bank-connect to session 2-3; what telemetry signals indicate the user is READY for the bank-connect ask? The longitudinal trust-development literature names the stage transition but doesn't operationalize the cue. Probably: dwell time + return-without-prompt + first manual data-entry + first override of an editorial default.

3. **HOW does a Stillpoint product recover trust after a violation event?** The asymmetric-collapse evidence is solid; the recovery side is thin. If Troth ships a bug that mis-categorizes a transaction, or Ingle ships a Tonight Card that suggests something deeply wrong (the household just had a death; the suggestion is the deceased's favorite meal), how does the product recover trust without making the violation worse?

4. **HOW does the "I don't know you yet" subtitle land empirically?** F12 (Ingle) ships the named-input subtitle for 14 days; HOW4 evidence is convergent across Spotify, Headspace, and Humble Product Design but is not empirically tested in the household-product context. A beta A/B against three conditions (no subtitle; named-input subtitle; pretend-confident subtitle) would settle the trade-off.

5. **HOW does a financial-trauma-aware first-session copy interact with users who do NOT have financial trauma?** F9 is designed against the trauma-population's needs; the operationalization risks reading as over-priming for the non-trauma user. Beta testing across both populations would inform copy length, tone, and information ordering.

6. **HOW does the multi-spouse trust dimension evolve when one spouse has prior negative experience with the product category and the other does not?** Ingle and Troth both encounter this case (one cooking-app-skeptic + one cooking-app-curious; one financial-app-burned + one financial-app-neutral). The per-user view scope (Ingle F21) is the architectural preparation; the actual experience trajectory needs measurement.

7. **HOW does Stillpoint communicate trust evolution across products in the portfolio?** A user who has trusted Troth for 60 days is approached for Ingle install. Does the cross-product trust transfer? At what depth? The Stillpoint operating principles §9 Cross-Project Learning Discipline names the architecture; the user-side trust transfer is the next research question.

8. **HOW does the architectural-impossibility-of-betrayal principle translate to AI-feature surfaces?** When Stillpoint products use LLM features (Troth's narrative generation; Ingle's meal-suggestion explanation), what architectural choices make those features trust-compatible? The Signal Sealed Sender / Proton open-source patterns suggest answers; the LLM-feature application is open.

---
