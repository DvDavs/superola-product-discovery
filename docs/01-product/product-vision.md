# Product Vision — P01 Recommendation

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a discovery recommendation, not an approved product commitment.

Superola should help a person planning an event find a relevant provider and reach a decision with less uncertainty, while giving providers qualified opportunities they can evaluate and answer without losing control of their business or contact information. Superola must learn whether it can produce this exchange repeatedly before taking responsibility for booking, customer payment, provider payout, or broad automated matching.

## Recommended launch hypothesis

**Superola V1 — Marketplace Leads**

Public marketplace → provider discovery → provider profile → direct structured service request/RFQ → provider quote or decline → in-platform conversation → customer decision.

The V1 boundary ends at a customer-reported `proceed`, `not proceeding`, or explicitly `unknown` outcome, with reporter and time where available. A quote is not a booking, stated intent is not payment, and an in-platform record must not be called a `verified booking` without a separately defined auditable event.

Direct RFQ is recommended over automatically broadcasting a general request because current evidence supports provider discovery and communication, but does not establish routing consent, eligibility, fan-out, response windows, deduplication, rerouting, or the operating capacity required for matching. See `docs/05-roadmap/mvp-scope.md` for the alternative comparison.

## Actor outcomes

- **Customer:** identify a provider who fits the service, place, timing, and event context; obtain a useful response; and decide whether to continue, without chasing a directory of stale contacts.
- **Provider:** maintain a credible discoverable presence, receive relevant requests, quickly quote or decline, and see enough attributable opportunity/conversion value to justify continued participation and, eventually, payment.
- **Superola operator:** create repeatable, trustworthy exchanges with measurable supply, demand, response, and outcome signals while keeping moderation, support, acquisition, and monetization costs sustainable.

Capabilities without a clear contribution to one of these outcomes do not belong in the V1 hypothesis. In particular, booking, payment/payout, universal real-time availability, general request broadcasting, sponsored inventory, external-agent actions, and protocol adapters require separate evidence and approval.

## Product principles for this hypothesis

1. Treat Superola as a two-sided marketplace, not a static directory.
2. Prefer a complete learning loop over a larger feature list.
3. Preserve customer intent, provider response, and outcome state without claiming a transaction Superola did not execute.
4. Keep organic relevance separate from paid placement and conversations separate from notification delivery.
5. Use category-aware semantics; do not force performers, venues, caterers, professionals, and transportation into one universal availability or quote model.
6. Make public marketplace information useful to people and machine-legible when public discovery ships; do not add protocol-only scope.
7. Treat legacy scale as an opportunity to audit, not proof of active supply.

## Success signals — definitions, not fabricated targets

Targets and reporting windows require owner/business approval and baseline data. P01 defines the signal families only.

| Signal family | Measures to define in later phases |
|---|---|
| Supply | Active/publishable providers; profile completeness and quality; category/geographic coverage; freshness; provider notification reachability. |
| Demand | Meaningful searches; zero-result searches; provider-profile views; direct service requests; request abandonment. |
| Marketplace | Provider response rate and time; decline/no-response rate; quote rate; conversation progression; customer-reported proceed/not-proceeding/unknown outcome with provenance where measurable. |
| Monetization | Trial activation; trial-to-paid conversion; provider retention; entitlement use; later provider demand for sponsored placement. |
| Quality | Spam/abuse; duplicate or ownership-disputed profiles; stale profiles; invalid contact/notification failures; reports and moderation outcomes; customer/provider satisfaction indicators. |

These signals must be segmented by launch category and geography so aggregate numbers do not hide an empty or unhealthy local marketplace.

## Evidence and approval boundary

- `CONFIRMED`; provenance `OWNER_INTERVIEW`: provider profiles, communication, recurring provider subscription, and sponsored placement are owner concepts; the commercial details and user demand are unvalidated.
- `CONFIRMED`; provenance `DAVID_DIRECTIVE`: Superola is treated as a two-sided marketplace and employer-facing artifacts must support Spanish and English.
- `PROPOSED`; provenance `TECHNICAL_DISCOVERY`: the Marketplace Leads slice, direct RFQ boundary, success model, and staging recommendations.
- `ASSUMPTION`: provider activity, launch geography, category priority, willingness to pay, contact policy, and the ability of direct RFQ to create a healthy loop remain open in the canonical registers.

Owner validation, customer/provider research, and legacy-data evidence may modify or reject this launch hypothesis before downstream scope is approved.
