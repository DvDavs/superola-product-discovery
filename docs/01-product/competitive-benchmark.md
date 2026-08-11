# Competitive Benchmark — GigSalad and The Bash

> Status: `PROPOSED` synthesis of current public evidence. Competitor behavior is `OBSERVED`; it is not a Superola requirement or accepted design.
> Scope: bounded public benchmark requested by `SRC-012`.
> Access date: 2026-08-11.

## Decision problem

What can Superola learn from two mature event-services marketplaces about acquisition, discovery, provider onboarding, lead handling, trust, ranking, monetization, and retention without copying their scope, economics, or opaque behavior?

## Evidence limits

- Sources are public first-party pages and live public surfaces (`EXTERNAL_PRIMARY` or `LIVE_PLATFORM`). Authenticated workflows, internal algorithms, moderation operations, experiments, and commercial terms may differ.
- Public prices, fees, product availability, and protocol behavior are time-sensitive observations.
- Competitor-reported conversion, response, traffic, and booking metrics are unaudited.
- Neither competitor validates Superola's possible U.S./Mexico scope, legacy-data quality, willingness to pay, or launch boundary.
- A competitor's use of booking, payment, guarantees, or paid placement does not justify adding those capabilities to Superola V1.

## Comparative lifecycle

| Dimension | GigSalad — `OBSERVED` | The Bash — `OBSERVED` | Claim sources | Superola implication |
|---|---|---|---|---|
| Acquisition and discovery | Search/browse by service, event type, and geography; category/location pages; direct provider selection or general matching. | Search by service and event geography; category/location pages; direct browsing or a general event brief. | `GS-01` `GS-02`; `TB-01` `TB-02` | Known-service discovery and general event matching are different customer jobs and may be alternative launch slices. |
| Public result content | Provider cards, ratings, recorded bookings, approximate rates, availability filter, local copy, and nearby markets. | Provider cards, reviews, recorded bookings, distance, starting price, payment status, availability/distance filters, and sponsored section. | `GS-01`; `TB-01` | Public discovery needs governed data and useful inventory; thin generated pages are not a growth strategy. |
| Account timing | Browsing precedes account creation; a submitted quote request creates a planner account/inbox. | Browsing precedes account creation; a submitted request creates an account. | `GS-02`; `TB-02` | Delay account friction until a durable marketplace action when privacy and abuse controls permit it. |
| Provider onboarding | Minimum profile first, then progressive media, preferences, calendar, and completeness enrichment; free-profile approval differs from paid activation. | Identity/contact, service and location precede membership; profile, categories, service area, price, media, calendar, and payment settings are enriched. | `GS-03`; `TB-03` | Separate activation requirements from profile maturity, but do not let payment bypass quality or trust gates. |
| Lead model | Customer-selected requests and general matching; fan-out is capped based on path and supply. | Direct and consented auto-added leads; documented fan-out to 5 or 10 similar providers and a 48-hour auto-add response window. | `GS-02` `GS-04`; `TB-04` | If routing enters scope, record lead type, consent, recipient provenance, cap, deadline, state, and rerouting behavior. |
| Quote lifecycle | Quote, clarification, decline; price, services, cancellation terms, expiration; changed event details invalidate open quotes. | Quote or decline; rate, deposit, message, travel/lodging, breaks, and optional expiry; contact becomes visible after quoting. | `GS-04`; `TB-02` | RFQ, quote, conversation, selection, and booking are separate states; quote acceptance must not silently imply payment or confirmed booking. |
| Contact boundary | Platform thread is encouraged, but paid providers may gain phone contact after responding; provider email remains hidden. | Platform messaging is available, but phone/email become available after a quote. | `GS-04`; `TB-02` | Both use a hybrid boundary. They do not validate Superola's unresolved in-platform communication policy (`A-010`). |
| Availability | Calendar, external sync, and lead blocking exist, but direct requests can still conflict; public visibility is optional. | Travel radius and blackout dates inform discovery; detailed semantics are private. | `GS-05`; `TB-03` | Availability may mean a public signal, routing input, request-time check, or guaranteed bookability. P01 must choose the claim before the feature. |
| Trust | Recorded bookings, weighted verified reviews, response behavior, Top Performer status, limited identity-verification experiment. | Recorded bookings/reviews, tenure, payment acceptance, response badges, and awards; terms disclaim universal identity/capability verification. | `GS-06`; `TB-05` | Define the auditable event behind every `verified` label. Identity, transaction, behavior, and reputation are different trust layers. |
| Organic ranking | Category/travel eligibility, then documented membership, location, recorded bookings, performance status, and tie handling. | Category, location, and recent confirmed booking activity/dollars are documented inputs; weights remain private. | `GS-07`; `TB-06` | Do not copy incumbent-feedback loops or let payment obscure relevance. Ranking needs explicit eligibility, relevance, quality, fairness, and gaming review. |
| Paid placement | Featured membership affects priority and has a visible banner. | Sponsored inventory is visibly separated above organic results; featured placement is sold by category/location. | `GS-07`; `TB-01` `TB-07` | Preserve a hard semantic and presentation boundary between organic relevance and paid placement. |
| Monetization | Free plus current paid memberships; provider booking fee varies by plan; customer service fee; no per-lead charge. | Paid annual membership, category/radius entitlements, possible customer and provider transaction fees, and featured placement. | `GS-08`; `TB-08` | These demonstrate possible revenue mechanisms, not Superola pricing or willingness to pay (`A-003`). Mixed fees add explanation and incentive complexity. |
| Retention | Responsiveness, bookings, reviews, ranking, calendar hygiene, lower paid fees, metrics, and payment protection form a loop. | Confirmed bookings, verified history, reviews/awards, protection, alerts, saved replies, and status visibility form a loop. | `GS-06` `GS-09`; `TB-02` `TB-05` `TB-09` | Retention should arise from repeated marketplace value and honest trust history, not notification volume alone. |

## Pattern classification

The four terms below are comparison dispositions requested by `SRC-012`, not repository evidence labels.

### `LEARN`

- Model known-service browsing and general event-to-provider matching as separate jobs, flows, and potentially separate release choices.
- Treat category, provider service, base location, event location, service area, and availability as distinct concepts.
- Use progressive provider onboarding: a governed minimum for publication followed by measurable enrichment.
- A trustworthy event record can connect requests, quotes, reviews, reputation, and retention even if payment is deferred.
- Design trust as layered evidence: identity checks, marketplace behavior, customer feedback, transaction history, and moderation are not interchangeable.

### `ADAPT`

- Publish service and service-plus-location pages only when real supply and useful, differentiated content justify them.
- If multi-provider routing is tested, require planner consent, cap fan-out, identify lead type, and expose closure/rerouting rules.
- Let calendar/service-area data improve relevance without promising real-time bookability unless Superola can support that promise.
- Keep a canonical platform thread while testing when controlled contact disclosure improves conversion without defeating safety or measurement.
- Keep sponsored inventory separately eligible, ranked, labeled, and measured from organic results.

### `AVOID`

- Do not let membership tier or booking dollars dominate customer relevance or create an unexplained incumbent advantage.
- Do not label a provider, booking, or review `verified` when the platform only recorded a self-asserted external event.
- Do not generate category/location pages without governed taxonomy, sufficient trustworthy supply, canonicalization, and useful local content.
- Do not auto-route requests without explicit customer consent or provider spam/quality controls.
- Do not treat payments, payouts, guarantees, disputes, cancellation enforcement, and post-event reviews as independent checkboxes; they are a coupled operational scope cluster.
- Do not copy opaque or variable fees as a pricing model for Superola.

### `REQUIRES VALIDATION`

- Whether the first customer path is direct provider selection, a general brief, or both.
- Whether a request should reach one selected provider, a curated set, or a capped broadcast.
- Minimum publishable provider profile and category-specific quality gates.
- Meaning and freshness of availability by category (`A-006`).
- Contact-disclosure and in-platform communication policy (`A-010`).
- Starting price/range versus quote-only behavior by service category.
- The event that can truthfully support a verified booking or review.
- Subscription, transaction, customer fee, paid placement, or a deliberately limited combination (`A-003`).
- Whether booking/payment/protection belongs in a later slice (`A-004`).

## Launch-slice consequences — not a final slice

### Foundation if public discovery is included

- Governed service taxonomy and stable provider/service identifiers.
- Base location, event location, service area, country, and timezone semantics.
- Public provider pages with canonical URLs and structured, permissioned facts.
- Organic ranking separated from sponsored eligibility and disclosure.
- Supply/content gates before indexable service/location pages are created.
- Explicit definitions for provider, booking, review, and availability trust claims.

### Additional scope if matching/RFQ is included

- Structured event/request identity, customer consent, recipient selection, fan-out cap, and request states.
- Provider preferences and eligibility, quote/decline states, conversation context, and notifications.
- Abuse, duplicate-request, privacy, response-window, closure, and rerouting policies.
- A defined boundary among quote, selection, booking confirmation, and payment.

### Defer unless separately approved

- Integrated payments, deposits, payouts, refunds, guarantees, disputes, and tax/compliance operations.
- Transaction-derived badges or ranking until the underlying event is auditable.
- Paid placement until organic ranking, supply quality, eligibility, disclosure, and measurement are credible.
- Competitor-specific fee percentages, fan-out counts, response thresholds, or ranking weights.

## Contradictions and unresolved evidence

- GigSalad's signup material refers to three paid levels while current pricing exposes Pro and Featured; Semi-Pro appears discontinued/grandfathered.
- Both platforms can treat externally completed but platform-recorded events as verified booking history. Public evidence does not establish equivalent transaction verification or fraud controls.
- Neither platform discloses full routing, ranking, moderation, dispute, or identity-verification logic.
- Availability filters do not prove fresh, category-consistent, guaranteed availability.
- The Bash's current sponsored inventory is visible, but public detail about current allocation, rotation, pricing, and renewal is incomplete.
- The Bash supports U.S./Canada discovery while its online payments are described as U.S.-only; GigSalad describes U.S./Canada operation. Neither model transfers directly to Mexico.

## Primary source register

All sources accessed 2026-08-11.

### GigSalad

| ID | Provenance | Topic and source |
|---|---|---|
| `GS-01` | `LIVE_PLATFORM` | [Homepage](https://www.gigsalad.com/), [Services](https://www.gigsalad.com/services), [Chicago mariachi results](https://www.gigsalad.com/Music-Groups/Mariachi-Band/IL/Chicago) |
| `GS-02` | `EXTERNAL_PRIMARY` | [How it works](https://www.gigsalad.com/how-it-works), [Requesting quotes](https://help.gigsalad.com/article/74-requesting-quotes), [Reading quotes](https://help.gigsalad.com/article/39-getting-quotes) |
| `GS-03` | `EXTERNAL_PRIMARY` | [Signing up](https://help.gigsalad.com/article/19-signing-up), [Profile completeness](https://help.gigsalad.com/article/57-promokit-100-percent), [Travel preferences](https://help.gigsalad.com/article/53-travel-preferences), [Category management](https://help.gigsalad.com/article/58-manage-your-categories) |
| `GS-04` | `EXTERNAL_PRIMARY` | [Responding to leads](https://help.gigsalad.com/article/36-responding-to-gig-leads), [Sending quotes](https://help.gigsalad.com/article/35-sending-quotes), [Client contact](https://help.gigsalad.com/article/64-client-contact-information) |
| `GS-05` | `EXTERNAL_PRIMARY` | [Managing calendar](https://help.gigsalad.com/article/141-manage-your-calendar), [Blocking leads](https://help.gigsalad.com/article/146-blocking-leads) |
| `GS-06` | `EXTERNAL_PRIMARY` | [Top Performer](https://help.gigsalad.com/article/114-top-performer-status), [Verified reviews](https://help.gigsalad.com/article/83-verified-reviews), [Identity verification](https://help.gigsalad.com/article/183-about-identity-verification) |
| `GS-07` | `EXTERNAL_PRIMARY` | [Search-result placement](https://help.gigsalad.com/article/90-search-result-placement) |
| `GS-08` | `EXTERNAL_PRIMARY` | [Membership pricing](https://help.gigsalad.com/article/148-membership-pricing), [Provider fees](https://help.gigsalad.com/article/97-vendor-service-fees), [Planner fee](https://help.gigsalad.com/article/75-event-planner-service-fee) |
| `GS-09` | `EXTERNAL_PRIMARY` | [Booking](https://help.gigsalad.com/article/37-booking-on-gigsalad), [Guarantee](https://help.gigsalad.com/article/76-worry-free-guarantee), [Dashboard](https://help.gigsalad.com/article/169-understanding-your-dashboard) |

### The Bash

| ID | Provenance | Topic and source |
|---|---|---|
| `TB-01` | `LIVE_PLATFORM` | [Homepage](https://www.thebash.com/), [Quick Quotes](https://www.thebash.com/event-expert), [Mariachi category](https://www.thebash.com/services/mariachi-band), [Chicago mariachi results](https://www.thebash.com/search/mariachi-band-chicago-il) |
| `TB-02` | `EXTERNAL_PRIMARY` | [FAQ](https://www.thebash.com/help), [Lifecycle of a Lead](https://itg.thebash.com/lifecycle-of-a-lead) |
| `TB-03` | `LIVE_PLATFORM` + `EXTERNAL_PRIMARY` | [Caterer signup](https://www.thebash.com/signup/caterer-landing), [Building Your Profile](https://itg.thebash.com/building-your-profile), [Customizing Membership](https://itg.thebash.com/customizing-your-membership), [Category selection](https://itg.thebash.com/selecting-your-categories) |
| `TB-04` | `EXTERNAL_PRIMARY` | [Auto-Add](https://itg.thebash.com/auto-add-gigs), [Responding to Leads](https://itg.thebash.com/best-practices-for-responding-to-leads) |
| `TB-05` | `EXTERNAL_PRIMARY` | [Reviews](https://itg.thebash.com/understanding-client-reviews), [Badges & Awards](https://itg.thebash.com/new-profile-announcement-badges), [Terms](https://www.thebash.com/terms-of-use) |
| `TB-06` | `EXTERNAL_PRIMARY` | [How Do You Rank?](https://itg.thebash.com/search-results), [New Member Tips](https://itg.thebash.com/tips-for-new-members) |
| `TB-07` | `LIVE_PLATFORM` + `EXTERNAL_PRIMARY` | [Chicago sponsored results](https://www.thebash.com/search/mariachi-band-chicago-il), [Featured Profiles](https://itg.thebash.com/featured-profiles-learn-more) |
| `TB-08` | `LIVE_PLATFORM` + `EXTERNAL_PRIMARY` | [Pricing & Payments](https://www.thebash.com/pricing-payments), [Caterer signup offer](https://www.thebash.com/signup/caterer-landing) |
| `TB-09` | `EXTERNAL_PRIMARY` | [Booking Guarantee](https://www.thebash.com/buyer-protection), [Event Protection](https://www.thebash.com/event-protection) |

## Reconsideration triggers

Refresh this benchmark before a pricing, ranking, routing, sponsored-placement, trust, booking, or payment decision; when either platform materially changes public terms; or after owner materials and live Superola evidence establish a narrower decision problem.
