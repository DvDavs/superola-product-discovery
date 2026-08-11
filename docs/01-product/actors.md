# Actors and Outcomes — P01

Actor existence and owner concepts use `OWNER_INTERVIEW`; outcome statements are `PROPOSED` with provenance `TECHNICAL_DISCOVERY` until validated. Actors are not final authorization roles or domain entities.

## Primary marketplace actors

| Actor | Evidence | Job to be done / desired outcome | What makes V1 valuable | Failure signal |
|---|---|---|---|---|
| Customer / event planner | Existence `CONFIRMED` | Find a suitable provider for a real event need, obtain a useful response, and make a confident next-step decision with less time and uncertainty. | Relevant discovery, credible profile information, a structured direct request, clear provider response, and a usable conversation. | Empty/irrelevant results, stale profile, no response, unsafe contact pressure, or a quote that cannot support a decision. |
| Provider / vendor | Existence `CONFIRMED` | Present the business accurately, receive relevant opportunities, decide quickly whether to pursue them, and convert demand into attributable business value. | Governed presence, correct service area, manageable requests, quote/decline control, conversation context, and visible value signals. | Spam/irrelevant requests, duplicate or unowned profile, notification failure, unmanageable profile work, or no credible route from activity to value. |
| Superola operator | Stakeholder `CONFIRMED`; operating role `PROPOSED` | Produce repeatable healthy local/category exchanges and learn where supply, demand, trust, and monetization can be sustained at acceptable operating cost. | Governed supply, measurable funnel states, moderation and support controls, entitlement boundaries, and truthful organic discovery. | Artificial registration volume, unanswered demand, abuse, opaque rankings, unbounded manual operations, or charging before value exists. |

## Supporting actors

| Actor | Evidence | Intended outcome | P01 boundary |
|---|---|---|---|
| Provider staff/member | `PROPOSED` | Help manage a business profile, requests, quotes, and conversations with appropriate accountability. | Multi-user roles may be deferred; P02 must decide whether the launch categories require them. |
| Platform administrator/support | `PROPOSED` | Resolve ownership/duplicates, govern taxonomy and publication, moderate reports, and handle provider lifecycle exceptions. | Only workflow-derived minimum operations belong in V1; no generic admin-suite assumption. |
| Superola owner/business decision-maker | `CONFIRMED` stakeholder | Validate customer/provider outcomes, launch market/category, monetization policy, and business risk. | Does not decide implementation details that belong to David/technical discovery. |
| External delivery or commodity system | `PROPOSED` supporting actor | Deliver notifications or other bounded capabilities reliably. | No vendor or technology selection in P01. |
| Public crawler/referral channel | `PROPOSED` channel | Discover permissioned public marketplace information and refer attributable demand. | Human-first machine-legible foundation only; no external write actions or protocol-only V1 deliverable. |

## Outcome-to-capability test

| Candidate capability | Customer outcome | Provider outcome | Operator outcome | P01 treatment |
|---|---|---|---|---|
| Governed search and public provider profiles | Find and evaluate relevant supply. | Be discoverable for accurate services/areas. | Measure useful supply and demand. | Included in proposed V1. |
| Direct structured RFQ | Express a concrete need to a chosen provider. | Receive enough context to quote or decline. | Observe qualified marketplace exchange. | Included in proposed V1. |
| Quote/decline + conversation | Reach a decision with context. | Control response and pursue viable work. | Measure response and progression. | Included in proposed V1. |
| Notification delivery | Know when the other side acts. | Respond before opportunity decays. | Reduce silent failure. | Minimum channels included; conversation remains separate. |
| General request broadcast/matching | Potentially reduce repeated searching. | Potentially receive more demand. | Potentially improve fulfillment, but only with routing operations. | Deferred pending consent, eligibility, fan-out, abuse, and liquidity evidence. |
| Booking/payment/payout | Complete transaction in one place. | Gain commitment/payment support. | Capture transaction and possible fees. | Deferred: major compliance and operations cluster. |
| Sponsored placement | See paid inventory clearly disclosed. | Buy incremental visibility. | Monetize scarce, measurable demand. | Staged after credible organic liquidity and measurement. |
| Natural-language intent interpretation | Express needs conversationally. | Receive structured, relevant demand. | Test a new discovery entry point. | Optional bounded experiment; deterministic search remains authoritative. |
| External agent actions/protocol adapters | Use third-party agent channels. | Reach future channel demand. | Diversify distribution. | `FUTURE`; no protocol-only V1 scope. |

If a capability cannot explain its actor outcome, measurable signal, and operating owner, it should be challenged before downstream design.
