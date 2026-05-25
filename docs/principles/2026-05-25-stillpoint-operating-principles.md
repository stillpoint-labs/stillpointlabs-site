# Stillpoint Labs Operating Principles

*Canonical reference, 2026-05-25. Synthesized from the May 2026 positioning research across Troth, Ingle, Reveal, borg-collective, and the noah-voice corpus validation.*

This is the operating manual for everything Stillpoint Labs builds. If a decision is being made about a product surface, a piece of copy, a feature gate, an onboarding flow, a notification, a pricing tier, or a roadmap priority, this document is the reference. When a new product joins the portfolio, it inherits these principles. When an existing product gets a refresh, it gets audited against this document.

The principles are not a wish list. They are pulled from research the team has already done, on real users, against real competitors, in real markets. The work that produced them is cited inline so anyone using this document can trace a principle back to its source and challenge it if the source no longer applies.

---

## 1. Stillpoint's Thesis

Stillpoint Labs builds software that lifts the human and reduces the load. We use technology to make people more capable of the things they already care about, not to convince them they should care about new things. Our products coach, partner, and scaffold; they do not surveil, measure-against, or shame. The household, the family, the person at the keyboard at 11 p.m. is the unit of design, and the question we ask before every feature ships is whether it leaves that unit *more* able to act or less. When the answer is *less*, the feature does not ship, even if the metric goes up.

Everything below is a way of holding that thesis steady when product pressure pushes the other way.

---

## 2. The Brand Voice

**Principle:** We speak in clear English, define every term on first use, and never talk down to the reader. Research informs the *what* and the *why*; ELI10 (Explain Like I'm 10, meaning use words a smart ten-year-old would understand without dumbing the idea down) controls the *how*.

**Source:** The Noah voice rules and the May 2026 corpus validation against 10 published Medium articles. The validation found that across 13,308 words of Noah's actual published work, the voice that produces engagement is conversational, story-first, metaphor-driven, anchored in specific details (numbers, names, ages, dates), and confident enough to admit being wrong on the page. The validation also caught the failure modes: AI-style transitions like *"Here's the thing"*, generic openers, banned buzzwords (*navigate, landscape, leverage, delve*), and drift toward the internet average.

**Why this matters across the portfolio:** Every product surface, every email, every README, every onboarding screen, every notification is a chance to either reinforce the brand voice or dilute it. The voice is not decoration; it is the single asset the larger competitors can't ship even when they try, because their incentives won't let them. Anthropic, Adobe, Monarch, and Cursor can match our features. They cannot match a voice that refuses to engagement-bait its way to retention. That refusal is structural for us and structurally impossible for them.

**Concrete examples from the portfolio:**
- Troth's positioning addendum (`failure-is-not-a-character-issue.md`) leads with the bills piling up on the counter, the unopened mail, the shame spiral. No abstract definition of *avoidance*. The reader recognizes the state in themselves before they read the word for it.
- Reveal's *"developed not inspired"* line tells the reader exactly what the product does in five words. The reader does not need a glossary of AI photography terms to understand the difference.
- Ingle's brand guide names the emotion target as *safety*, not *efficiency*, and the verb is *tend*, not *track*. Both choices are voice work doing positioning work.

**Do:**
- Define every technical term on first use, inline, in plain English. *Executive function (EF, meaning the brain's ability to plan, focus, and switch tasks) drops sharply at the end of a long day.*
- Lead with a concrete situation before the abstract claim. The story sets up the principle; the principle alone reads as a lecture.
- Use specific numbers, names, dates. "84% of households see >5% month-to-month income swings" beats "income is variable."
- Embed observations inside larger thoughts. A short sentence carries more weight when it lives in a paragraph than when it floats alone as a dramatic transition.
- Admit when we don't know something or when an earlier framing was wrong. The corpus validation showed Noah's most-read articles open with admissions.

**Don't:**
- Use *navigate, landscape, leverage, delve, genuinely, honestly, straightforward, to be honest*. The validation traced these as the highest-signal AI tells in scaled content.
- Drop a punchy one-liner as a section transition. *"Here's the thing"*, *"Let's dive in"*, *"That said"*, *"The bottom line is"* are bait. Embed the punchline inside a paragraph that actually delivers it.
- Use bullet-point lists in the middle of a longform essay. Bullets belong in tutorials, comparisons, and explicit call-to-action sections. In prose, convert bullets to flowing sentences.
- Define a bold term as a standalone definition sentence. Bold flows into the paragraph as a visual landmark, not as a vocabulary card.
- Write in the voice of an industry analyst. Write in the voice of a friend over coffee who happens to know the research.

---

## 3. The Product Voice

**Principle:** Stillpoint apps speak to users the way a calm friend speaks to someone in over their head. We coach, we partner, we scaffold. We never judge, never measure the user against an arbitrary ideal, never surveil, never shame. The product is on the user's side, and every word it says has to demonstrate that.

**Source:** Troth's positioning addendum (`failure-is-not-a-character-issue.md`), Ingle's caretaker reframe research (the Elevation-vs-Surveillance axis, Eikey BJPsych Open's 73% of MyFitnessPal users perceiving the app as contributing to their eating disorder, Hartley and Langer's clinical critiques of MyFitnessPal and Noom), and the broader behavioral-finance work showing shame produces 6.8× more payment delays (Hilbert et al. 2024 induced-scarcity experiment).

**Why this matters:** The dominant pattern in consumer-facing software is engagement-through-guilt: streaks, red bars, *you missed your goal today*, comparison shaming, gamified restriction. The pattern produces short-term DAU spikes and long-term retention collapse (food and diet apps die at ~2% retention by 52 weeks per the JMIR mHealth retention study, with the shame loop documented as the largest single mechanism). Stillpoint products live or die on the opposite mechanism: the user trusts the app enough to come back to it after a bad week.

**Concrete examples:**
- Troth never uses red *"over budget"* bars. The language is *adjustment*, not *overspending*. A plan change is treated as expected, not as failure.
- Ingle never says *"you missed your plan today"*. The plan is a guide, not a contract. Drag meals between days, leftovers become tomorrow, the witching hour is a known thing the product is helping the cook through.
- Reveal never tells the user their photograph is bad. The artist statement explains what the system saw and what it developed; the photograph is *theirs*.
- Borg-collective's `adhd-guardrails` skill exists specifically because no vendor can ship shame-free language and work/life boundaries; their token-billed business models punish those features. The skill catches the moments when the user is working past the point where the work is still net positive and says so without scolding.

**Do:**
- Frame variance neutrally. *"Adjustment,"* *"this week ran ~$X,"* *"you're roughly on track,"* *"the answer is already here when you need it."*
- Treat returning users after an absence with simpler views, not backlogs of missed alerts. Silence is reassurance; a stack of *"you missed!"* notifications is the opposite.
- Use predictable, bounded check-ins (Sunday-evening summary, monthly all-clear) instead of constant push notifications. The CGM analogue (silence = health) is the right model.
- Treat the household, family, or person as the unit. *"We noticed,"* *"what's there,"* *"the books,"* not *"you spent."*
- Let the user correct an inference. The product makes a smart guess; the user can override it; the override teaches the system without a quiz.

**Don't:**
- Streaks for compliance behavior (no eating-streak, no spending-streak, no check-in-streak). Streaks weaponize loss aversion against the user.
- Comparison shaming (no *"you spent more than your partner,"* no *"users like you save more"*).
- Calorie counters, deficit tracking, character-trait quizzes, willpower badges, *master your money* gamification. The Ingle caretaker research is explicit on this: ban the calorie surface entirely; the verb is *tend*, never *track*.
- Loss-frame copy. *"You lost streak,"* *"you blew your budget,"* *"you fell behind."*
- Surveillance framing. *"We're tracking your sleep,"* *"your spending has been monitored,"* *"your meal plan adherence is at 65%."*

---

## 4. The Target User Archetype

**Principle:** The canonical Stillpoint user is the satisficer (a person who picks the first option that is good enough, rather than searching for the best one). She values stability over novelty, gets angry at UI changes, has zero tolerance for app wonkiness, and uses technology because she has to, not because she enjoys it. Her name in our heads is Elisabeth (Noah's wife), and every product decision gets sanity-checked against her.

**Source:** Noah's stated design principle, validated by the Ingle positioning refresh finding that the household has *two* buyer-types (the Claude-power-user installer who is often male, age 25–34, technical; and the primary cook / Logistics Laura, often female, age 32–48, less tech-forward, who is the daily user), and by the Troth positioning refresh finding that Fidelity 2026 puts 80% of couples on the one-engaged-manager split with Settled Simone as the modal buyer rather than Two-Partner Tomas. Both products independently surfaced the same archetype as the load-bearing user.

**Why this matters:** Most software gets built for the *installer*. That is the person who reads HackerNews, runs three browsers, has a developer account on every platform, and tolerates rough edges because the underlying capability is cool enough to make up for them. Building for the installer is easy; building for the daily user who is not the installer is much harder, and that user is the one who decides whether the household keeps the product.

**What Elisabeth means for design:**
- **Stick the landing on the front end the first time.** She does not give the product a second chance after a wonky first session. The MVP launch is the only launch; subsequent updates are improvements to a thing she already trusts, not rescues from a thing she already left.
- **Warn before changes.** Even a button moving from one spot to another is hostile if it happens without warning. *"Hey, we're moving the X tomorrow"* is required infrastructure, not a polish item.
- **Make the magic invisible on the backend.** If the system is doing something clever (inferring a category, suggesting a meal, picking a photographic tradition), the cleverness shows up as a result, not as a knob to turn. She does not want to tune the system. She wants the system to be right by default.
- **Defaults are the product.** The first-run experience is the product for her. Settings she has to find are settings she will never find.
- **No required configuration.** If the product needs five preferences set before it works, she closes it. The product works at minimum config; preferences accumulate from her actual use, not from a quiz.
- **Onboarding does not interrogate.** No archetype questionnaire, no money-script quiz, no caretaker-self-assessment, no Big Five Lite. (See §6 Personalization Principle.)

**Concrete examples:**
- Troth's UI shows *"what's left"* as a single number, not a per-category breakdown. The household earning $30K and the household earning $120K both see the same uncluttered surface; the depth lives in optional drill-downs they can find later if they want.
- Ingle's PWA companion exists specifically because the spouse-buyer is not the Claude-installer. The PWA has to work for someone who has never opened Claude and is not going to.
- Reveal's hero is a single compare-slider showing your photograph vs. your photograph developed. No tabs, no menus, no first-screen onboarding interrogation. The wedge is legible in 15 seconds because that is all the time Elisabeth gives a product.

**Do:**
- Test every change against the *"would Elisabeth notice this and be annoyed"* check.
- Default to the smart guess. Make corrections easy and silent.
- Ship the front-end pixel-perfect. The backend can be janky and improving in the background; the surface she touches cannot.
- Send a heads-up before UI changes. *"We're moving X next week"* is a feature, not a polish item.

**Don't:**
- Add toggles, settings panels, or knobs without thinking hard about whether 90% of users want the default. If they do, the toggle is hostile.
- Ship UI changes as *"refreshes"* or *"redesigns"*. Ship them as incremental improvements with prior warning.
- Require any configuration before first value. The product gives value at minimum config and gets smarter through use.
- Build for the power user as the primary persona. The power user is a channel, not the user. The daily user lives inside the household.

---

## 5. The Archetype Framework (Cross-Product)

**Principle:** Archetypes (which we sometimes call personas, meaning a named pattern of behavior we use as shorthand in design conversations) are dimensional, state-dependent, fluid, and inferred from behavior. They are not personality types, not categorical buckets, and not anything the user characterizes themselves into. The same person occupies different positions on the dimensions as their state changes, and the product responds to the state, not to a frozen self-report.

**Source:** Troth's `household-finance-research.md` §3 framework (the five-dimension model with the behavioral-state layer), Reveal's positioning research (Pat/Eric/Mira as buyer archetypes inferred from purchase shape, not from quizzes), Ingle's caretaker reframe (Logistics Laura as a lived experience, not a checkbox), and the Klontz Money Script Inventory validation work (KMSI-R 2025 showed even the well-established four money scripts have poor factorial validity, CFI = .762, on diverse samples; categorical types fail empirically across populations).

**Why this matters:** The personal-finance, fitness, and productivity industries are built on categorical types: *you are a saver / spender / monk / millionaire-next-door*. The categorical frame produces three failures simultaneously. First, the user is bad at characterizing themselves (Gladwell, *Blink*: people are terrible reporters of their own preferences and tendencies). Second, the type freezes a behavior that is actually fluid; the same person who is a vigilant Glancer at baseline becomes a textbook Avoider under overwhelm, and a categorical product cannot respond to that shift. Third, the type triggers identity defense; once the user has been categorized as the *Avoider* by the app, the app is implicitly saying *you are the problem* every time it nudges them.

The dimensional alternative replaces *what type are you* with *where are you on each axis right now, and what state is the system in.* Troth uses five dimensions (structure appetite, detail engagement, partner dynamics, income regularity, financial anxiety) plus a behavioral-state layer (baseline vs. stressed / avoidant). Reveal uses three buyer shapes (Phone-Photographer, Aspiring, Memory-Maker) inferred from purchase trigger and price-shape, not from a quiz. Ingle uses a single Logistics Laura archetype derived from cognitive labor research (Daminger 2019, 70 interviews showing women lead cognitive labor in 26 of 32 hetero couples; the asymmetry concentrates in anticipation, the most invisible step). All three are dimensional, behavioral, and never stored as a user type.

**Why archetypes still earn their keep:** They are communication labels for the rendering layer (the part of the product that picks language and visual emphasis at runtime). A user whose dimensions add up to a Glancer-shaped baseline pattern gets calmer, less-detailed copy. A user whose dimensions shift toward stressed gets the simplified view automatically. The archetype is a render-time label, never a stored category.

**Concrete examples per product:**
- **Troth.** The Keeper / Tracker / Glancer / Negotiator archetypes live only inside the LLM rendering prompt. They are never stored on the user record. The fifth archetype, Avoider, is a *state*, not a type. It fires when telemetry signals a stressed state (engagement collapse following a negative financial event, queued actions accumulating, payday-anchored variance). Same person, different state, different render.
- **Reveal.** Pat (Phone-Photographer, $5–15/mo, slow-curate acquisition), Eric (Aspiring, $15–30/mo, depth-of-craft acquisition), Mira (Memory-Maker, $50–200 one-time at the event moment, transactional-SEO acquisition). The archetypes are *purchase shapes*, not personality types. Mira is *event-driven* because that is how she buys, not because she is *that kind of person*.
- **Ingle.** Logistics Laura is a load-bearing user with specific cognitive constraints (anticipation work, end-of-day EF depletion, low-EF dinner vocabulary). The product targets the *cognitive shape of her job*, not the *kind of person she is*.
- **Borg-collective.** The ICP is *ADHD/cognitive-load-sensitive AI-native developer managing multiple projects*. That is a description of a working state and a working context, not a personality type. The user is *in* that state when they install borg; the product responds to the load they are carrying, not to a self-diagnosis they typed in.

**Do:**
- Define each archetype as a *pattern of behavior under a context*, not a *kind of person*.
- Surface the archetype label only at render time (in the LLM prompt, in copy selection, in visual density). Never store the label on the user.
- Add a state layer wherever behavior shifts under load (overwhelm, decision fatigue, end-of-day EF depletion). The state layer modifies how the dimensions render; it is not a sixth dimension.
- Detect state transitions from telemetry. Engagement drops, action backlogs, response-time slowdowns, time-of-day patterns are all signals.

**Don't:**
- Ask the user to pick their archetype at onboarding. Even if it improves cold-start, it freezes a fluid pattern, triggers identity defense, and produces worse data than telemetry would.
- Display the archetype label to the user (*"You are a Glancer"*). The label is internal infrastructure, not user-facing.
- Use the archetype to limit the user's options. A Glancer-baseline user who wants to drill into detail screens should see them.
- Treat archetype membership as a moral or ethical position. A Glancer is not lazier than a Keeper. An Avoider is not weaker than a Tracker.

---

## 6. The Personalization Principle

**Principle:** Personalization is the key to sticky apps, but the source of personalization is behavior, never self-report. We surface what we need from telemetry and pattern inference. We never ask the user to characterize themselves at onboarding. When we need a preference, we show a default, let the user correct it, and learn from the correction.

**Source:** Gladwell's *Blink* framing (people are bad at reporting what they want; the gap between stated and revealed preferences is the dominant finding in 50 years of consumer-research literature), Troth's behavioral-state work (the same person reports being a Glancer at baseline and reads as an Avoider under telemetry-observed stress; the self-report is a snapshot of one state), Ingle's caretaker research (Plan to Eat publicly removed their pantry inventory feature because synchronization is structurally unwinnable when it depends on user self-report), and the broader retention literature (food/diet apps die at 2% retention by year one in part because the onboarding self-assessment sets up an expectation the user cannot sustain).

**Why this matters:** Onboarding questionnaires feel like personalization but produce two specific failures. First, they front-load friction (every additional onboarding step drops retention 1.5–2 percentage points per Iyengar's 401(k) study and the broader funnel-analytics consensus). Second, they generate brittle data. The user's answer on a Tuesday is not their answer on a Sunday, and the system has now stored Tuesday's answer as ground truth. Telemetry-derived personalization is slower at the start but produces a profile that updates itself and that the user does not have to defend.

**The patterns we use:**

**Ask through tasks, not surveys.** The user does a thing they want to do; the system observes and learns. Ingle's pantry doesn't ask *"do you usually have rice on hand"*; it watches what gets added to lists across weeks and infers *things you usually have*. Troth doesn't ask *"are you a Keeper or a Glancer"*; it watches login frequency, time on detail pages, and notification interaction rate.

**Infer from telemetry.** The behavioral signals enumerated in the Troth state-layer work (engagement drop after a negative financial event, action backlog accumulation, payday-anchored variance) are all derivable from data the product already has. No new intake is needed. Same for Ingle (low-EF-dinner adoption rate, drag-drop-meal frequency, witching-hour traffic) and Reveal (which traditions the user accepts vs. rejects, which artist-statement framings they share).

**Show, then let the user correct.** The first-run experience makes a guess. The guess is shown; the user can accept, modify, or reject. Each correction is a teach signal. The system gets smarter without ever interrogating.

**Surface preferences from purchase shape and context.** Reveal infers Mira-shape from *event-triggered transactional search* and Pat-shape from *recurring small-amount usage*. The signal is in the buying behavior, not in a *"what describes you best"* quiz.

**The tension with the no-surveillance principle (§8):** Personalization needs data; we said we don't surveil. The reconciliation: we collect only what the user-facing value requires; we make the collection legible to the user; we never sell, share, or monetize the data beyond the product surface itself. Zuboff's surveillance-capitalism critique is the structural critique; the operational rule that satisfies it is *data flow stays user-facing*. If the user can turn off a personalization without breaking the product, the personalization is elevation. If the personalization only works because the user cannot turn it off (because we are selling the data flow underneath it), the personalization is surveillance.

**Concrete examples:**
- Troth's archetype inference: five trait dimensions surfaced from manual categorization overrides, custom category creation, session frequency, time on detail pages, partner-tag distribution, income variance, payday-anchored engagement. Zero questionnaire fields.
- Ingle's pantry: *things you usually have*, not *things you currently have*. Synchronization with reality is structurally unwinnable (Plan to Eat learned this the hard way); inference from list patterns is winnable.
- Reveal's tradition selection: the system picks Sternfeld or Adams or Kawauchi based on what is in the photograph. The user did not declare a favorite photographer at onboarding. The render says *"developed in the tradition of Stephen Shore because of the [specific compositional feature]"*; the user can accept or correct.

**Do:**
- Treat the first session as inference data, not as a configuration ceremony.
- Show the smart default; let the user override; learn from the override.
- Make the personalization legible. If the system inferred something, say so in plain English.
- Allow the user to turn off any inferred personalization without breaking the product.

**Don't:**
- Ask the user to fill in a Big Five Lite, a Klontz Money Script Inventory, an *"are you a planner or a spontaneous type"* quiz, a *"what's your fitness goal"* matrix, or anything that asks them to characterize themselves.
- Ship a *"tell us about yourself"* onboarding screen. If a piece of data is essential, ask for it inline at the moment it is needed.
- Store the inferred type as a user-facing label. Personalization is invisible infrastructure, not a badge.
- Sell, share, or monetize the behavioral data. The data flow stays user-facing; that is the line.

---

## 7. The Mental-Load Principle

**Principle:** Every product surface either reduces or adds to the user's mental load. We optimize for reduction. The world is loud, the user's working memory holds about four active items (Cowan 2001), and most households are already operating above their cognitive ceiling. Our job is to take items off the pile, not to add to it.

**Source:** Troth's behavioral research (Cowan 2001 working-memory ceiling; the Sweller cognitive-load theory failure mode of *qualitative shift to heuristic shortcuts or task abandonment* rather than gradual slowdown; the Stern et al. 2023 ADHD-as-structural-case work; Herd & Moynihan's administrative burden framework), Ingle's caretaker research (Daminger anticipation work; PMC narrative review on decision fatigue and prefrontal depletion at end of day; the EF-cooking literature), borg-collective's cognitive-load thesis (HBR's 14% AI-induced mental fog; METR's 19% developer slowdown; Anthropic's own 17% skill-mastery reduction study), and Weiser & Brown's calm-technology canonical text.

**Why this matters:** The dominant pattern in consumer software is *add a surface, add a notification, add a feature*. Each addition is a small tax. The taxes compound, not just add (Herd & Moynihan). Past the working-memory ceiling, the failure is qualitative; users do not slow down, they shut down. The Ingle caretaker research is explicit: when EF is depleted, *deciding what to cook* is the rate-limiter, not the cooking itself. Same shape across the portfolio: the deciding load, not the doing load, is what breaks people.

**The patterns we use:**

**Default to the smart guess.** A product that ships a default that is right for 80% of users is doing the load reduction work. A product that ships a *"pick one"* screen with five options is shifting the load onto the user. The 80% guess plus an easy override beats the five-option menu every time.

**The plan is a guide, not a contract.** Ingle's `4.6` finding from Plan to Eat: the meal plan must accommodate drag-and-drop, leftovers-as-tomorrow, and the rule that the user can ignore the plan without the product saying *"you failed."* Troth's variant: the budget is a plan, plan changes are expected, variance is *adjustment* not *failure*. Reveal's variant: the artist statement explains what the system developed; the user is not graded on whether they like it.

**No forced compliance loops.** Streaks, daily check-ins, *"you missed your goal today"* notifications, and any *"complete your X to unlock Y"* gating are forced compliance. Forced compliance increases short-term DAU and destroys long-term retention. The fasting tracker 52-week study (2.1% retention) is the canonical case.

**Count and minimize active items.** Troth's research surfaces the working-memory ceiling: a household tracking 12 bills, 8 portals, 4 accounts, and 6 categories is already past the ceiling. Every removed item increases engagement. Consolidate logins, automate bill-pay, treat each external portal as a tax. Same pattern for Ingle (the pantry holds *things you usually have*, not *every can in the cupboard*) and for borg-collective (the `adhd-guardrails` skill catches when the work itself has crossed into net-negative territory).

**Calm-tech defaults for notifications.** Default to zero push. Pull, not push. Predictable, bounded check-ins (Sunday-evening summary, monthly all-clear, payday review) reduce anticipatory anxiety because the user knows when to expect them. Constant alerts produce the doubt-check-relief-doubt cycle the CGM literature documents (the same hardware that reduces fear when alerts are exception-based produces compulsive checking when alerts are constant).

**Concrete examples:**
- Troth shows *"what's left and 11 days until next paycheck"* as a flow rate, not as a balance. The household earning $30K and the household earning $120K both see one number first; depth is opt-in.
- Ingle's *low-EF dinner* primitives (snack dinner, rotisserie remix, breakfast-for-dinner, sheet pan, freezer MVP, sandwich bar) reduce the dinner-deciding load by collapsing six branches of *what should I cook* into one.
- Reveal does not ask the user to pick a development style. The system picks; the user accepts or asks for another interpretation. The deciding load is taken off the user.
- Borg's `adhd-guardrails` skill catches the moment when adding more parallel agent work crosses from net-positive to net-negative for the human in the loop.

**Do:**
- Count the active items a user has to hold in working memory to use the product. Cut the count.
- Default to the right guess; make the override silent.
- Treat plan changes as expected, not as failure events.
- Use silence as reassurance. Send fewer notifications; make the ones you send carry a recommended action.
- Predictable, bounded check-ins. The predictability is anxiolytic (anxiety-reducing).

**Don't:**
- Add a screen, a notification, a setting, or a *"complete your profile"* prompt without subtracting one of equal weight.
- Use streaks, daily check-ins, or *"unlock"* gates. These weaponize the compliance loop.
- Send re-engagement nudges after a user has disengaged from overwhelm. The nudge worsens the avoidance (Yerkes-Dodson collapse-past-the-peak finding). Simplify instead.
- Ask the user to configure something the system can infer.

---

## 8. The Anti-Surveillance / Anti-Shame Principle

**Principle:** We do not surveil, judge, measure-against, or shame. Every feature, every notification, every piece of copy, every default visual gets audited against this. If a feature only works because it triggers guilt, comparison, or fear, the feature does not ship.

**Source:** Troth's positioning addendum (`failure-is-not-a-character-issue.md`, the explicit framing that financial avoidance is a state with a physiological substrate, not a character defect, with Porges polyvagal evidence, Lazarus-Folkman threshold model, Hilbert et al. 2024's induced-scarcity experiment producing 6.8× more payment delays through measurable shame-driven avoidance), Ingle's caretaker reframe (Eikey BJPsych Open's 73% of MyFitnessPal users perceiving the app as contributing to their eating disorder; Hartley and Langer RD critiques of MyFitnessPal and Noom bait-and-switch tracking; Zuboff's surveillance-capitalism structural critique applied to m-health), and the broader avoidance literature (Galai & Sade ostrich effect; Sicherman et al. 9.5% login drop after market declines; Howard & Lukas one in three would rather deep-clean a bathroom than check savings).

**Why this matters:** The dominant business model in consumer health, finance, and productivity software is engagement-through-guilt. The model works in the short run (DAU spikes), fails in the long run (retention collapses), and produces durable psychological harm in the people it sticks to (the Eikey ED finding is the most-cited example, but the pattern repeats across categories). Anthropic, Cursor, MyFitnessPal, Noom, and the major budgeting apps cannot ship anti-shame features even when they want to because their token-billed or engagement-billed business models punish the choice. Stillpoint can ship them because our business model is subscription, not engagement. The structural misalignment is the moat (see §10).

**What we never do:**
- **No calorie counters, no spend-shaming bars, no streak pressure.** Ingle bans the calorie surface entirely. Troth bans red *"over budget"* bars. Reveal does not grade the photograph.
- **No comparison shaming.** No *"users like you save more,"* no *"you spent more than your partner,"* no *"your friends are on track this month."* Shared visibility in Troth is *the books*, never *the scoreboard*.
- **No character-failure framing.** Not *willpower*, *discipline*, *grit*, *just need to*, *should*, *commitment*, *master your money*, *level up*, *hack*, *optimize*. These words quietly carry the discipline-problem framing the research says causes the harm.
- **No gamified restriction.** No streaks for spending less, no badges for skipping coffee, no green-bar rewards for hitting an arbitrary target.
- **No surveillance disguised as personalization.** If the only way the feature works is by tracking the user without their understanding or by selling the data flow, the feature does not ship.

**The tension with the personalization principle (§6):** We need data to personalize; we said no surveillance. The line is *user-facing value the user can turn off*. Troth detects the engagement-drop pattern that signals an Avoider state and simplifies the interface in response. The user benefits from the simplification. The data flow does not leave the product. The user can turn off the state-aware rendering and the product still works. That is the difference between elevation and surveillance.

**The tension with the mental-load principle (§7):** Sometimes the most load-reducing thing the product can do is to *not show the user something they would feel bad about*. Troth's behavioral-state response simplifies the view under stress instead of pushing the user to confront the unfiled-taxes-and-unopened-bills pile. The principle of refusing shame and the principle of reducing load align here, but the design has to actively make the simplification, not just *also display the bad news*.

**Concrete examples from the portfolio:**
- Troth's state-layer response. Engagement collapse after a negative financial event triggers fewer notifications, a simpler *"roughly on track"* surface, and no alert backlog. The product responds to the avoidance state by reducing load, not by punishing the absence.
- Ingle's pantry. *Things you usually have*, not *things you currently have*. The product does not surveil the cupboard; it infers a pattern and uses the inference to reduce the cooking load.
- Reveal's tradition framing. The system says *"developed in the tradition of Stephen Shore"* and explains why. The user is not graded against Shore. The render is *theirs*, in a tradition the system saw fit the photograph.
- Borg's `adhd-guardrails`. Catches the moment when the work itself is becoming net-negative for the user (late night, fatigue indicators, scope creep) and says so without scolding. No streak, no compliance gate, no *you've worked X hours straight* alarm.

**Do:**
- Frame variance neutrally; treat plan changes as expected.
- Reduce the visible task count under stress instead of escalating.
- Let the user opt out of any personalization without breaking the product.
- Use the language guide in Troth's positioning addendum as the cross-product reference: *state, condition, design, scaffold, environment, system, setup, cadence, capacity, attention, the books, what's there, what happened, we noticed, the household.*

**Don't:**
- Use *discipline, willpower, character, motivation, just need to, should, commitment, grit, master your money, level up, hack, optimize* in any product copy. These words carry the character-failure frame regardless of intent.
- Show streaks, scoreboards, comparison rankings, or *"users like you"* social proof.
- Ship a feature whose only mechanism is loss aversion or fear of missing out.
- Sell, share, or aggregate behavioral data beyond the product surface itself.

---

## 9. The Cross-Project Learning Discipline

**Principle:** Stillpoint Labs is one company with one set of principles, building several products. What we learned in Reveal informs Troth. What we learned in Troth informs Ingle. What we learned in Ingle informs borg-collective. The compounding only happens if we actively carry the learnings forward and revisit earlier products against the latest principles.

**Source:** Noah's stated principle, validated by the May 2026 positioning refresh exercise itself. Troth's `failure-is-not-a-character-issue.md` reframed the entire product around an insight that did not exist in the original brief; Ingle's caretaker reframe drew on the same anti-shame thesis Troth had just landed; the Reveal *"developed not inspired"* framing borrowed structurally from the Ingle *"tend not track"* framing; borg-collective's cognitive-load-as-moat thesis is the developer-tooling version of Ingle's elevation-vs-surveillance axis. The cross-pollination is observable, but only because it happens deliberately.

**Why this matters:** The default mode of startup work is *forget the previous project the moment the current sprint starts.* That mode is the right mode for engineering tasks; it is the wrong mode for principles. Without a deliberate review mechanism, the new product ships with a regression on a principle the team had already landed in the previous product.

**The mechanism:**

**Quarterly principles audit per shipped product.** Every quarter, every shipped product gets walked against this document. Each section becomes a checklist; the product surface is checked against the do/don't lists. Where a gap is found, the gap is filed as a research-or-design ticket. The audit doesn't have to fix the gap immediately; it has to *find* the gap.

**New research findings flow back to earlier products.** When the Troth research surfaced the state-layer model, the Ingle product team got a heads-up: does the meal-planning surface have a state-aware analogue? When the Ingle caretaker research surfaced the elevation-vs-surveillance axis, the Reveal team got a heads-up: does the photographic-development surface elevate or surveil? The flow is structural; it happens regardless of which team did the research.

**The principles doc itself versions.** This document is dated 2026-05-25. The next version supersedes this one. Earlier versions remain in the repo for diff-readability. When a principle shifts (the em-dash rule got loosened from *"not one"* to *"cap at 3 per article"* between voice validation runs), the diff is visible and reviewed.

**Concrete examples:**
- Troth's state-layer model emerged from Noah's lived experience. Ingle's caretaker reframe drew on the same lived experience refracted through household cognitive labor. Both products converged on the *system-as-guardian, not surveiller* framing, but only because the the researcher carried the framing across.
- The voice work for Troth's positioning addendum (*"we noticed, the books, what's there, the household"*) became the language guide for the broader brand. Without the addendum, the language drift across product surfaces would have continued.
- Borg-collective's cognitive-load thesis is the developer-tooling version of the household mental-load thesis. The shape transfers; the surface details differ. The transfer only works because the principles doc explicitly names the shape.

**Do:**
- Run a quarterly audit of every shipped product against the latest principles doc. Bring the findings to the principles document review.
- When new research lands on one product, ask explicitly *does this change anything for the other products*. The answer is sometimes no; the asking is mandatory.
- Version this document. The current version supersedes the previous one; older versions live in the repo so the team can see what changed and why.
- Carry the language forward. If a phrase from Troth's positioning lands well, see whether it belongs in the Ingle brand guide. If the Ingle elevation axis is the right axis, see whether it belongs in the Reveal positioning.

**Don't:**
- Treat each product as a separate company. The infrastructure (shared identity layer, household data model, principles doc, brand voice) compounds; treating products as siloed throws the compounding away.
- Skip the quarterly audit. Most regressions on principles happen between version 1.0 and version 1.4, not at the launch moment. The audit catches the regression while it is still cheap to fix.
- Update the principles doc without dating the change. The history matters; future product teams will need to see when a principle shifted and what evidence shifted it.

---

## 10. The Moat Thesis

**Principle:** Stillpoint's moat is not the technology, not the AI capability, not the feature checklist, not the pricing, and not even the brand. The moat is the operating principles in this document, executed consistently, across a portfolio of products, in a market where every well-capitalized competitor is structurally barred from doing the same thing.

**Source:** Three independent strands of the May 2026 positioning research converged on the same insight from three different angles. Reveal's positioning refresh names it as *"judgment is the moat."* Every adjacent tool (Adobe Generative Remove, Google Reimagine, Luminar GenSwap) chose generative invention while Reveal alone chose curatorial restraint, and the restraint is a *judgment* call that the larger tools cannot ship because their business models punish it. Borg-collective's positioning refresh names it as *"cognitive load is the moat."* Anthropic, Cursor, and the major coding-tool vendors cannot ship shame-free language, work/life boundaries, or scope-locking because those features reduce token consumption and engagement, and reducing token consumption is suicide for a token-billed business. Troth's positioning addendum names it as *"systems-not-character is the moat."* Ramsey, YNAB, and the broader personal-finance industry are structurally locked into the discipline-problem framing because their entire content engine and community model depend on it; a tool that says *the system is the problem, not the user* cannot emerge from inside that industry.

**Why this matters:** Founders are trained to look for moats in technology (proprietary algorithms, exclusive data, network effects). Those moats are real but increasingly thin in the AI era. Model capability is commoditizing, data is increasingly portable, and network effects in consumer software are harder to build than the textbooks suggest. The under-discussed moat is *the choices a competitor cannot copy even when they want to*, and the most durable version of that moat is the choice that is *structurally* impossible for the competitor's business model.

**The shape of the moat in practice:**

**Brand voice that refuses engagement-bait.** Big competitors cannot ship a product that defaults to zero push notifications, treats plan changes as expected, and reframes variance as adjustment, because their growth metrics punish it. We can.

**Personalization without surveillance.** Big competitors cannot ship telemetry-derived personalization that *the user can turn off without breaking the product*, because the data flow underneath is part of how they make money. We can.

**Anti-shame product framing.** Big competitors cannot remove streaks, scoreboards, and red bars because those mechanisms drive DAU and DAU drives valuation. We can.

**Cognitive-load reduction over feature accumulation.** Big competitors cannot ship a roadmap that *removes* features more often than it adds, because feature count is the way enterprise sales pitches and analyst reports score them. We can.

**Boundary-respecting UX.** Big competitors cannot ship a coding tool that catches the moment when the user has worked past the point where the work is net positive, because their pricing punishes the boundary. We can.

**Concrete examples:**
- Reveal lives in the curatorial-restraint quadrant alone. Every well-capitalized competitor chose generative invention. Conjointly 2025 documents the consumer trust collapse (AI marketing-image agreement fell from 55% to 36% YoY). The wedge widens as the market moves.
- Borg-collective lives in the skill-layer-with-anti-engagement-features quadrant alone. Anthropic's plugin marketplace explicitly invites contributions to this layer, and the layer is structurally misaligned with Anthropic's own incentives. Borg can ship `adhd-guardrails`. Anthropic cannot.
- Troth's positioning addendum (*"failure is not a character issue"*) sits in the space that Ramsey, YNAB, and the major budgeting apps cannot occupy without abandoning their content engine. Olen's *Pound Foolish* documents the personal-finance industry's structural dependence on the discipline-problem framing.
- Ingle's *"the kitchen table, tended"* framing sits in the space that MyFitnessPal, Noom, and the meal-kit market cannot occupy without abandoning their core engagement model. The Eikey ED finding (73% of users perceiving MyFitnessPal as contributing to their disorder) is the failure mode the dominant model produces; the failure is not a bug, it is the engagement loop working as designed.

**The discipline required to keep the moat:**

The moat only holds if we execute the principles consistently. The moment we ship a streak feature, a red over-budget bar, or a comparison-shaming surface, we lose the structural distinction that big competitors cannot match. The principles doc is therefore not aspirational marketing; it is the actual operating constraint that produces the moat. Every quarterly audit is a moat-protection exercise.

**Do:**
- Treat the operating principles as the moat. Defend them when product pressure pushes the other way.
- When a new competitor enters the space, look for the principle they cannot ship because of their business model. That is where we stay durable.
- Make the principles legible to users. When users say *"why does Stillpoint software feel different,"* the answer is in this document.
- Audit shipped products against the principles quarterly. Catch the regression while it is still cheap.

**Don't:**
- Compete on features. The features commoditize. The principles do not.
- Compete on price below the cost of execution. A cheap version of the principles is not the principles.
- Compete on AI capability. Every competitor has the same model access. The differentiation is in what we choose not to do with it.
- Soften a principle to make a quarter's metric. The metric recovers; the moat does not.

---

## Closing Note

This document is the operating manual. It will be wrong about specific things; the research it cites will be superseded; the products will surface findings that change the framing. When that happens, the document gets updated, the change gets dated, the rationale gets recorded, and the older version stays in the repo for the diff.

The thesis at the top does not change: lift the human, reduce the load, refuse the shame frame. Everything else is the work of holding that thesis steady against pressure.
