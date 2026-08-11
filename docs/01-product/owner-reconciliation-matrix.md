# Owner Reconciliation Matrix

> Status: internal working instrument. English only. Created by P01.1.
>
> The P01 launch hypothesis it reconciles against remains **Superola V1 — Marketplace Leads (B0 envelope)**, `PROPOSED — OWNER VALIDATION REQUIRED`. Nothing in this file approves it.

## Purpose

Compare future owner answers and owner materials against the P01 baseline in a way that makes confirmation, contradiction, and silence all visible. Without this, a verbal answer or a slide in an owner document can quietly become a requirement without evidence, provenance, or a decision record.

This matrix does not hold owner answers yet. Baseline rows are filled; reconciliation rows are `PENDING` by design. Do not pre-fill an expected owner answer.

## Record model

Each reconciliation topic carries these fields. Baseline fields are populated from the canonical repository state; reconciliation fields are populated only when real owner input arrives.

| Field | Populated | Meaning |
|---|---|---|
| ID | Baseline | Stable `OR-0NN` identifier. Never reused or renumbered. |
| Topic | Baseline | The decision area under reconciliation. |
| Current P01 position | Baseline | What the repository currently proposes or records, stated without softening. |
| Evidence status | Baseline | `CONFIRMED`, `OBSERVED`, `PROPOSED`, `ASSUMPTION`, `FUTURE`, or `REJECTED` per `docs/00-context/glossary.md`. |
| Current provenance | Baseline | `OWNER_INTERVIEW`, `DAVID_DIRECTIVE`, `TECHNICAL_DISCOVERY`, `LIVE_PLATFORM`, `PROJECT_DATA`, `OWNER_MATERIAL`, `EXTERNAL_PRIMARY`, or `EXTERNAL_SECONDARY`. |
| Linked registers | Baseline | Related assumption (`A-0NN`) and question (`Q-0NN`) IDs so registers stay authoritative. |
| Decision owner | Baseline | Who can actually settle it: owner, David, or a named evidence source. |
| Owner response / material | Reconciliation | The answer or artifact received, with source, version, and date. Quote or summarize; never paraphrase into a requirement. |
| Relationship to P01 | Reconciliation | `CONFIRMS`, `EXTENDS`, `CONTRADICTS`, `REPLACES`, `CLARIFIES`, or `STILL OPEN`. |
| Scope impact | Reconciliation | What enters, leaves, or moves in the proposed V1 boundary. |
| Architecture impact | Reconciliation | Which structural concern is affected. Named as a concern, not as a design. P02 decides the design. |
| UX impact | Reconciliation | Which journey, state, or surface is affected. P04 decides the design. |
| Cost / timeline impact | Reconciliation | Direction and driver only. No numbers until an authorized costing phase produces them. |
| Required follow-up | Reconciliation | The specific next evidence action and its owner. |
| Final disposition | Reconciliation | `ACCEPTED`, `ACCEPTED WITH CONDITIONS`, `DEFERRED`, `REJECTED`, or `OPEN`, plus the date and who decided. |

### Relationship definitions

Precision here is the entire point of the instrument.

| Value | Use when |
|---|---|
| `CONFIRMS` | Owner input matches the current P01 position. Evidence status may strengthen; scope does not change. |
| `EXTENDS` | Owner input adds something P01 did not consider. Scope grows. Requires an explicit staging decision. |
| `CONTRADICTS` | Owner input conflicts with the current P01 position. P01 must be corrected or the contradiction escalated. Never silently averaged. |
| `REPLACES` | Owner input supersedes the P01 position entirely. The old position becomes `REJECTED` with rationale preserved. |
| `CLARIFIES` | Owner input resolves ambiguity without changing direction. Usually converts an `ASSUMPTION` into something narrower. |
| `STILL OPEN` | The topic was raised and not resolved. This is a valid, recordable outcome. Recording it is mandatory. |

An owner answer that is not written down as one of these six values has not been reconciled.

## Baseline — seeded reconciliation topics

Seeded with the highest-value topics only. Add rows only when a new topic materially changes scope, cost, timeline, or architecture.

| ID | Topic | Current P01 position | Evidence status | Current provenance | Linked registers | Decision owner |
|---|---|---|---|---|---|---|
| OR-001 | Launch objective | Customer obtains a useful provider response and a confident next-step decision; provider receives relevant, attributable opportunity; operator observes a repeatable measurable exchange. | `PROPOSED` | `TECHNICAL_DISCOVERY` | `Q-001` | Owner |
| OR-002 | Launch categories | Start with the smallest coherent category archetype, not the full event-services breadth. Performer, venue, professional/decorator, catering/cake, and transportation vary materially; no universal schema. | `PROPOSED` | `TECHNICAL_DISCOVERY` | `Q-004`, `A-013` | Owner + provider research |
| OR-003 | Launch geography | One owner-approved geography for the costing and learning baseline. US/Mexico priority is an unvalidated assumption derived from historical reach. | `ASSUMPTION` | `TECHNICAL_DISCOVERY` over `OWNER_INTERVIEW` reach | `Q-003`, `A-002`, `A-013` | Owner / business |
| OR-004 | Launch language(s) | One production locale at launch; additional locales are an explicit gated expansion, not a default. | `PROPOSED` | `TECHNICAL_DISCOVERY` | `Q-003` | Owner |
| OR-005 | V1 stopping boundary | V1 ends at a customer-reported `proceed` / `not proceeding` / `unknown` outcome. Booking, payment, payout, service completion, and review are later states. | `PROPOSED — OWNER VALIDATION REQUIRED` | `TECHNICAL_DISCOVERY` | `Q-002`, `A-004`, `A-008` | Owner |
| OR-006 | Direct RFQ versus multi-provider matching | Each V1 RFQ has exactly one customer-selected recipient with an independent lifecycle. No recipient sets, routing, fan-out, shared response windows, auto-closure, or reroute. General matching is a distinct future hypothesis. | `PROPOSED` | `TECHNICAL_DISCOVERY` | `Q-013`, `Q-017`, `A-011` | Owner + customer/provider research |
| OR-007 | Customer verification | Verify customer contact/account control just in time, before provider delivery. Pre-submit and guest-then-verify alternatives remain uncompared. | `PROPOSED` | `TECHNICAL_DISCOVERY` | `Q-018`, `A-014` | Owner/product + privacy/security |
| OR-008 | Provider onboarding and profile claim | Prefer curated fresh onboarding. Claim/dispute capability is conditional on an audited, lawful imported cohort; it is not assumed. | `PROPOSED` | `TECHNICAL_DISCOVERY` | `Q-006`, `A-007` | Owner + legal/privacy + audit |
| OR-009 | Legacy provider expectations | Approximately 43,000 registrations are owner-reported and unaudited. Legacy value is optional to the launch slice, never its precondition. Imported records cannot receive RFQs before lawful-use, reachability, control, and publication gates pass. | `CONFIRMED` (owner-reported, unaudited) for the count; `ASSUMPTION` for usable value | `OWNER_INTERVIEW` + `TECHNICAL_DISCOVERY` | `Q-005`, `A-001`, `A-007`, `A-009` | Owner + authorized data audit + legal/privacy |
| OR-010 | Availability semantics | "Accepting RFQs" is not a date or resource availability promise. A universal availability model across categories is superseded and rejected. | `PROPOSED`; `A-006` `SUPERSEDED` | `TECHNICAL_DISCOVERY` | `Q-007`, `A-006` | Provider/customer research + owner |
| OR-011 | Contact disclosure | Interaction stays in-platform as asynchronous text attached to the RFQ; the owner separately described email/SMS relay without exposing customer contact details. The combined policy is not owner-validated. | `PROPOSED` (in-platform preference is `DAVID_DIRECTIVE`; relay concept is owner-reported) | `DAVID_DIRECTIVE` + `OWNER_INTERVIEW` | `Q-008`, `A-010` | Owner policy + research + compliance |
| OR-012 | Subscription timing and value | Free onboarding and value measurement at V1.0; a simple subscription only at a V1.x gate after providers can observe attributable value. No prices set; no entitlement automation built. | `PROPOSED` staging over a `CONFIRMED` (owner hypothesis, unvalidated) business idea | `TECHNICAL_DISCOVERY` over `OWNER_INTERVIEW` | `Q-009`, `A-003`, `A-012` | Owner + pricing research |
| OR-013 | Sponsored placement timing | Deferred until credible organic liquidity, separate organic relevance measurement, sellable scarcity, approved disclosure/rotation/fairness policy, separable attribution, and operating capacity exist. | `PROPOSED` deferral over a `CONFIRMED` (owner concept, unvalidated) idea | `TECHNICAL_DISCOVERY` over `OWNER_INTERVIEW` | `Q-010` | Owner / business policy |
| OR-014 | Booking and payment expectations | Booking acceptance, customer payment/deposit, provider payout, refunds, disputes, guarantees, and transaction-derived reviews are explicitly deferred as a coupled operating cluster. | `PROPOSED` deferral | `TECHNICAL_DISCOVERY` | `Q-002`, `A-004` | Owner |
| OR-015 | Existing feature/monetization document status | Not received. No claim from it may be inferred. Registered as `SRC-003`. | Pending source | `OWNER_MATERIAL` (pending) | `Q-011` | Owner |
| OR-016 | Existing UI/UX material status | Not received. No design claim may be inferred. Registered as `SRC-004`. Reconciliation precedes any P04 authorization. | Pending source | `OWNER_MATERIAL` (pending) | `Q-011` | Owner |
| OR-017 | Six-month expectation | Approximately six months was discussed informally in relation to a serious production V1; the repository treats it as a non-contractual planning hypothesis subject to scope, evidence, dependencies, and capacity. | `CONFIRMED (informal)` for the discussion; `DAVID_DIRECTIVE` for the non-contractual treatment | `OWNER_INTERVIEW` + `DAVID_DIRECTIVE` | `A-008`, `Q-012` | Owner + David |

## Reconciliation ledger

Fill only from received owner answers or registered owner materials. One row per topic per intake event; a topic answered twice gets two rows so the change is visible.

| ID | Owner response / material (source, version, date) | Relationship | Scope impact | Architecture impact | UX impact | Cost / timeline impact | Required follow-up | Final disposition | Decided by / date |
|---|---|---|---|---|---|---|---|---|---|
| OR-001 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C1. | OPEN | — |
| OR-002 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C4; reconcile with any category list in `SRC-003`. | OPEN | — |
| OR-003 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C4; if undecided, record at most two bounded scenarios. | OPEN | — |
| OR-004 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C4; confirm whether one locale is acceptable at launch. | OPEN | — |
| OR-005 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C2. | OPEN | — |
| OR-006 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C3; confirm the one-recipient semantic explicitly, not by silence. | OPEN | — |
| OR-007 | PENDING | STILL OPEN | — | — | — | — | Confirm the identity boundary only: must a request reach a provider from an identified, reachable customer? Exact verification timing stays a P04 comparison. | OPEN | — |
| OR-008 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C5; separate fresh onboarding from profile claim in the answer. | OPEN | — |
| OR-009 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C5; capture permission and belief about usable supply, not counts. | OPEN | — |
| OR-010 | PENDING | STILL OPEN | — | — | — | — | Ask IF TIME T2; promote to CORE if C4 names categories with hard date scarcity. Must be resolved in follow-up if the meeting runs short. | OPEN | — |
| OR-011 | PENDING | STILL OPEN | — | — | — | — | Ask IF TIME T1. | OPEN | — |
| OR-012 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C6. | OPEN | — |
| OR-013 | PENDING | STILL OPEN | — | — | — | — | Ask IF TIME T3. | OPEN | — |
| OR-014 | PENDING | STILL OPEN | — | — | — | — | Covered by CORE C2; record a separate row if the owner expects booking or payment in V1. | OPEN | — |
| OR-015 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C7; request the document and register version/date. | OPEN | — |
| OR-016 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C7; request UI/UX files or links and register version/date. | OPEN | — |
| OR-017 | PENDING | STILL OPEN | — | — | — | — | Ask CORE C7; determine commitment versus planning hypothesis. | OPEN | — |

## Intake process for owner materials

Applies to any owner document, design file, link, dataset, or written answer. Follow it in order; do not skip registration because the material "looks minor".

1. **Receive and preserve the original outside this repository.** Keep it in the owner-authorized location. Do not commit proprietary owner material, exports, or credentials here.
2. **Register the source.** Add or update the row in `docs/00-context/source-register.md`: source owner, class, version, date supplied, access date, availability, and evidence treatment. `SRC-003`, `SRC-004`, `SRC-005`, `SRC-006`, and `SRC-007` already exist as pending placeholders — update them rather than creating duplicates.
3. **Extract only the relevant claims.** Prefer metadata plus authorized sanitized extraction. Sanitize PII, contact data, pricing that is commercially sensitive, and anything with third-party rights. If sanitized extraction would distort meaning, record that limitation instead of copying the original.
4. **Attach provenance `OWNER_MATERIAL`** to extracted claims, or `OWNER_INTERVIEW` to spoken answers. Provenance is recorded separately from evidence status; neither substitutes for the other.
5. **Compare each claim against the baseline table** and write a reconciliation ledger row with an explicit relationship value. `STILL OPEN` is a valid result and must be recorded.
6. **Escalate contradictions rather than averaging them.** A `CONTRADICTS` or `REPLACES` row requires an explicit decision by the named decision owner before any canonical document changes.
7. **Update the registers.** `docs/00-context/assumptions.md` status changes, `docs/01-product/open-questions.md` resolutions, and risk updates come from ledger rows, not from memory of the conversation.
8. **Update the P01 baseline only when justified.** Amend `docs/05-roadmap/mvp-scope.md` and related product documents only for `EXTENDS`, `CONTRADICTS`, or `REPLACES` rows with a recorded decision. Preserve superseded positions with rationale; do not delete them.
9. **Record material decisions.** A decision that constrains architecture requires an ADR under `docs/02-architecture/adr/`. A decision that only changes product scope is recorded in the ledger and the plan.
10. **Re-evaluate the release gate below** and state plainly whether P02/P04 can begin or must wait. That statement goes to David, who authorizes phase start.

## Release gate — when can P02 and P04 begin?

This is a readiness test, not an authorization. David authorizes phase start explicitly.

The gate does not require every uncertainty to be resolved. It requires that the remaining uncertainty cannot invalidate the core product model that P02 and P04 would design against.

**A BLOCKING item is satisfied by one of two things:** an owner decision, or an explicitly documented bounded scenario set that David accepts as the design envelope. A bounded scenario set means at most two named alternatives with their consequences recorded — not an open range.

### BLOCKING FOR P02/P04

| Gate | Satisfied when | Topics |
|---|---|---|
| G-01 Primary outcome | The owner states what customer and provider must each get for the first release to count as successful. | OR-001 |
| G-02 V1 stopping boundary | The owner confirms or moves where V1 ends, including whether booking or payment is expected in the first release. | OR-005, OR-014 |
| G-03 Request boundary | The owner explicitly accepts, rejects, or modifies one-recipient direct RFQ versus multi-provider matching. Silence is not acceptance. | OR-006 |
| G-04 Category archetypes | A launch archetype set is named, even if the full category list is not final. Archetypes drive profile, service-area, and quote variability. | OR-002 |
| G-05 Geography and language | A launch geography and locale are decided, or a bounded scenario set is documented and accepted. | OR-003, OR-004 |
| G-06 Availability claim | It is settled what "available" or "accepting requests" promises a customer: an indication, an invitation to ask, or a reservable commitment. This is an IF TIME question in the meeting; if it is not reached there, it must be resolved in follow-up before P04 designs profile and request surfaces. | OR-010 |
| G-07 Identity boundary | Provider onboarding and profile-claim expectations are stated, and it is settled whether a request may only reach a provider from an identified, reachable customer. The exact verification timing is not part of this gate. | OR-007, OR-008 |
| G-08 Monetization direction | The staging intent is settled: what stays free, what may become paid, and that no price is being committed now. | OR-012 |
| G-09 Legacy expectation | The owner's expectation for legacy providers is stated: fresh start, curated import pilot, or broad migration — plus whether permission to contact or migrate exists. | OR-009 |
| G-10 Material status | It is known which owner documents and designs are commitments versus explorations, and whether the six-month discussion is a fixed external commitment or a planning hypothesis. | OR-015, OR-016, OR-017 |

If G-01, G-02, G-03, or G-09 is unresolved, P02 and P04 would be designing against an unknown product. Do not start.

If only G-05 is unresolved, P02 and P04 may proceed against the documented scenario set, provided every artifact states which scenario it assumes.

### CAN REMAIN OPEN

These do not invalidate the core model and must not hold the gate:

- Exact customer verification timing — pre-submit, just-in-time, or guest-then-verify. `docs/05-roadmap/mvp-scope.md` already charters P04 to compare these; only the identity boundary in G-07 is blocking.
- Exact subscription price, plan packaging, trial duration, and billing mechanics.
- Exact sponsored inventory mechanics: position count, rotation rules, targeting granularity, and pricing.
- Future booking and payment design, once it is settled that they are outside V1.
- Native mobile implementation, provided the channel priority for launch is known.
- External-agent protocols and adapters, and the optional natural-language intent experiment.
- SEO location-page program scope, extra locales, and public distribution beyond the approved minimum.
- Final taxonomy depth beyond the launch archetypes.
- Support staffing levels and operator tooling depth beyond exception-only manual paths.
- Analytics tooling choices and any technology selection, which belong to P03 and are out of scope for this gate.

## Owner material checklist

Request in this order. Priority reflects how much each item unblocks, not how easy it is to obtain.

| # | Material | Why it matters | Register as | Handling |
|---|---|---|---|---|
| 1 | Latest feature and monetization document | Highest chance of confirming or contradicting the proposed V1 boundary and monetization staging. | `SRC-003` | Register version and date. Distinguish commitments from ideas explicitly, in writing. |
| 2 | Existing UI/UX files or links | Determines whether P04 starts from existing design intent or from open structural work. | `SRC-004` | Register version and date. Links are acceptable; do not copy proprietary design files into this repository. |
| 3 | Authorized access to the current Superola site or admin | Converts implementation and behavior claims from `ASSUMPTION` to `OBSERVED`. Includes the unverified WordPress/MyListing claim in `SRC-009`. | `SRC-002` | Authorized, bounded audit only. Never request credentials through informal channels; use the owner's approved access process. |
| 4 | Analytics, search, and traffic data | Tests whether reported reach corresponds to real demand, and informs geography and category selection. | `SRC-006` | Aggregated exports preferred. Request source system, date range, and metric definitions. |
| 5 | Documented business and pricing assumptions | Separates illustrative figures from validated intent before any economic gate. | `SRC-007` | Register as owner input, not as validated economics. |
| 6 | Legacy schema and data export | Needed for migration viability, taxonomy mapping, and usable-supply yield. | `SRC-005` | **Later, under controls only.** Request after privacy handling, storage location, retention, and deletion are agreed. Never in this repository. Never over an insecure channel. |

Do not request items 3 and 6 casually during the meeting. Ask whether authorized access is possible and agree a controlled process afterwards.
