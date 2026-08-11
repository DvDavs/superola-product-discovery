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

| Term | Working definition | Status |
|---|---|---|
| Customer | Person or organization planning an event and seeking services. | `CONFIRMED`; provenance `OWNER_INTERVIEW`; detailed roles pending. |
| Provider / Vendor | Business or service professional offering event-related services. | `CONFIRMED`; provenance `OWNER_INTERVIEW`; account/business structure pending. |
| Business | Provider organization that may own listings and include staff or members. | `PROPOSED` concept. |
| Listing / Provider profile | Marketplace representation of a provider/service including relevant descriptive, media, geographic, and availability information. | `PROPOSED` terminology; exact model pending. |
| Event | Customer occasion with attributes such as type, date, location, guest count, and budget. | `PROPOSED` entity. |
| Service request / RFQ | Customer request for a provider/service in an event context. | `PROPOSED` entity. |
| Quote | Provider response describing price, scope, conditions, or availability. | `PROPOSED` entity. |
| Booking | Accepted commercial commitment between customer and provider. | `PROPOSED`; release scope remains an open `ASSUMPTION`. |
| Conversation / Message | In-platform communication context and its individual user-authored entries. | `PROPOSED` capability. |
| Notification | Delivery of a platform-event alert through in-app, email, push, SMS, or another channel. | `PROPOSED`; distinct from messaging. |
| Organic ranking | Ordering based on non-paid marketplace relevance/quality rules. | `PROPOSED`; must remain distinct from promotion. |
| Sponsored placement / Promotion | Paid visibility targeted to a market such as category + geography, with disclosure and allocation rules. | `PROPOSED`; final commercial model unknown. |
| Taxonomy | Governed system of service categories and related concepts used for discovery, SEO, migration, and reporting. | `PROPOSED`; legacy quality unknown. |
| Service area | Geography in which a provider is willing/able to serve customers, potentially different from its base location. | `PROPOSED`; representation pending. |
| Claimable profile | Preliminary/unclaimed provider record that a verified provider may assume control of. | `PROPOSED`; usefulness, legal acceptability, and technical feasibility remain `ASSUMPTION`s. |
| Employer-facing artifact | Material intended to be shown directly to the Superola owner. | `CONFIRMED`; provenance `DAVID_DIRECTIVE`: Spanish + English support required. |
