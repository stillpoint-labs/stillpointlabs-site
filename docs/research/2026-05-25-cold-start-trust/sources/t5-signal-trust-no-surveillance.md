# Source: Signal — Trust Without Surveillance (Mozilla / Signal Support / VPN Overview)

**Full citation:** Signal Support. "Is it private? Can I trust it?" Plus Mozilla Foundation "Signal App Review 2025." Plus VPNOverview "Is Signal Safe?"
**URL:** https://support.signal.org/hc/en-us/articles/360007320391 · https://www.mozillafoundation.org/en/nothing-personal/signal-privacy-review/
**Date accessed:** 2026-05-25
**Evidence level:** 7 (vendor + independent reviewer; the architecture itself is independently inspectable)
**Research topic area:** T5 — Anti-tracking trust earning

## Credibility Scores

| # | Dimension | Score | Justification |
|---|-----------|-------|---------------|
| 1 | Authority | 8/10 | Signal Foundation is a non-profit; Mozilla Foundation is a recognized privacy advocate; the Signal Protocol is the underlying gold standard adopted by WhatsApp, Messenger. |
| 2 | Evidence Quality | 6/10 | Signal publishes transparency reports and source code; the protocol has been audited multiple times. |
| 3 | Currency | 9/10 | 2024-2025. |
| 4 | Intent | 8/10 | Non-profit; explicit refusal to monetize via ads or data. |
| 5 | Bias & Objectivity | 6/10 | Signal's own posture is one-sided but justifiable; Mozilla review is independent. |
| 6 | Logic & Coherence | 9/10 | Sealed Sender + metadata minimization + open source + non-profit + transparency reports — these are coherent mutually-reinforcing trust signals. |
| 7 | Corroboration | 9/10 | The Signal Protocol's adoption by WhatsApp and Facebook Messenger is itself massive corroboration; multiple independent audits. |
| 8 | Intellectual Honesty | 8/10 | Signal publishes subpoenas; explicit about what data they DO retain (signup timestamp, last-connected timestamp). |
| 9 | Specificity | 8/10 | Names exact technical primitives: Sealed Sender, E2EE, sign-up phone-number-or-username, 40M MAUs reported. |
| 10 | Relevance | 9/10 | Direct: how does an anti-surveillance product earn enough trust to grow? |

**Composite score:** 7.80

## Bias Guard Check

- [x] I agree with this source's conclusions → scored harder on dims 5, 6, 8
- [ ] I disagree with this source's conclusions → scored more generously on dims 5, 6, 8
- [ ] Neutral / no strong reaction

## Key Findings

1. **Trust earned through ARCHITECTURE that makes betrayal impossible.** Signal's Sealed Sender means the server doesn't know who sent a message to whom — even if compromised, the data isn't there to leak. This is "we can't betray you" beats "we won't betray you."
2. **Transparency reports as routine.** Signal publishes every legal request and response. Routine transparency makes the absence of trust-violating events VISIBLE, which is itself trust-building.
3. **Non-profit status is a structural trust signal.** No shareholder pressure to monetize via data flows. This is the Proton "aligned business model" pattern in non-profit clothes.
4. **The Signal Protocol's adoption by competitors (WhatsApp, Messenger) is META-corroboration.** "The privacy gold standard adopted by your less-private alternatives" is itself the trust argument.

## Inclusion Decision

**Decision:** Core
**Rationale:** Rule 1 (Strong Include) — composite 7.80. Signal is the canonical case of "trust through architectural impossibility-of-betrayal" pattern.

**Perspective category:** Practitioner
