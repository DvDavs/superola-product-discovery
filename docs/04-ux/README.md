# UX Architecture — P04 Index and Working Assumptions

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`, executed under an explicit `DAVID_DIRECTIVE` authorizing P04.
> **Nothing here is approved.** No owner gate is satisfied by this phase. `G-06` remains UNSATISFIED and P04 proceeds on `WA-01` rather than on an owner answer.
> Scope: structural UX architecture. **Not** visual design. No color, typography, branding, or component library was selected, and none may be inferred from any P04 artifact.

This index is the entry point to P04. It carries the five working assumptions every other P04 document depends on, the artifact map, and the boundary of what P04 decided.

## The five working assumptions

`WA-01`–`WA-04` are stamped `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`; **`WA-05` is not — it is P04's own recommendation, evidence `PROPOSED`, provenance `TECHNICAL_DISCOVERY`**, and stamping it as a David directive would convert a proposal into a decision. Each permits P04 to proceed **without** marking an owner gate satisfied, and each preserves its decision branch intact. Cite these by ID.

| ID | Assumption | Evidence / provenance | Branch preserved |
|---|---|---|---|
| `WA-01` | **Accepting requests is not guaranteed date availability.** A provider may be discoverable and `accepting`. The customer supplies date and time requirements as request context. The provider determines feasibility inside the request interaction and may respond with a `clarification`, a `decline`, or an `offer` — or not respond at all, which is the system-observed `NoResponse` state rather than a provider action. **Date infeasibility is a decline reason, not a response kind**, and **no surface presents a provider as guaranteed available for a specific slot.** | `ASSUMPTION`; provenance `DAVID_DIRECTIVE` | `G-06` / `Q-007` remain formally unresolved. Calendar synchronization and bookable availability remain future branches (`DB-10`). |
| `WA-02` | **One deliberate recipient.** The customer selects a provider and exactly one `ServiceRequest` reaches that provider. Automatic broadcast and fan-out are not the V1 flow. | `ASSUMPTION`; provenance `DAVID_DIRECTIVE` | Consent-based sending to similar providers remains a future branch (`DB-01`, `Q-013`, `Q-017`). |
| `WA-03` | **V1 Phase 1 ends at marketplace outcome.** No booking, deposit, payment, payout, refund, or transaction-derived review. P04 shows where those would attach; it does not design their workflows. | `ASSUMPTION`; provenance `DAVID_DIRECTIVE` | Transaction Extension remains `FUTURE` and separately owner-approved (`DB-02`, `G-02`). |
| `WA-04` | **Web-first with required responsive behavior.** Native mobile is future. No native-mobile-specific interaction pattern is introduced. | `ASSUMPTION`; provenance `DAVID_DIRECTIVE` | `DB-06`. |
| `WA-05` | **Guest-then-verify customer identity.** Public discovery, profile viewing, and request composition are anonymous. Account establishment and contact-channel verification occur at submit, before provider delivery. | `PROPOSED`; provenance `TECHNICAL_DISCOVERY` | Pre-submit account and verify-at-profile remain the compared alternatives (`Q-018`, `DB-12`, `OR-007`). `mvp-scope.md` charters this comparison to P04 by name. |

**`WA-05` is P04's own recommendation, not a David directive.** It is the one working assumption this phase produced rather than received, and it is the one most likely to change under owner or usability evidence.

## Artifact map

| Document | What it decides or records |
|---|---|
| `customer-journey.md` | The canonical customer journey, its exception journeys, and the customer/provider visibility asymmetry. |
| `request-intake.md` | The intake model comparison and selection, the GigSalad comparative input, `RequestDraft` UX, event versus service, the universal/category classification, per-archetype question sets, and the AI-assisted intake disposition. |
| `discovery-and-results.md` | Browsing, governed location, filters, free-text secondary matching, zero-result and degraded-input behavior, and the results information hierarchy. |
| `identity-and-verification.md` | The five verification timings compared and `WA-05`. |
| `provider-profile.md` | The V1 public profile architecture and its privacy rules. |
| `provider-onboarding.md` | Progressive onboarding and the account-versus-publication separation. |
| `provider-workspace.md` | Provider surfaces, `RequestIntake` control, and the dual-role UX. |
| `response-conversation-outcome.md` | RFQ send, provider response kinds, conversation, notification bodies, and outcome capture. |
| `operator-surfaces.md` | The minimum operator queues and the anti-inflation test. |
| `information-architecture.md` | Public and authenticated conceptual hierarchy. |
| `surface-inventory.md` | Every surface, its states, and its interaction complexity. **The evidence base for `ADR-020`.** |
| `rendering-evidence.md` | The per-surface rendering evidence and the `ADR-020` recommendation. |
| `map-decision.md` | The `ADR-019` Level 3 decision. |
| `accessibility-and-responsive.md` | Baseline interaction architecture and phone-width evaluation. |
| `wireframes.md` | Low-fidelity structural wireframes. |
| `design-canon.md` | The phase's binding internal brief — vocabulary, constraints, and the decisions every other P04 document was written against. Persisted because the other documents cite its section numbers. |
| `ux-complexity-review.md` | The adversarial cost and complexity review of P04's own proposals. |
| `p05-p06-handoff.md` | Inputs owed to P05 and P06. |

Owner-facing artifacts live in `presentation/` and are bilingual. Diagrams live under `diagrams/`.

## What P04 decided

| Decision | Outcome |
|---|---|
| Request intake model | **Progressive**, governed by `CategoryArchetype`. Traditional universal intake rejected as the primary model; assisted intake is `FUTURE`. |
| `RequestDraft` | Begins local and anonymous at the first answer; promoted to a server draft when an `Account` exists; account creation is never required to browse, search, view a profile, or begin a draft. |
| Customer verification timing | **Guest-then-verify** (`WA-05`). |
| Web rendering (`ADR-020`) | **Option A** — server-rendered plus progressive enhancement, with three named client islands. No V1 surface requires client state that survives navigation. |
| Rendered map (`ADR-019` Level 3) | **No rendered map in V1.** Deferred with three named reopening triggers. The privacy invariant is retained but is explicitly not the reason. |
| AI-assisted intake | **Not V1.** Worth testing later under four named conditions. |
| Facet counts (`Q-035`) | **Recommendation: no live facet counts in V1.** `Q-035`'s owner is P04 plus David; P04 recommends and David decides. |
| Dual-role UX (`ADR-004`) | Two persistent contexts in one account. No mode toggle, no account type, no second account. |

## What P04 did not decide

`G-06` / `Q-007` — what "available" promises a customer. `Q-008`, `A-010`, `OR-011` — contact-disclosure policy. `Q-026` disclosure limb. `Q-031` — whether a durable `DemandWatch` exists. The `NoResponse` window length. The `RequestIntake` decay window length. `Q-020` beyond a recommendation. Sponsored mechanics. Subscription pricing or packaging. Legacy migration design. Payment and booking design. Any vendor. Any color, font, or brand.

`SRC-004` — the owner's existing UI/UX materials — is **NOT RECEIVED**. No design claim in P04 derives from prior owner UX work, and `OR-016` still requires reconciliation. Any P04 artifact may be confirmed, extended, contradicted, or replaced by that reconciliation.

---

*Record dates — phase executed 2026-08-12. The competitor request-flow capture used as comparative input in `request-intake.md` was supplied by David and was not independently re-verified against first-party sources during this phase.*
