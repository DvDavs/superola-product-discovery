# ADR-008 — Monetization is a reserved policy seam, not a V1 module

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David for the architecture; **the owner decides packaging and timing (`OR-012`, `G-08`)**
- Related evidence/requirements: `docs/01-product/monetization.md`; `docs/05-roadmap/mvp-scope.md` ("Do not build a generic admin suite, entitlement engine, or sponsored-campaign system"); `docs/02-architecture/architecture-overview.md` principle 6 ("Model monetization and entitlements explicitly"); `A-003`, `A-012`, `R-004`, `R-017`, `R-018`; `docs/02-architecture/decision-branches.md` `DB-07`, `DB-08`
- Supersedes / superseded by: N/A

## Problem / context

Two repository statements sit in apparent tension: `mvp-scope.md` forbids building an entitlement engine or sponsored-campaign system, while `architecture-overview.md` principle 6 requires modelling monetization and entitlements explicitly. P02 must resolve this without either building a system or leaving a hole that requires restructuring later.

`CONFIRMED` (owner hypothesis, unvalidated): providers may pay a modest recurring subscription; category plus geography could define sponsored inventory. `PROPOSED`: the staging. `OPEN`: `A-003`, `A-012`, `OR-012`, `G-08`.

## Constraints

Free onboarding at V1.0. No price, package, or trial duration is approved. Payment must never bypass profile quality, trust, or eligibility gates. Organic ranking and paid placement must remain separate mechanisms and separate trust surfaces. `monetization.md` additionally requires that a trial clock not start before a provider is publishable **and** able to receive relevant demand — meaning the clock depends on marketplace measurements that do not yet exist.

## Options considered

### Option A — A reserved policy seam, not a module (recommended)

Exactly three structural facts, **each independently required for Phase 1 anyway**:

1. **One named `EligibilityDecision` point** consulted by Provider for publication and by Discovery for appearance, whose named inputs already include an `entitlement` input that is **constant** in V1.
2. **`placementBasis` carried end to end** — on every result slot and every measurement event — with `organic` as its only V1 value.
3. **Per-provider value events** in Analytics, which are the future gate's evidence.

No plan, price, tier, trial clock, proration, or entitlement data. **No empty structures "for later".**

### Option B — Build a minimal entitlement model now

Rejection factors: `mvp-scope.md` forbids it, and `OR-012`/`G-08` is unanswered, so anything built now is speculative generality that will be wrong. The expensive part of entitlements is not the *check* — that is one function — it is the **lifecycle**: a calendar-driven state machine with money attached, dominated by exceptions (failed payment, partial period, refund, currency, tax by jurisdiction, and two jurisdictions under `S-2`).

### Option C — Carry no seam at all

Rejection factors: this is where the real asymmetry lives. Deferring the *entitlement system* is nearly free. Deferring **`placementBasis`** is not: retrofitting placement provenance after sponsorship ships destroys historical organic comparability — the `R-004` and `R-018` failure mode — and removes the only data that could teach Superola how to **price** sponsorship, since position-to-request attribution is obtainable only from organic data.

## Decision

Recommend Option A, and state the **eligibility ordering invariant** explicitly:

> Trust and quality gates decide publication first. A future entitlement may **narrow** eligibility but may never bypass those gates. `EligibilityDecision` must never gain a "paid" branch — paid placement is a separate pipeline, not a branch here.

`Subscription` and `SponsoredCampaign` remain concepts with no entity, field, relationship, or diagram box. **And no `featured` boolean exists anywhere** — that is the named anti-pattern.

**Approval still required:** David for the seam; **the owner for packaging, timing, and charging policy (`OR-012`, `G-08`).**

## Rationale

The tension resolves by locating **one seam rather than building a system**. Because publication and request eligibility gating is mandatory regardless, the seam is where the decision already had to be — so it costs one indirection.

`placementBasis` is the cheapest high-value seam in the design and the most expensive to retrofit. Carrying it while sponsorship does not exist costs one enumerated value and one discipline.

## Consequences

Positive: no paywall; eligibility is trust-gated and never payment-gated; entitlements later are a change **in one place**; organic data stays comparable after sponsorship launches; the monetization gate can be evaluated from real value events.

Negative: no revenue in Phase 1, which is the intended trade (`A-012`). Provider value visibility becomes important instead — and that *is* the eventual willingness-to-pay driver.

Operational: no billing disputes, which would otherwise be the most support-intensive case class in this product, arriving in two languages under `S-2`.

## Cost implications

The seam: NEAR-ZERO. The system: MEDIUM-HIGH build dominated by billing lifecycle exceptions; MEDIUM recurring; **HIGH per case** operator cost; HIGH exit, because subscription state and billing history are the stickiest vendor-held data of all. Sponsored inventory: HIGH build across inventory per market, eligibility, allocation and rotation fairness, campaign lifecycle, budget pacing, disclosure in **every** channel, and separately attributable measurement — with **negative expected value before liquidity** (`R-018`).

## Lock-in and exit implications

Deliberately none: no billing vendor is selected or implied. Keeping the seam rather than the system also keeps the *business* option open, including the fallback of permanently free supply with promotion only.

## Security and privacy implications

Sponsored eligibility data is operational and must not enter the public projection or organic ranking inputs. Payment-adjacent data — bank details, tax identifiers, card data — must not be stored in V1, **and not via legacy import**, so classifying and excluding it is an explicit P05 rule. A marketplace verification must never be silently reused as payout clearance; typed verification facts prevent that drift (`ADR-012`, `security-privacy-architecture.md` §9).

## Reconsideration trigger

**All** of `monetization.md`'s V1.x gate conditions **observed as data**: cohort supply and demand health; providers able to observe *attributable* value rather than impressions; measurable trial activation, conversion, retention, and churn; plus owner validation of package intent and charging policy. For sponsorship, additionally and all three: publishable-provider count in one `Market` exceeding first-page positions (genuine slot scarcity); measured position-to-request effect; and unsolicited provider requests for paid visibility.

## Validation

Verify exactly one eligibility decision point exists and that publication and appearance both consult it; that no plan, price, or entitlement structure exists; that no `featured` or `sponsored` flag exists on any profile or in any ranking input; and that every result and measurement event carries `placementBasis`.
