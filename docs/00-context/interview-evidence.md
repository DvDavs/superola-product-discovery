# Owner Interview Evidence

Canonical source representation: [sources/owner-interview-superola-2026-08-11.md](sources/owner-interview-superola-2026-08-11.md) (`SRC-001`).

## Interpretation rules

- `CONFIRMED` rows have provenance `OWNER_INTERVIEW` unless explicitly stated otherwise; they do not imply independent technical or data verification.
- Qualifiers such as owner-reported, illustrative, and unvalidated sit alongside the base evidence label; they do not create additional labels.
- `ASSUMPTION` identifies the WordPress/MyListing claim: its referenced `EXTERNAL_SECONDARY` source is unavailable and no direct platform observation currently supports it.
- `PROPOSED` rows have provenance `TECHNICAL_DISCOVERY`; they are discovery models or interpretations, not approved requirements.
- Owner evidence and David's project treatment are intentionally separate. For example, the owner discussed local advertising and roughly six months; treating them as non-software scope and a non-contractual planning hypothesis is provenance `DAVID_DIRECTIVE`.

## Traction and reach

| Evidence | Structured claim | Verification need |
|---|---|---|
| `CONFIRMED (owner-reported, unaudited)` | The legacy site accumulated approximately 43,000 registered service providers despite limited maintenance. | Legacy database and account-quality audit. |
| `CONFIRMED (owner-reported, unaudited)` | Example counts were approximately 4,000 DJs, 5,000 bands, 4,000 norteño groups, and 5,000 mariachis. | Category normalization and database counts. |
| `CONFIRMED (owner-reported, unaudited)` | Registrations and customer/provider interactions generated substantial platform email. | Mail, application, and analytics evidence. |
| `CONFIRMED (owner-reported, unaudited)` | Reach included the United States, Mexico, and possibly other countries. | User/provider geography and traffic analysis. |

## Marketplace and provider profiles

| Evidence | Structured claim | Verification need |
|---|---|---|
| `CONFIRMED (owner-reported, unaudited)` | Provider registrations included musicians/entertainment and many other event-service categories. | Validate current taxonomy, actual category history, and desired future breadth. |
| `CONFIRMED`; provenance `DAVID_DIRECTIVE` | The repository must treat Superola as a two-sided marketplace rather than only a static directory. | Validate exact transaction and trust boundaries for MVP; do not attribute this guardrail to the owner. |
| `CONFIRMED` | The owner described profiles with gallery, geography, calendar/availability, media where relevant, profile information, messaging, and service discovery. | Prioritize attributes and flows by provider category. |
| `PROPOSED` | Customers should evaluate service fit, geographic fit, date availability, style, and quote/contact options. | Owner/user validation and journey research. |

## Monetization

| Evidence | Structured claim | Verification need |
|---|---|---|
| `CONFIRMED (owner hypothesis, unvalidated)` | Providers may eventually pay a small recurring monthly subscription. | Willingness-to-pay and plan/entitlement discovery. |
| `CONFIRMED (illustrative, not approved)` | A scenario of 100 providers in a city at roughly USD 40/month implied roughly USD 4,000/month. | Pricing, churn, fees, tax, and unit-economics analysis. |
| `CONFIRMED (owner hypothesis, unvalidated)` | A free period, possibly three months, and gradual premium features were discussed. | Trial rules, conversion goals, and entitlement scope. |
| `CONFIRMED (business strategy idea)` | A portion of area revenue might be reinvested in local advertising. | Business validation; no current software requirement. |

## Sponsored placement

| Evidence | Structured claim | Verification need |
|---|---|---|
| `CONFIRMED (owner concept, unvalidated)` | Category + geography could define sponsored inventory, such as Mariachi + Chicago. | Inventory, eligibility, market depth, and policy design. |
| `CONFIRMED (illustrative, not approved)` | About ten promoted positions and pricing around USD 100/month were discussed. | Demand, pricing, disclosure, fairness, and unit economics. |
| `CONFIRMED (owner concept, unvalidated)` | Sponsored providers could rotate rather than permanently owning rank one; featured providers/videos were also mentioned. | Ranking separation, rotation, measurement, and user-trust research. |
| `PROPOSED` | Promotion may require campaigns, targeting, time windows, fees/budgets, impressions, clicks, and conversions. | Domain discovery; do not encode as a final model. |

## AI ideas

| Evidence | Structured claim | Verification need |
|---|---|---|
| `PROPOSED`; provenance `DAVID_DIRECTIVE` | Customers might describe an event need in natural language and receive suitable provider/service results. | Confirm owner/user value, then evaluate deterministic constraint mapping, quality, latency, and cost. |
| `PROPOSED` | Natural-language interpretation should produce structured constraints before geographic, availability, service, and ranking logic. | Architecture research after product validation. |
| `PROPOSED`; provenance `DAVID_DIRECTIVE` | Public/external provider discovery could create preliminary profiles that real providers later claim. | Confirm business ownership, then perform legal, privacy, copyright, platform-terms, robots/API, accuracy, consent, and verification review. |

## Communications

| Evidence | Structured claim | Verification need |
|---|---|---|
| `PROPOSED`; provenance `DAVID_DIRECTIVE` | The intended product should ideally keep customer/provider interaction inside Superola rather than immediately exposing external contact channels. | Confirm owner policy and validate incentives, safety, and acceptable escape hatches. |
| `CONFIRMED (owner concept, unvalidated)` | Email/SMS may relay alerts or messages without unnecessarily exposing customer contact information. | Consent, deliverability, privacy, abuse, retention, and compliance design. |
| `PROPOSED` | Conversations/messages and delivery notifications are distinct bounded capabilities. | Domain and integration design after product discovery. |

## Legacy issues

| Evidence | Structured claim | Verification need |
|---|---|---|
| `CONFIRMED (owner-reported)` | Map/location behavior is broken or unreliable and providers can appear in incorrect locations. | Live-system and data audit. |
| `CONFIRMED (owner-reported)` | Data saturation and provider-entered category creation likely degraded taxonomy quality. | Taxonomy, duplicate, and content audit. |
| `CONFIRMED (owner-reported)` | Plugins or legacy implementation details may contribute to problems. | Code/configuration/runtime audit. |
| `ASSUMPTION`; provenance `EXTERNAL_SECONDARY` | The P00 brief references unavailable prior analysis suggesting WordPress/MyListing or similar tooling. | Obtain and assess the referenced analysis or independently verify the implementation through an authorized audit. |
| `CONFIRMED` | The owner prefers a proper rebuild with a different visual design over continued patching. | Clarify migration, coexistence, cutover, and design constraints. |

## Working expectations and timing

| Evidence | Structured claim | Verification need |
|---|---|---|
| `CONFIRMED` | The owner expects technical feedback and is open to better approaches rather than blind implementation. | Establish decision and approval cadence. |
| `CONFIRMED` | The owner is open to justified specialist help while caring about controlled spending. | Define capability gaps and hiring thresholds. |
| `CONFIRMED` | Brainstorming and discovery should precede commitment to a final plan. | Execute staged discovery. |
| `CONFIRMED` | A fresh feature/monetization document and UI/UX work exist but have not been received. | Obtain, register, and reconcile these materials. |
| `CONFIRMED (informal)` | Approximately six months was discussed in relation to a serious production V1. | `CONFIRMED`; provenance `DAVID_DIRECTIVE`: treat it as non-contractual and subject to scope, evidence, dependencies, team capacity, and uncertainty. |
