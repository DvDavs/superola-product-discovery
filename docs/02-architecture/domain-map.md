# Capability / Domain Map — Initial Hypothesis

> Every capability below is `PROPOSED`; boundary discovery has not started. A capability is not automatically a bounded context, module, microservice, team, database, or deployment unit.

| Cluster | Candidate capabilities | Key discovery boundary |
|---|---|---|
| Access and parties | Identity, Customers, Businesses, Vendor Membership/Staff | Person vs account vs business, ownership, roles, claim/verification. |
| Marketplace supply | Catalog, Categories/Taxonomy, Listings, Media | Service model, category variation, content governance, media rights. |
| Location and time | Geography/Service Areas, Availability | Base location vs service area, travel, timezone, availability semantics. |
| Discovery and growth | Search, Ranking, Promotions/Sponsored Placement, Favorites, AI/Matching | Deterministic constraints, organic trust, paid disclosure, personalization. |
| Demand and transaction | Events, Service Requests/RFQ, Quotes, Bookings | MVP stopping point, negotiation, commitment, cancellation, concurrency. |
| Communications | Conversations/Messaging, Notifications | User content vs event delivery, privacy, consent, moderation, channels. |
| Money | Payments, Subscriptions/Billing | Customer charges, provider payouts, provider plans, entitlements, tax/compliance. |
| Trust and operations | Reviews/Reputation, Verification, Moderation, Analytics, Admin | Eligibility, abuse, evidence, auditability, operational ownership. |

P02 must refine boundaries from validated product flows and invariants rather than decomposing this list mechanically.
