# Glossary

## Evidence labels

| Term | Meaning |
|---|---|
| `CONFIRMED` | Directly supported by an identified authoritative source for that claim. Provenance identifies whether the authority is the owner, David, project data, or another source; owner-reported but unaudited facts must say so. |
| `OBSERVED` | Directly observed in an existing system or artifact; not automatically an intended future requirement. |
| `PROPOSED` | A recommendation or model created during discovery; not approved merely because it is documented. |
| `ASSUMPTION` | A belief that requires validation and has an owner in the assumption register. |
| `FUTURE` | Intentionally considered beyond the initial product scope. |
| `REJECTED` | Intentionally not selected, with rationale preserved when material. |

## Provenance

Evidence status and provenance answer different questions:

- **Evidence status:** how the claim is treated (`CONFIRMED`, `OBSERVED`, `PROPOSED`, `ASSUMPTION`, `FUTURE`, or `REJECTED`).
- **Provenance:** where the claim or directive originated.

| Provenance | Meaning |
|---|---|
| `OWNER_INTERVIEW` | Owner-attributed statement preserved from interview evidence; not independently verified unless stated. |
| `DAVID_DIRECTIVE` | Project, workflow, scope, or presentation instruction supplied by David; not automatically an owner requirement. |
| `LIVE_PLATFORM` | Direct observation from an authorized audit of the current/public platform. |
| `PROJECT_DATA` | Authorized internal data, schema, analytics, configuration, or repository state. |
| `OWNER_MATERIAL` | Versioned document, design, or other artifact supplied by the owner. |
| `EXTERNAL_PRIMARY` | Official first-party documentation, API reference, pricing page, law, standard, or research source. |
| `EXTERNAL_SECONDARY` | Third-party analysis or report that requires source-quality assessment. |
| `TECHNICAL_DISCOVERY` | Recommendation or synthesis produced during product/technical discovery. |

Use provenance on material claims where owner intent, project direction, observation, data, or recommendation could be confused. Keep it in a separate field or clause; never combine it into a new evidence label. `CONFIRMED` + `OWNER_INTERVIEW` confirms that the owner statement was reported, not that its numerical or technical content is independently true. `DAVID_DIRECTIVE` and `TECHNICAL_DISCOVERY` must not be presented as owner requirements without owner approval.

## Product and marketplace terms

Terms marked **P02** were added or amended by P02 domain modelling. All P02 terms are `PROPOSED`; provenance `TECHNICAL_DISCOVERY`. Naming is treated as an architectural constraint here, not as copywriting: a term that asserts more than the platform can evidence is a defect.

| Term | Working definition | Status |
|---|---|---|
| Customer | Person or organization planning an event and seeking services. **A role, not an entity or an account type** (P02). | `CONFIRMED`; provenance `OWNER_INTERVIEW`. Role modelling `PROPOSED` (`ADR-004`). |
| Provider | Business or service professional offering event-related services. **A role, not an entity or an account type** (P02). | `CONFIRMED`; provenance `OWNER_INTERVIEW`. |
| ~~Vendor~~ | **P02: retired for the supply side.** `Provider` is canonical for marketplace supply; `Supplier` or `third party` refers to technology suppliers. The collision was real: `AGENTS.md` and `architecture-overview.md` use "provider/vendor" for external suppliers while the glossary used it for marketplace supply. | `PROPOSED` retirement. `docs/04-ux/vendor-flow.md` would need renaming if P04 keeps it. |
| Account | **P02.** The authenticated principal that can act in the marketplace. Opaque, permanent, never an email address. Not typed by marketplace role. | `PROPOSED` (`ADR-004`). |
| Person | **P02.** The human behind an Account. **A concept only — never a record**, because Superola cannot prove human identity in V1. | `PROPOSED`; deliberately unmodelled. |
| Business | The commercial party that offers services and bears accountability, ownership, and future entitlement. A sole practitioner **is** a Business. Outlives the Account that created it. | `PROPOSED` concept, refined by P02. |
| BusinessMembership | **P02.** A revocable relation binding an Account to a Business with a role — **never an owner reference on Business**. V1 ships the relation with one role value. | `PROPOSED` (`A-023`). |
| ProviderProfile | Marketplace representation of a provider, carrying presentation, credibility, media, publication state, and freshness. **P02: distinct from `Business` and from `ServiceOffering`.** | `PROPOSED`; `ADR-002`. |
| ~~Listing~~ | **P02: retired.** It conflated profile, offering, and directory entry — part of what P01 diagnosed. Use `ProviderProfile` and `ServiceOffering`. | `PROPOSED` retirement. |
| ServiceOffering | **P02.** One governed category of service a provider offers. **The unit of discovery eligibility** — discovery matches offerings, not profiles. Service area attaches here because archetype semantics differ per offering. | `PROPOSED`. |
| Category | A governed taxonomy node customers search by and operators measure by. **Only operators create categories**; provider input can only ever be a `CategoryProposal`. | `PROPOSED`; refined by P02 (`ADR-007`). |
| CategoryArchetype | **P02.** The service-shape family determining service-area semantics, resource concept, and offer policy. **Adding a Category is a governance act; adding an Archetype is an engineering act.** | `PROPOSED` (`ADR-007`, `A-018`). |
| CategoryAttributeDefinition | **P02.** The governed definition of one attribute an offering may carry, with unit, value domain, mandatory null semantics, and revocable filterability. Never provider-writable. | `PROPOSED`. |
| Filterable attribute | **P02.** An attribute permitted as a search filter. Requires all seven criteria in `domain-model.md` §3.4 — most importantly explicit null semantics, per-`Market` coverage, and language-neutral values, which are the three that produced the legacy saturation defect. | `PROPOSED`. |
| ProviderNarrative | **P02.** Free-text provider description. **Never filterable, never taxonomy, never an attribute source.** | `PROPOSED`. |
| Event | Customer occasion with attributes such as type, date, location, guest count, and budget. **P02: downgraded to `EventContext`, a value object owned by a request.** `Event` as an aggregate is reserved for the Transaction Extension, where bookings and reviews must attach to one real-world occasion. | `PROPOSED` value object; `Event` aggregate `FUTURE`. |
| ServiceRequest / RFQ | Customer request for a provider in an event context. **P02: `ServiceRequest` is the canonical entity name; `RFQ` remains the accepted owner-facing synonym.** Exactly one recipient offering, immutable after delivery. | `PROPOSED` entity. |
| RequestDraft | **P02.** The customer's private, reusable, pre-delivery request content. **Has no recipient and is never visible to any provider.** Required by the B0 envelope, and it is the seam that makes multi-provider matching a policy change rather than a redesign. | `PROPOSED` (`ADR-003`). |
| ProviderResponse | **P02.** The provider's answer to a request, of kind `clarification`, `decline`, or `offer`, with an optional decline reason. **Replaces `Quote` as the entity name**, because clarification and decline are first-class and the no-response signal must remain observable. | `PROPOSED` (`ADR-003`). |
| Quote | **P02: redefined as a *kind* of `ProviderResponse` (`offer`).** A provider statement of price, scope, and conditions. **Never a booking.** | `PROPOSED`. |
| PriceStatement | **P02.** A monetary statement with mandatory currency, basis, inclusions, exclusions, conditions, and freshness. **No implicit currency, ever.** | `PROPOSED`. |
| ReportedOutcome | **P02.** The customer's self-declared statement of what happened: `proceeding`, `not_proceeding`, or `unknown`, with reporter and time. **Never derived from platform activity and never labelled conversion, booking, payment, or completion.** `unknown` (an explicit answer) is not `Unreported` (silence). | `PROPOSED`. |
| Booking | Accepted commercial commitment between customer and provider. **P02: `Booking` is not `Payment`, and a V1 `ReportedOutcome: proceeding` is neither.** V1 deliberately has no accept transition that creates an obligation. | `FUTURE`; release scope remains `A-004` OPEN. |
| Conversation / Message | In-platform communication context and its user-authored entries. **P02: a `ThreadEvent` is a system marker and is not a `Message`.** | `PROPOSED` capability. |
| Notification | **P02: split into `NotificationIntent` (the decision to inform) and `DeliveryAttempt` (one attempt on one channel).** Delivery failure is attributable to the attempt and is **never** evidence of provider non-response. | `PROPOSED`; distinct from messaging. |
| Disclosure decision | **P02.** The recorded decision resolving whether a contact field may be disclosed to a given recipient, on a given channel, for a given field, in a given request state. **Contact data is never stored on a request, message, or notification.** | `PROPOSED` (`ADR-010`). |
| Organic ranking | Ordering based on non-paid marketplace relevance and quality rules. **P02: organic inputs only.** | `PROPOSED`; distinct from promotion. |
| Sponsored placement / Promotion | Paid visibility targeted to a `Market`, with disclosure and allocation rules. | `PROPOSED`; commercial model unknown. |
| placementBasis | **P02.** The provenance value carried on every result slot and every measurement event; `organic` is its only V1 value. This is the mechanism that keeps organic and paid separable and keeps historical data comparable. **A `featured` boolean is the named anti-pattern.** | `PROPOSED`. |
| Taxonomy | The governed system covering `Category`, the archetype binding, `CategoryAttributeDefinition`, allowed value domains, filterability, and publication requirements. **Provider input can only ever be a proposal.** | `PROPOSED`; legacy quality unknown. |
| ServiceAreaDeclaration | **P02: replaces `Service area`.** The provider's **claim** about where it will serve — provider-asserted and possibly wrong — with archetype-specific variants. Distinct from `BaseLocation` and from the event's service location. | `PROPOSED`. |
| Place / BaseLocation / GeoPoint / Market | **P02.** `Place` is governed geographic identity in a variable-depth hierarchy. `BaseLocation` is an offering's operating origin. `GeoPoint` carries **mandatory precision and provenance**, and one without sufficient precision may not drive distance eligibility. `Market` is a governed (category × place) pair serving search, liquidity measurement, and future sponsored inventory as **one** concept (`Q-021`). | `PROPOSED`. |
| LocationEligibility | **P02.** Three-valued geographic eligibility: `eligible`, `ineligible`, or `undetermined`. **`undetermined` must be surfaced, never coerced** — coercing it up reproduces legacy saturation, coercing it down hides real supply. | `PROPOSED`. |
| RequestIntake | **P02.** Whether an offering is currently willing to receive requests: `accepting`, `paused`, or `unconfirmed`, where a stale `accepting` decays to `unconfirmed`. | `PROPOSED` (`ADR-005`). |
| Availability (date or resource) | **P02: `REJECTED` for V1.** `V1 has no availability model.` `A-006` is `SUPERSEDED`, and "accepting requests" is **not** a date or resource promise (`OR-010`, `G-06`). | `REJECTED` for V1; semantics remain `Q-007`. |
| verificationBasis | **P02.** The explicit basis recorded on every trust-flavoured fact — for example contact-channel-confirmed, self-asserted, imported-unaudited, operator-reviewed. **Never a bare `verified: true`.** | `PROPOSED`; `Q-014` made structural. |
| PublishedSnapshot | **P02.** The versioned, **allowlist-derived**, rebuildable public projection of a profile. A field is public because it is explicitly publishable; unknown fields are **absent, not passed through**. | `PROPOSED` (`ADR-012`). |
| LegacyProviderRecord / OwnershipClaim | **P02: replaces `Claimable profile`**, which named a state rather than an entity. A `LegacyProviderRecord` is Superola asserting a business *may* exist; it is **structurally not a `ProviderProfile`**, so it cannot appear in discovery or receive a request. An `OwnershipClaim` **binds ownership; it does not publish** — it seeds a new profile in `Draft`. | `PROPOSED` (`ADR-009`); legal acceptability remains `A-007`, `Q-025`. |
| SuppressionRecord | **P02.** A durable *do not import, publish, or contact* marker, **checked before record creation**, so a deletion is not undone by a later import pass. | `PROPOSED`; retention basis is `Q-024`. |
| ModerationCase / ModerationDecision | **P02.** A `Report` is a submission, a `ModerationCase` is the operator work item, and a `ModerationDecision` is the auditable finding. **Content state and actor state are two lifecycles.** | `PROPOSED`. |
| EligibilityDecision | **P02.** The single named concept answering whether an offering may appear or receive a request. Its named inputs include a **constant** entitlement input in V1 — which is the monetization seam. It must never gain a paid branch. | `PROPOSED` (`ADR-008`). |
| AuditRecord | **P02.** Who did what, when, on whose authority. Holds **references, never payload copies**; not deletable by the domain-deletion path; needs a stated retention period. Distinct from a domain event and from an analytics event. | `PROPOSED`. |
| Employer-facing artifact | Material intended to be shown directly to the Superola owner. | `CONFIRMED`; provenance `DAVID_DIRECTIVE`: Spanish + English support required. |
