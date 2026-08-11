# Feature Inventory — Outcome-Led P01 Backlog

This inventory records hypotheses without converting them into approved requirements. Evidence status and provenance follow `docs/00-context/glossary.md`.

## Proposed V1 capability set

| Area | Candidate capability | Actor outcome served | Evidence / treatment |
|---|---|---|---|
| Discovery | Governed service/category + geography search, meaningful empty results, organic relevance | Customer finds plausible supply; provider reaches relevant demand; operator measures local liquidity. | Need `CONFIRMED`; model `PROPOSED`. |
| Provider presence | Establish a business, publishable profile, service area, category-appropriate media and facts; claim only for an approved imported/preloaded cohort | Customer evaluates fit; provider controls a credible presence. | Profile concept `CONFIRMED`; launch model `PROPOSED`; claim path conditional. |
| Customer participation | Just-in-time account/contact verification before a durable RFQ is delivered; owned request status, withdraw, and close | Preserve low-friction discovery while making requests reachable, consented, and recoverable. | `PROPOSED`; owner/product plus privacy/security validation required. |
| Requests | One direct RFQ to one customer-selected provider, with an independent lifecycle | Customer expresses a concrete need; provider receives relevant context without automatic routing. | `PROPOSED — OWNER VALIDATION REQUIRED`. A reused draft requires deliberate customer action and fresh confirmation for each recipient. |
| Provider response | Decline, request clarification, or provide a category-appropriate quote/offer | Customer gets a useful answer; provider retains control. | `PROPOSED`. Universal quote fields/version/expiry are not assumed; a quote is not booking. |
| Communications | Asynchronous in-platform text conversation attached to the RFQ | Both sides preserve context toward a decision; operator can measure progression and address reports. | `PROPOSED`; provenance `DAVID_DIRECTIVE`; no presence, typing, read receipts, attachments, audio/video, or realtime transport assumption. |
| Notifications | One approved minimum request/response alert channel with minimal sensitive content and delivery/failure visibility | Prevent silent marketplace failure. | `PROPOSED`; separate from messages. SMS, push, and additional channels require evidence and approval. |
| Trust/integrity | Provider contact control, just-in-time customer contact/account control, duplicate/report handling, publication/moderation minimums, response/freshness indicators; claim resolution only for an approved import cohort | Reduce false, stale, abusive, or misleading marketplace interactions. | `PROPOSED`; scope is risk-based, not universal identity proofing. |
| Operator capability | Exception procedures for taxonomy/publication, duplicates/reports, stale or abusive profiles, and RFQ lifecycle; conditional imported-profile claims | Keep the marketplace operable and claims truthful. | `PROPOSED`; start manual/exception-only and derive tooling only from measured queues. |
| Measurement | Supply, demand, response, quote, conversation, outcome, monetization, and quality events | Test whether both sides receive value and whether operations are sustainable. | `PROPOSED`; no fabricated targets. |
| Human-public distribution | Useful human-rendered public provider pages with stable URLs, permissioned facts, locale/geography semantics, freshness, and correction paths | Let people browse credible supply without treating publication as automatic crawler authorization. | `PROPOSED`; provenance `DAVID_DIRECTIVE` + `TECHNICAL_DISCOVERY`. Indexability and each crawler class are separately approval-gated. |
| Monetization evidence | Free onboarding/eligibility and value/trial measurement events; a later subscription reconsideration seam | Avoid charging or building entitlement automation before value while preserving the owner-confirmed subscription hypothesis. | Subscription concept `CONFIRMED`; launch timing/package `PROPOSED`; implementable entitlements deferred until validated. |

## Category variability — inputs for P02, not a universal schema

`PROPOSED`; provenance `TECHNICAL_DISCOVERY`. The following compares representative archetypes as hypotheses for owner/provider validation. It identifies candidate semantics rather than requirements or one all-category record.

| Dimension | Performer (mariachi/band/DJ) | Venue | Photographer/decorator/service professional | Catering/cake | Transportation |
|---|---|---|---|---|---|
| Service area | Base location + travel radius/markets; travel willingness can depend on event value. | Fixed physical location; customer travels to venue. | Mobile radius/markets; decorator may need delivery/setup zones. | Delivery/service zones; kitchen/base location differs from event site. | Origin/destination corridor, operating/permit area, depot location. |
| Availability | Date + time window, setup/travel buffers, possibly multiple acts/teams. | Date/time inventory by room/space; holds and setup/cleanup matter. | Session windows and staff/equipment concurrency. | Production capacity, delivery slot, order lead time. | Vehicle/driver availability across pickup, trip, wait, and return. |
| Pricing | Hour/set/package, lineup size, travel, equipment, overtime. | Rental period, room, capacity, day/time, minimum spend, extras. | Hour/package/deliverable or project scope, team, materials, usage rights. | Per guest/item/tier, minimum order, staffing, delivery/setup. | Vehicle type, passengers, distance/time, minimum hours, wait, tolls. |
| Duration | Performance plus breaks, setup, teardown, travel. | Access window plus event/setup/cleanup. | Coverage or service window plus preparation/delivery. | Preparation lead time plus service/delivery window. | Pickup-to-dropoff and waiting/return legs. |
| Capacity | Performers/production configuration; audience fit may be descriptive. | Guests, layouts, rooms, accessibility/parking constraints. | Simultaneous jobs, crew, equipment, deliverable volume. | Guests/servings/items and production throughput. | Passenger/luggage capacity per vehicle/fleet. |
| Media | Audio/video central for performers; photos and repertoire. | Photos/video, floor plans, amenities. | Portfolio central; before/after or style examples. | Menu/item photography, dietary/allergen information. | Vehicle/fleet photos, features, condition. |
| Quote structure | Package/set length + travel/equipment/overtime/terms. | Space/date/time + capacity + included services/deposit/terms. | Scope/deliverables + hours/materials/travel/rights. | Guest/item count + menu/options/staff/delivery/tax/terms. | Vehicle(s) + itinerary + time/distance/wait/tolls/gratuity/terms. |
| Inventory/resources | Performers, instruments, sound/lighting, duplicate teams. | Rooms, tables/equipment, parking, allowed vendors. | Staff, gear, props/materials, production slots. | Ingredients, kitchen throughput, serving equipment, staff. | Vehicles, drivers, seats, permits, maintenance status. |
| Travel | May add fees, lodging, setup buffer, or decline. | Mostly customer travel; offsite venue services are separate. | Provider travel, delivery, setup/strike. | Delivery/service travel with freshness/logistics constraints. | Travel is the service; route and deadhead shape feasibility. |

### Candidate cross-category invariants

- Governed provider/business identity and one or more offered services.
- Base location is distinct from service area and event/service location.
- A request needs event/service context, desired time/date when relevant, location, and category-specific details.
- Availability is stateful, sourced, and time-sensitive; it must not be reduced to an unconditional universal boolean.
- Providers need quote/clarify/decline control and a conversation linked to the request.
- Price representations need currency, basis, inclusions/exclusions, conditions, and freshness rather than a naked number.
- Media needs ownership/permission, moderation, accessibility, and category relevance.
- Travel, duration, capacity, resources, and lead time can affect eligibility and quote feasibility even when the field names differ.

### Category-specific behavior P02 must evaluate

- Whether the service uses fixed-location inventory, mobile service area, routes, delivery zones, or combinations.
- Which availability unit exists: team, room, person, production slot, vehicle, or another resource; whether holds/concurrency matter.
- Which request attributes and quote components are required per launch category.
- Whether pricing is public indicator, starting price, package, unit-based estimate, or quote-only.
- How duration, capacity, travel, lead time, and resources affect eligibility without one brittle universal schema.
- Whether a provider may operate multiple teams/locations/services and how ownership/staff permissions work.

P01 explicitly does **not** design a universal category schema or preselect an architecture shape. After launch categories and evidence are selected, P02 must compare viable models such as a narrow launch-category model, a shared core with extensions, and governed metadata/configuration against complexity, operability, migration, and change cost.

## Staged or optional capabilities

| Capability | Treatment | Gate |
|---|---|---|
| Natural-language intent → structured constraints → deterministic search | Separate non-critical-path experiment | Requires independent approval, budget/cost ceiling, quality baseline, privacy boundary, deterministic fallback, and stop rule. |
| Simple provider subscription and implementable entitlements | `PROPOSED` staged V1.x | Owner validates package/trial intent; providers have received observable value; category/geography liquidity, billing/compliance, cancellation/support, and measurement gates pass. |
| Sponsored placement | Owner concept `CONFIRMED`; implementation staged V1.x or later | Credible organic marketplace, sufficient eligible inventory and demand, explicit disclosure/fairness/allocation, separate ranking, attributable measurement, and provider demand. |
| Category/location public landing pages | Conditional | Useful differentiated content, governed taxonomy, credible supply, canonicalization, localization, and freshness. |

## Explicitly deferred from the recommended V1

- General-event brief automatically routed or broadcast to multiple providers.
- Booking, customer payment, deposits, provider payout, refunds, disputes, guarantees, and transaction protection.
- Post-service completion and reviews that imply an unobservable booking/service event.
- Universal real-time availability or calendar synchronization.
- Natural-language intent on the V1 critical path; any experiment is separate, independently approved, budgeted, and stoppable.
- Native mobile application unless owner/user evidence changes the channel decision.
- External profile acquisition/scraping until business, legal, privacy, copyright, terms, consent, and accuracy reviews pass.
- External-agent write actions and MCP/A2A/A2UI/UCP/ACP adapters.
- Any protocol-only V1 deliverable or claim that AI replaces governed search/ranking.

## Trust minimum for the proposed V1

1. Verify control of an account/contact channel for providers and just in time before a customer's durable RFQ is delivered; compare the friction/integrity tradeoff in P04. Do not describe this as universal identity verification.
2. Require business/profile control for new providers. Add an authorized claim/dispute path only if an audited imported/preloaded-profile cohort is approved.
3. Prevent obvious duplicate creation where possible and provide duplicate reporting/manual exception resolution.
4. Establish minimum publication quality, prohibited content, media rights acknowledgement, report intake, moderation state, and deactivation path.
5. Track provider request receipt, response/decline/no-response, notification failure, and freshness without publishing punitive or misleading labels before policy validation.
6. Minimize contact disclosure; preserve an in-platform thread; record the unresolved owner policy for when direct details may be exchanged.
7. Keep sponsored eligibility, ordering, labels, and measurement separate from organic results before any paid inventory ships.
8. Reserve `verified` terminology for a precisely defined auditable event. V1 has no `verified booking` unless Superola can prove what event occurred and how.

Expensive universal government-ID or business-registry verification is not assumed; add stronger checks only where abuse evidence, category risk, law, or a specific trust claim requires them.
