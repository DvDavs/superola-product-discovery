# Measurement Plan — Day-One Marketplace Telemetry

## Purpose and rules

The launch must generate evidence that can decide whether to stop, correct, or expand the marketplace. This document defines metrics; it does not set targets. Targets, thresholds, and business success criteria remain `OWNER/BUSINESS VALIDATION REQUIRED`.

Day-one rules:

- Every relevant event carries governed `market`, `locale`, `category/archetype`, and `placementBasis` where applicable; these dimensions cannot be reconstructed reliably later.
- Use pseudonymous actor/session identifiers. Never send contact data, request/conversation free text, precise private location, verification secrets, or operator notes into analytics.
- Event names do not claim outcomes the platform cannot prove. `reported_outcome` is not booking, payment, conversion, or service completion.
- `unknown` (customer explicitly reports uncertainty) and `unreported` (no report) remain separate.
- Notification delivery, notification interaction, and marketplace state are separate. Failed delivery is never `NoResponse`.
- Composer abandonment and verification abandonment are separate measures.
- Organic and sponsored placement remain separate in events and reporting.
- Cash infrastructure cost and illustrative human operational sensitivity are reported separately.

## Event-quality envelope

Every event should include: event name/version, occurred-at instant, market, locale, category/archetype where relevant, pseudonymous actor/session, surface/entry path, lifecycle identifiers appropriate to the event, and placement provenance where relevant. Event schemas must define allowed properties; unknown or free-text properties are rejected.

Instrumentation acceptance requires automated schema validation, duplicate/idempotency checks for submission events, documented lifecycle-to-event mapping, privacy review, and a dashboard/query that exposes missing required dimensions.

## Supply metrics

| Metric | Definition | Decision supported |
|---|---|---|
| Publishable providers | Distinct providers that currently meet every applicable publication gate, whether published or awaiting an operator action. | Whether supply acquisition is producing viable inventory. |
| Published providers | Distinct discoverable ProviderProfiles in the measured market cell. | Actual searchable supply. |
| Provider profile completion | Completion of governed required attributes by archetype and requirement version; not a self-scored percentage across irrelevant fields. | Where onboarding fails and whether quality thresholds are realistic. |
| Category coverage | Published providers per governed category/archetype and market cell. | Pilot-category health and zero-result explanation. |
| Geography coverage | Published eligible supply by approved coarse place/service-area unit. | Whether search coverage exists without exposing precise location. |
| Request-intake status | Providers in accepting, paused, unconfirmed/stale states and state changes. | Whether published supply can receive demand honestly. |
| Active/inactive | Governed activity/freshness definition and coverage, reported separately from availability. | Supply maintenance and future ranking research. |
| Time/operator effort to publish | Elapsed time and operator minutes from onboarding start to publication or terminal stop reason. | Human TCO and gate friction. |

## Discovery metrics

| Metric | Definition | Decision supported |
|---|---|---|
| Searches | Valid discovery queries, segmented by market/category/place and entry path. | Demand shape. |
| Zero-result rate | Queries returning zero eligible results divided by valid queries, with category-gap versus coverage-gap reason. | Supply acquisition and taxonomy/geography correction. |
| Category selected | Governed category/archetype selection event. | Demand mix and composer entry. |
| Place selected | Governed coarse place selection event; never raw precise address in analytics. | Geographic demand and coverage gaps. |
| Profile views | Unique/total provider profile views from a result or other approved entry path. | Provider evaluation behavior. |
| Result-to-profile CTR | Profile opens divided by eligible result impressions, segmented by position and `placementBasis`. | Result quality and placement effects. |
| Candidate set / undetermined | Eligible result count and geographic eligibility state, including `undetermined`. | Search health and service-area ambiguity. |
| Alternative-provider impressions | Eligible alternative cards/rows rendered, with source context and placement provenance. | Whether alternatives are actually offered. |
| Alternative-provider clicks | Alternative profiles selected from an alternative surface. | Alternative relevance. |
| See More usage | Explicit transition from limited alternatives to the alternative list. | Need for breadth and placement design. |

## Demand and response metrics

| Metric | Definition | Decision supported |
|---|---|---|
| Request started | A RequestDraft begins, by entry path and archetype. | Demand intent and funnel denominator. |
| Draft abandonment | Drafts that do not reach review/send, segmented by step; expiry window remains policy-controlled. | Form/category friction. |
| Verification completion | Pending-verification requests that verify within the approved window. | Identity-path viability. |
| Verification abandonment | Pending-verification requests that lapse/are abandoned without verification, reported separately from draft abandonment. | Verification friction. |
| Request submitted | Customer explicitly confirms a recipient and submission; idempotent duplicate actions count once. | Intentional sends. |
| Request delivered | Verified request becomes visible to an eligible recipient. | Marketplace opportunity denominator. |
| Clarification | First/subsequent provider clarification events. | Request quality and archetype-question adequacy. |
| Decline | Provider decline, with governed reason where approved. | Fit, availability, location, price, and request-quality learning. |
| Offer | Provider creates or revises an offer, without implying acceptance/booking. | Quote/offer behavior. |
| NoResponse | Request crosses the approved observable window without provider response; non-punitive and non-terminal. | Provider responsiveness and recovery need. |
| Conversation activity | Authored message activity by party and lifecycle state; no message body in analytics. | Whether interaction continues after first response. |
| Reported outcome | Customer reports proceeding, not proceeding, or unknown, with provenance and optional governed reason if separately approved. | Marketplace usefulness without transaction claims. |

## Marketplace measures

| Metric | Definition | Important qualification |
|---|---|---|
| Request-to-response rate | Delivered requests receiving clarification, decline, or offer within the observation window. | Report window and late responses separately. |
| Time to first response | Time from delivery to first provider response; distribution, not only average. | Exclude notification failure as a causal assertion; segment it. |
| Offer rate | Delivered requests receiving at least one offer. | An offer is not accepted booking or revenue. |
| No-response rate | Delivered requests entering NoResponse. | Non-terminal; track late recovery. |
| Customer-proceeding rate | Requests with customer-reported `proceeding` divided by eligible reported-outcome denominator. | Report response rate and `unreported` shown beside it. |
| Provider coverage | Eligible/responding providers per market cell and demand concentration across providers. | Prevent aggregate health from hiding thin cells or overload. |
| Repeat demand | Customers starting a later distinct need/request in the approved observation definition. | Separate same-need alternative requests from new service/need. |
| Operator load | Cases, minutes, backlog age, resolution time, escalation and private-access share by queue. | Report workload and capacity, not just case count. |

## Alternative-provider effectiveness

The alternatives feature is useful only if it adds deliberate, eligible opportunities and is associated with additional useful outcomes without increasing abuse or confusing customers. Causal incrementality requires the separate design gate below.

| Funnel step | Event/metric | Required segmentation |
|---|---|---|
| Shown | `alternative_shown` / eligible impressions | Prior request/profile context; source state such as profile view, Declined, NoResponse, RecipientIneligible; organic/sponsored basis. |
| Explored | `alternative_clicked`, `alternative_see_more` | Position, category/place compatibility, source surface. |
| Reuse started | `reused_request_started` | Same need/different provider versus new service using EventContext only. |
| Reviewed | `reused_request_reviewed` | Fields retained, discarded, or requiring reconfirmation as governed flags—not values. |
| Sent | `reused_request_submitted` | New recipient, explicit confirmation, duplicate-prevention outcome. |
| Responded | `alternative_provider_response` | Clarification/decline/offer/NoResponse and time to first response. |
| Alternative-associated outcome | `alternative_reported_outcome` | Proceed/not proceeding/unknown plus whether the original path had already produced an outcome; descriptive, not causal. |

Core descriptive measures are impression→click, click→reuse start, reuse start→new request delivered, response rate/time, and alternative-associated reported proceeding. Also track customers with multiple open requests, reports/blocks, duplicate attempts, and provider complaints. No automatic fan-out event should exist in V1.

An **incremental successful outcome** is a causal measure and may be reported only under a predeclared comparison design—such as randomized eligible exposure, a defensible holdout, or another reviewed causal design—with assignment/exposure, denominator, observation window, contamination, and uncertainty documented. Without that design, P06 permits only descriptive “alternative-associated” language.

## Monetization measures — only once enabled

| Metric | Definition |
|---|---|
| Eligible sponsored inventory | Slots/candidates that pass organic eligibility and the approved paid-inventory policy. |
| Sponsored impressions/clicks | Disclosed paid placements rendered and selected, with position and market cell. |
| Sponsored leads | New delivered single-recipient requests attributable under the approved window; never inferred from a profile view alone. |
| Spend/revenue | Amount and currency under the approved billing model; refunds/credits reported separately. |
| Organic vs sponsored outcomes | Comparable funnel/outcome distributions with provenance; never blend paid and organic. |
| Provider willingness/retention | Research interest, paid uptake if offered, renewal/cancellation, and stated reason. |
| Trust/ops effect | Complaints, reports, support minutes, inventory rotation/fill, and organic-result displacement. |

## Trust and operations metrics

| Metric | Definition |
|---|---|
| Report volume | Reports created by type/surface, distinct from blocks. |
| Block volume | Party blocks and subsequent prohibited-contact attempts. |
| Moderation queue | New cases, backlog age, resolution time, outcome, escalation and minutes per case. |
| Claim queue | Zero unless an approved legacy cohort exists; then cases, attempts, disputes, time, outcome, and fraud signals. |
| Support volume | Contacts by governed reason and stage, with handling time. |
| Provider disputes | Disputes about ownership, moderation, lead quality, sponsorship, or future transactions kept distinct. |
| Failed notifications | Delivery failures by event type/channel, retry outcome, and marketplace state. |
| Failed verification | Verification delivery/attempt failures separate from user abandonment. |

## Data required before advanced alternative ranking

| Signal | Minimum evidence before use | Primary hazards |
|---|---|---|
| Price | Category-specific comparable `PriceStatement` semantics: currency, basis, inclusions, exclusions, conditions, as-of/freshness; adequate coverage; missing-data policy; `Q-040` resolved. | False comparability, outdated prices, pressure to show a naked number, bias toward simple services. |
| Recent provider activity | Governed definition tied to legitimate provider maintenance or marketplace action; freshness window; adequate coverage; privacy/fairness review; anti-gaming and treatment of new providers. | Rewards clicks/logins rather than service quality; incumbency/new-provider bias; manipulability. |
| Historical response speed | Sufficient new-platform delivered-request history; consistent clock start/stop; notification failures and pauses handled; distribution and minimum sample; decline behavior treated fairly; no legacy inference. | Penalizes thoughtful responses, rewards fast declines, sparse-sample volatility, leakage from Demand into Provider ownership. |

Until those requirements hold, alternative ranking should rely on deterministic eligibility, transparent stable organic rules, and controlled exploration. Sponsorship may place only eligible providers and must not masquerade as any quality signal.

## Pilot review packet

Each review should show, by approved market cell:

1. supply and coverage;
2. search/zero results and profile evaluation;
3. request, verification, delivery, response, offer, NoResponse, and reported outcomes;
4. alternatives funnel if enabled;
5. trust/support/queue load and notification failures;
6. cash infrastructure cost and human operational sensitivity separately;
7. qualitative findings and data limitations;
8. owner-defined stop, correct, or expand decision.

No dashboard may hide `unreported`, merge composer with verification abandonment, mix organic with sponsored, or label customer-reported outcomes as transactions.
