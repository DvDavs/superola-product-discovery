# High-Value Product Questions

This is a focused decision register, not a generic questionnaire. P01 must prioritize answers that change scope, cost, compliance, migration, or marketplace mechanics.

## Next owner meeting

These questions use business language and ask the owner only for product/business direction. David and the technical team retain implementation decisions.

### CORE — maximum 7

1. **What result must a customer and a provider each get for you to call the first Superola release successful?**
2. **Should the first release focus on a customer choosing one provider and requesting a quote, or must Superola also send one request to several providers? Where should that first journey stop: decision, booking, or payment?**
3. **Which categories and geographic markets must launch first, and which language(s) do those customers and providers need on day one?**
4. **Of the reported legacy providers, what do you believe is still trustworthy and usable—and do we have permission to migrate, contact, or ask them to claim their profiles?**
5. **What provider value should always be free, what should eventually require a subscription, and what must a provider experience before it is fair to start a trial or charge?**
6. **When should customers and providers be allowed to exchange phone numbers or email, and what interactions do you want Superola to keep in the platform?**
7. **Of the feature/monetization document, existing UX work, and six-month discussion, which items are firm business commitments and which are ideas we can challenge or stage?**

### IF TIME — maximum 5

1. **What must be true in a city/category before you would feel comfortable selling sponsored placement there?**
2. **What promise should “available” make to a customer: a helpful indication, an invitation to ask, or a reservable commitment?**
3. **Which trust claims matter at launch, and what proof would make words such as “verified” honest?**
4. **How much moderation, provider reactivation, and customer/provider support can Superola realistically operate during launch?**
5. **Is public web discovery the first acquisition priority, and is a mobile app genuinely required for the first release or a later channel?**

Answers should be recorded with source version/date and reconciled against the P01 recommendation. They may confirm, modify, or reject it.

## Canonical decision register

| ID | Question | Why now | Decision owner / evidence |
|---|---|---|---|
| Q-001 | What exact customer outcome and provider outcome define MVP success? | Prevents the full marketplace vision from becoming an unbounded first release. | Owner + David; feature document and interviews. |
| Q-002 | Does V1 stop at discovery/contact, support structured requests/quotes, include booking, or include payment/payout? | Each boundary materially changes scope, compliance, domain design, and roadmap. | Owner/business decision + user research. |
| Q-003 | Which launch geography/geographies and languages are committed first? | Changes payments, tax, legal, localization, support, maps, SEO, and operations. | Owner/business strategy + analytics. |
| Q-004 | Which provider categories are launch-critical, and where do their profile/availability/quote needs differ? | Avoids designing only for musicians or a lowest-common-denominator model. | Owner + provider research + legacy data. |
| Q-005 | What trustworthy value exists in the legacy 43k registrations, and what may legally migrate or be contacted? | Determines cold start, reactivation, migration effort, privacy risk, and launch sequencing. | Authorized data audit + legal/privacy + owner. |
| Q-006 | How should provider identity, business ownership, staff, multiple services, and claim verification work? | Controls trust, permissions, migration, billing, and duplicate resolution. | Owner + provider research + legacy audit. |
| Q-007 | What does "available" mean across categories, and is it informative, request-based, reservable, or bookable? | Drives search accuracy, calendars, quoting, concurrency, and booking semantics. | Provider/customer research. |
| Q-008 | What must remain inside Superola, and when may users exchange direct contact information? | Changes adoption, safety, privacy, moderation, measurement, and notification strategy. | Owner policy + user research + compliance. |
| Q-009 | What provider value justifies subscription, and what remains free? | Determines packaging, entitlement design, liquidity, and acquisition friction. | Pricing research + owner. |
| Q-010 | What fairness, disclosure, targeting, rotation, and measurement rules govern sponsored placement? | Protects trust and prevents a simplistic `featured=true` design. | Owner/business policy + customer/provider research. |
| Q-011 | Which pending feature/monetization and UI/UX materials represent commitments versus explorations? | Prevents conflict and accidental requirement promotion. | Owner + document provenance/versioning. |
| Q-012 | Which risks or capability gaps justify specialist hiring, and at what project gate? | Aligns spend with evidence and schedule dependencies. | David + architecture/roadmap evidence. |
| Q-013 | Must launch support known-service browsing, a general event brief routed to providers, or treat them as alternative slices? | Matching introduces structured requests, routing, fan-out, provider eligibility, state, messaging, and abuse controls beyond public discovery. | Owner + customer/provider research. |
| Q-014 | What auditable event can Superola label as a verified provider, booking, or review? | Prevents migrated, self-asserted, customer-confirmed, contracted, and platform-paid events from being conflated. | Owner + product + operations + legal/privacy. |
| Q-015 | Which public acquisition channels are launch objectives, and what crawler/training policy follows for Googlebot, OAI-SearchBot, and GPTBot? | Search discovery and model-training access are separate policy decisions with content, legal, privacy, and measurement consequences. | Owner + David + legal/privacy + growth evidence. |
| Q-016 | What measured demand or partner opportunity would justify an external agent/RFQ adapter after V1? | Prevents protocol popularity from creating scope without channel economics, stable workflows, or target-market access. | Owner + David + product/channel evidence. |
| Q-017 | If a customer deliberately contacts multiple providers, should each direct RFQ remain independent, and what happens to the others after the customer decides? | Prevents manual comparison from silently becoming shared multi-recipient routing, auto-closure, or an overstated conversion model. | Owner + customer/provider research. |
| Q-018 | Which customer verification timing creates acceptable trust without unacceptable request abandonment? | Pre-submit, just-in-time, and guest-then-verify options change friction, spam, reachability, consent, and support. | Owner/product + privacy/security + P04 research. |
