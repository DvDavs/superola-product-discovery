# Presentation Source Register

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. P07 content claims have been revalidated; rendered-slide bilingual and visual QA remain pending.

This is an internal production-support register and remains English only. The owner-facing artifacts it references are intentionally bilingual.

## Discovery Preview v0.1

| Slide/artifact | Claim | Canonical source | External source/access date | Evidence label | Provenance | Review status |
|---|---|---|---|---|---|---|
| Preview 1 | Superola is a two-sided event-services marketplace, not only a directory; owner reported ~43k registrations; quality/activity remain unaudited; location/taxonomy issues exist. | `docs/00-context/product-context.md`; `docs/00-context/interview-evidence.md`; `A-001`; `R-001`; `R-010` | `SRC-001` repository representation, 2026-08-11 | Marketplace framing `CONFIRMED`; registration/quality claims `CONFIRMED` owner-reported, unaudited | `DAVID_DIRECTIVE` (framing), `OWNER_INTERVIEW` (reported facts) | P01.1 REVIEWED |
| Preview 2 | Customer, provider, and operator outcomes. | `docs/01-product/actors.md` | None | `PROPOSED` | `TECHNICAL_DISCOVERY` | P01 REVIEWED |
| Preview 3 | Direct-RFQ marketplace loop and V1 endpoint. | `docs/01-product/product-vision.md`; `docs/01-product/user-journeys.md` | None | `PROPOSED — OWNER VALIDATION REQUIRED` | `TECHNICAL_DISCOVERY` | P01 REVIEWED |
| Preview 4 | Mature marketplace patterns and coupled transaction scope. | `docs/01-product/competitive-benchmark.md` | GigSalad `GS-01`–`GS-09`; The Bash `TB-01`–`TB-09`, accessed 2026-08-11 | Competitor behavior `OBSERVED`; Superola synthesis `PROPOSED` | `LIVE_PLATFORM`, `EXTERNAL_PRIMARY`, `TECHNICAL_DISCOVERY` | EXTERNALLY REVIEWED + P01 REVIEWED |
| Preview 5 | Marketplace Leads inclusion recommendation and free-onboarding stage. | `docs/05-roadmap/mvp-scope.md`; `docs/01-product/monetization.md` | None | `PROPOSED — OWNER VALIDATION REQUIRED` | `TECHNICAL_DISCOVERY` | P01 REVIEWED |
| Preview 6 | Booking/payment, matching, realtime availability, sponsorship, mobile, and agent actions are deferred/gated. | `docs/05-roadmap/mvp-scope.md`; `docs/01-product/feature-inventory.md` | None | `PROPOSED` / `FUTURE` | `TECHNICAL_DISCOVERY` | P01 REVIEWED |
| Preview 7 | Human-first machine-legible foundation; optional deterministic AI intent experiment; future protocol adapters. | `docs/07-research/ai-discoverability.md` | Official Google/OpenAI/protocol sources registered there, accessed 2026-08-11 | `PROPOSED` / `FUTURE` | `EXTERNAL_PRIMARY`, `TECHNICAL_DISCOVERY` | EXTERNALLY REVIEWED + P01 REVIEWED |
| Preview 8 | Seven owner questions that materially change product, MVP, business, architecture, or timeline. | `docs/01-product/open-questions.md` | None | Open questions | `TECHNICAL_DISCOVERY` | P01 REVIEWED |
| Preview 9 | Owner answers are reconciled item by item; pending materials are requested; architecture and UX follow; scope, timeline, cost, and technology remain later evidence-gated work. | `docs/01-product/owner-reconciliation-matrix.md`; `plans/P01.1-owner-reconciliation-prep.md` | None | Process statement; no product claim | `TECHNICAL_DISCOVERY` | P01.1 REVIEWED |

Numbers and competitor observations remain qualified on the slide. The preview contains no final technology, price, target, or delivery commitment.

## Owner Meeting Guide v0.1

> Status: `PROPOSED — WORKING DRAFT`. `presentation/owner-meeting-guide-v0.1.md` is bilingual because it is spoken directly to the owner; this register remains English only.

| Section | Claim or content | Canonical source | Evidence label | Provenance | Review status |
|---|---|---|---|---|---|
| Meeting flow | Marketplace loop, proposed V1 framing, and deferral rationale restated for live delivery. | `presentation/discovery-preview-v0.1.md`; `docs/05-roadmap/mvp-scope.md` | `PROPOSED — OWNER VALIDATION REQUIRED` | `TECHNICAL_DISCOVERY` | P01.1 REVIEWED |
| CORE C1–C7 | Seven owner decisions that materially change scope, cost, timeline, or architecture. | `docs/01-product/open-questions.md`; `docs/01-product/owner-reconciliation-matrix.md` (`OR-001`–`OR-017`) | Open questions | `TECHNICAL_DISCOVERY` | P01.1 REVIEWED |
| IF TIME T1–T5 | Secondary owner topics: contact disclosure, availability promise, sponsorship readiness, trust claims, operating capacity. | `docs/01-product/open-questions.md`; `docs/05-roadmap/mvp-scope.md` | Open questions | `TECHNICAL_DISCOVERY` | P01.1 REVIEWED |
| Materials request | Feature/monetization document, UI/UX materials, and authorized site access requested; legacy data staged to a later controlled request. | `docs/00-context/source-register.md` (`SRC-002`–`SRC-007`); reconciliation matrix checklist | Pending sources | `OWNER_MATERIAL` (pending) | P01.1 REVIEWED |
| Capture template | Answers require source, date, speaker, and an explicit relationship value before any canonical document changes. | `docs/01-product/owner-reconciliation-matrix.md` | Process rule | `DAVID_DIRECTIVE` workflow discipline | P01.1 REVIEWED |

The guide states in both languages that the V1 slice is a proposal. It contains no technology, price, target, or delivery commitment, and it does not present any `PROPOSED` item as approved.

## Architecture Preview v0.1

> Status: `PROPOSED — OWNER VALIDATION PENDING`. `presentation/architecture-preview-v0.1.md` is bilingual because it is shown directly to the owner; this register remains English only. Added by P02.

| Section | Claim or content | Canonical source | Evidence label | Provenance | Review status |
|---|---|---|---|---|---|
| Preview 1 — the shape | Four capabilities inside one system; one source of truth; future extensions have a defined attachment point but are not built. | `docs/02-architecture/domain-map.md`; `system-architecture.md`; `ADR-001`; `ADR-006` | `PROPOSED` | `TECHNICAL_DISCOVERY` | P02 REVIEWED |
| Preview 2 — five trust decisions | Business is distinct from public profile; one request to one chosen provider; never claim what cannot be proven; organic and paid never mix; customer contact is not stored inside the request. | `ADR-002`, `ADR-003`, `ADR-005`, `ADR-006`, `ADR-008`, `ADR-010`; `domain-model.md` §2 | `PROPOSED` | `TECHNICAL_DISCOVERY` | P02 REVIEWED |
| Preview 3 — current site's data | Imported records enter a separate space, are not discoverable, cannot receive requests, and are not published; claim grants ownership rather than publishing; deletion is recorded so a later import cannot re-create the record. **Does not decide how many records migrate.** | `ADR-009`; `security-privacy-architecture.md` §7; `docs/06-migration/legacy-data-strategy.md` (NOT STARTED) | `PROPOSED`; migration scope remains `A-001`, `A-007` OPEN | `TECHNICAL_DISCOVERY` | P02 REVIEWED |
| Preview 4 — "what changes if…" | Reversal impact for six owner alternatives, stated as what does **not** change alongside what does. | `docs/02-architecture/decision-branches.md` (`DB-01`–`DB-14`) | `PROPOSED` | `TECHNICAL_DISCOVERY` over `DAVID_DIRECTIVE` envelope (`SRC-013`) | P02 REVIEWED |
| Preview 5 — what is needed from the owner | Two questions the technical team cannot answer (`G-02` stopping boundary, `G-06` availability meaning), plus one needing the owner and counsel (`G-09` legacy permission). | `docs/01-product/owner-reconciliation-matrix.md` gates; `Q-007`, `Q-025`, `Q-029` | Open questions | `TECHNICAL_DISCOVERY` | P02 REVIEWED |

The preview names **no** framework, database, cloud, search product, payment vendor, price, target, or delivery date. It states in both languages that the design was built on working assumptions rather than on owner answers, and it does not present any `PROPOSED` item as approved.

## Technology Preview v0.1

> Status: `PROPOSED — DAVID APPROVAL REQUIRED`. `presentation/technology-preview-v0.1.md` is bilingual because it is shown directly to the owner; this register remains English only. Added by P03.
>
> **This is the first owner-facing artifact in the repository that contains cost figures.** Every figure is a **modelled band over stated volume assumptions, not a quote and not a budget**, and the preview says so in both languages in the same section as the table.

| Section | Claim or content | Canonical source | Evidence label | Provenance | Review status |
|---|---|---|---|---|---|
| Preview 1 — the number that matters | Human cost is 88–96% of total cost of ownership at every modelled scenario; technology is $75.98/month at Pilot and $560.79/month at Growth. | `docs/03-technology/cost-model.md` §1, §5 | `PROPOSED`; **volumes and hourly rates are stated ASSUMPTIONS, not evidence** | `TECHNICAL_DISCOVERY` | P03 REVIEWED |
| Preview 2 — the technical shape | One system with separated parts; one database with point-in-time recovery; server-built public pages; media on zero-egress storage; audio and video linked rather than hosted. | `technology-evaluation.md` §3; `ADR-013`, `ADR-014`, `ADR-016`, `ADR-018` | `PROPOSED — DAVID APPROVAL REQUIRED` | `TECHNICAL_DISCOVERY` | P03 REVIEWED |
| Preview 3 — cost bands | Three scenarios with technology, people and total. **Explicitly qualified in both languages as working assumptions, with the unaudited legacy count named as not used for sizing.** Excludes payments, migration, marketing and legal. | `cost-model.md` §1–§5, §10 | `PROPOSED`; `A-001`/`R-001` qualifier carried onto the artifact | `TECHNICAL_DISCOVERY` | P03 REVIEWED |
| Preview 4 — what makes this expensive | Operator labour first; anonymous-visitor bytes second; a second operating language third; legacy migration fourth; payments fifth. | `cost-model.md` §5, §7, §8; `R-033`, `R-043`; `internationalization-architecture.md` §4 | `PROPOSED` | `TECHNICAL_DISCOVERY` | P03 REVIEWED |
| Preview 5 — what we deliberately did not add | No separate search engine, no map, no realtime messaging infrastructure, no AI in search, no paid monitoring. Each with a stated activation condition. | `technology-evaluation.md` §7; `ai-evaluation.md` §10; `ADR-019` | `PROPOSED` / `REJECTED` with triggers | `TECHNICAL_DISCOVERY` | P03 REVIEWED |
| Preview 6 — how it scales | Three stages, each with a measurable trigger rather than a date. The whole marketplace peaks under ten requests per second at Growth. | `technology-evaluation.md` §1.1, §6; `infrastructure-options.md` §7 | `PROPOSED` | `TECHNICAL_DISCOVERY` | P03 REVIEWED |
| Preview 7 — what is needed from the owner | Three decisions that materially change cost: the V1 stopping boundary (`G-02`), one or two operating languages (`G-05`), and legal permission for legacy records (`G-09`). | `docs/01-product/owner-reconciliation-matrix.md` gates; `A-004`, `A-016`, `A-007` | Open questions | `TECHNICAL_DISCOVERY` | P03 REVIEWED |

**The preview names no vendor and no product.** It presents only the *shape* of the recommendation and the cost consequences, because vendor selection is David's decision to approve and is not an owner-facing question. It does not present any `PROPOSED` item as approved, and it states in both languages that no technology is bought or contracted.

### P03.1 revisions to this preview

| Change | Why |
|---|---|
| **USD stated on every monetary figure** | `AGENTS.md` currency policy. A bare `$` is ambiguous to a reader in Mexico. **No MXN conversion was added** — exchange rates are time-sensitive and change no comparison. |
| **The combined "Total" column was removed.** Technology cash is Preview 1 and §3; the human figure moved to §3.1 and is labelled an **illustrative sensitivity** that says in both languages *"this table is NOT added to the one above"* | Presenting ~USD $14,859/month as a Superola operating cost would present two assumed hourly rates and unmeasured workloads as a bill. `cost-model.md` §1 now carries the same split. |
| **Rendering marked as a starting point, not a decision** | `ADR-020`. P04 owns it. |
| **Authentication disclosed as on hold, with the verification result stated honestly** | `ADR-017` moved to `HOLD` after P03.1 verification falsified its deciding claim for two of four vendors. **Disclosing a finding that went against our own recommendation is the point.** |
| **The map bullet rewritten** | The privacy invariant is retained absolutely; the product conclusion moved to P04. |
| **AI bullet extended with the bounded assisted-intake experiment** | `ai-evaluation.md` §8.1. The search-path recommendation is unchanged; the wording no longer implies AI has no product role. |

## Cost Explainer v0.1

> Status: `PROPOSED — DAVID APPROVAL REQUIRED`. `presentation/cost-explainer-v0.1.md` is bilingual because it is shown directly to the owner; this register remains English only. Added by P03.1.
>
> **It contains no arithmetic of its own.** Every figure is reproduced from `docs/03-technology/cost-model.md` §1.1, §3, §4 and §7, which is the single arithmetic source of truth. **Prices verified 2026-08-11; authentication vendor facts 2026-08-12.**

| Section | Claim or content | Canonical source | Evidence label | Provenance | Review status |
|---|---|---|---|---|---|
| 1 — what the technology costs | USD $76 / $193 / $561 per month; volumes are assumptions; the unaudited legacy count sized nothing. | `cost-model.md` §1.1, §2.2 | `PROPOSED`; **volumes are stated ASSUMPTIONS** | `TECHNICAL_DISCOVERY` | P03.1 REVIEWED |
| 2 — where the money goes | Per-category anatomy of the Pilot and Growth bills, reconciling exactly to the totals in section 1. | `cost-model.md` §3, §4; `cost-alternatives.md` §1 | `PROPOSED` | `TECHNICAL_DISCOVERY` | P03.1 REVIEWED |
| 3 — why not the cheapest server | ~USD $8/hour break-even between managed and self-managed. | `cost-model.md` §6; `ADR-016`; `cost-alternatives.md` §2 | `PROPOSED`; **hourly rate is an ASSUMPTION**; the break-even form is rate-independent | `TECHNICAL_DISCOVERY` | P03.1 REVIEWED |
| 4 — which decisions save the most | Zero-egress media, the governed `Place` model, A/V link-out, in-database search, no AI in the critical path. | `cost-alternatives.md` §4–§9; `ADR-018`, `ADR-019`, `ADR-014`; `ai-evaluation.md` §6 | `PROPOSED` | `TECHNICAL_DISCOVERY` | P03.1 REVIEWED |
| 5 — the human cost, separately | ~USD $578 / $2,592 / $14,298 as an **illustrative sensitivity**, with both assumed rates visible and an explicit instruction not to add it to section 1. | `cost-model.md` §1.2, §5 | `PROPOSED`; **rates and workloads are ASSUMPTIONS, not a forecast or commitment** | `TECHNICAL_DISCOVERY` | P03.1 REVIEWED |
| 6 — what is not included | Payments/booking, legacy migration, marketing/acquisition/content/legal, salaries, a rendered map. | `cost-model.md` §10; `cost-alternatives.md` §11 | Exclusions, stated | `TECHNICAL_DISCOVERY` | P03.1 REVIEWED |
| 7 — what could raise the bill | Payments, legacy approval, a second operating language, runaway public traffic, a rendered map. | `cost-model.md` §8, §11; `internationalization-architecture.md` §4 | `PROPOSED` | `TECHNICAL_DISCOVERY` | P03.1 REVIEWED |
| 8 — what remains open | Rendering, authentication, map, geocoding vendor, region, AI-assisted intake — each with a named owner. | `p03-decision-reconciliation.md` §2, §4; `ADR-017`, `ADR-019`, `ADR-020` | Open decisions | `TECHNICAL_DISCOVERY` | P03.1 REVIEWED |

**The explainer names no vendor and no product**, and states in both languages that it is an illustrative planning scenario rather than a forecast.

## UX Preview v0.1

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. `presentation/ux-preview-v0.1.md` is bilingual because it is shown directly to the owner; this register remains English only. Added by P04.
>
> **It contains no figures of any kind** — no price, no percentage, no response time, no traffic number. The one quantity it carries is a **question count**, and it is stated as a design target rather than a measured result.

| Section | Claim or content | Canonical source | Evidence label | Provenance | Review status |
|---|---|---|---|---|---|
| 1 — customer journey | The customer describes a need, sees relevant providers, chooses one deliberately, sends one request, and receives a price, a question, or a no. **Superola does not send the request to everyone.** | `docs/04-ux/customer-journey.md`; `docs/04-ux/request-intake.md`; `WA-02` | `PROPOSED — OWNER VALIDATION REQUIRED` | `TECHNICAL_DISCOVERY` over `DAVID_DIRECTIVE` (`SRC-015`) | P04 REVIEWED |
| 2 — provider journey | Register, describe the business, say what you do and where, publish, receive relevant requests, answer with a price, a question, or a no. The provider controls whether to answer and can pause requests. | `docs/04-ux/provider-onboarding.md`; `docs/04-ux/provider-workspace.md`; `ADR-005` | `PROPOSED` | `TECHNICAL_DISCOVERY` | P04 REVIEWED |
| 3 — the request-intake principle | Superola asks only what the chosen service needs, so a mariachi request is short and a venue request asks different things. Compared against a competitor flow of roughly twenty presented steps. **Stated as a design target with the measurement that would confirm it, not as a measured improvement.** | `docs/04-ux/request-intake.md` §1–§3, §6–§7; `docs/07-research/gigsalad-request-flow-capture.md` | Superola target `PROPOSED`; competitor flow `OBSERVED` **as supplied, not independently re-verified** | `TECHNICAL_DISCOVERY`; competitor capture `DAVID_DIRECTIVE` (`SRC-015`) | P04 REVIEWED |
| 3 — the date is a request, not a promise | The customer states the date; the provider confirms whether it works. **This is the decision still owed by the owner** (`G-06`), stated in owner language without the gate ID in the body. | `ADR-005`; `WA-01`; `docs/04-ux/provider-profile.md` | `ASSUMPTION`; `G-06` UNSATISFIED | `DAVID_DIRECTIVE` | P04 REVIEWED |
| 4 — "what changes if…" | Payments in V1, sending to several providers, a real calendar with guaranteed dates, a mobile app, AI helping fill in the request, and no map drawn on screen. Each states what does **not** change alongside what does. | `docs/02-architecture/decision-branches.md` (`DB-01`, `DB-02`, `DB-06`, `DB-08`, `DB-10`); `docs/04-ux/map-decision.md`; `docs/04-ux/request-intake.md` §9 | `PROPOSED` / `FUTURE` | `TECHNICAL_DISCOVERY` | P04 REVIEWED |

**The preview names no framework, database, vendor, price, target, or delivery date, and no color, typeface, or brand.** It states in both languages that the design was built on working assumptions rather than on owner answers, that nothing is approved, and that it is structure rather than visual design.

### The competitor comparison, and its limit

The only comparative claim in the preview is the intake-length contrast. Its basis is `docs/07-research/gigsalad-request-flow-capture.md`: **twenty-four items supplied by David, recorded at step-shape granularity, not independently re-verified, and asserting nothing about wording, ordering, required-versus-optional status, or friction.** The preview must say in both languages that the comparison comes from a flow David observed and supplied. **If any recommendation ever turns on current competitor behavior, `AGENTS.md` requires re-verification against first-party sources with an access date, and this capture is insufficient for that purpose.**

## UX Walkthrough v0.1 — prototype, not a presentation artifact

`presentation/superola-ux-walkthrough-v0.1.html` is a **marked structural prototype**, not an owner-facing presentation artifact and **not** the P07 *Superola Decision & Cost Explorer*. It carries a persistent bilingual `PROTOTYPE — NOT FINAL DESIGN` banner, is self-contained with no external requests, and exists to let a reader walk the primary journey rather than read it. Its grayscale palette and system font stack are **placeholders required for any HTML to render at all**; no product surface may take a color, typeface, or measurement from it, and `plans/P04-ux-architecture.md` records that as a non-goal. It makes no claim requiring a source row.

## P07 Owner Review v1 — content source

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. The canonical editorial package is `superola-owner-review-v1.md`, `superola-owner-review-speaker-notes-v1.md`, `superola-owner-review-appendix-v1.md`, and `owner-decision-sheet-v1.md`. No rendered deck exists yet.

| Slide/artifact | Claim or content | Canonical source | External source/access date | Evidence label | Provenance | Review status |
|---|---|---|---|---|---|---|
| Core 1–2 | Superola is treated as a two-sided event-services marketplace and the presentation is a recommendation/decision conversation, not approved scope. | `docs/00-context/product-context.md`; `docs/01-product/actors.md`; `product-vision.md` | None | Marketplace framing `CONFIRMED` as `DAVID_DIRECTIVE`; owner scope `OPEN`; outcomes `PROPOSED` | `DAVID_DIRECTIVE`, `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Core 3 | Public inspection supports bounded observations but not private/internal conclusions or migration readiness. | `docs/00-context/source-register.md`; public audit §1.3; `docs/05-roadmap/migration-dependency.md` | Live Superola public platform, accessed 2026-08-13 | Public facts `OBSERVED`; internal state `UNKNOWN` | `LIVE_PLATFORM`, `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Core 4 | 43,361 public listing URLs differ from owner-reported ~43,000 registrations; 47/53 ownership sample is sample-bound; location/taxonomy/booking-surface observations remain public-only. | `docs/00-context/interview-evidence.md`; `product-context.md`; public audit §§3.2, 4.4, 8.1, 12.2, 17.2–17.4 | Live Superola public platform, accessed 2026-08-13 | Owner count `CONFIRMED` owner-reported/unaudited; public findings `OBSERVED`; relationship/internal state `UNKNOWN` | `OWNER_INTERVIEW`, `LIVE_PLATFORM` | P07 ADVERSARIAL REVIEWED |
| Core 5–8 | Branch L is the current one-recipient RFQ recommendation; customer/provider journeys and alternative-recipient reuse require deliberate choice, review, and a new request; automatic fan-out is not V1. | `docs/05-roadmap/mvp-definition.md`; `docs/02-architecture/decision-branches.md`; `docs/04-ux/customer-journey.md`; `provider-onboarding.md`; `provider-workspace.md`; `request-intake.md` | None | `PROPOSED — OWNER VALIDATION REQUIRED`; multi-recipient `FUTURE` | `TECHNICAL_DISCOVERY` over `DAVID_DIRECTIVE` working envelope | P07 ADVERSARIAL REVIEWED |
| Core 9 | Eligibility precedes ordering; paid placement cannot purchase eligibility; ranking inputs are candidates, not an approved formula. | `ADR-006`; `ADR-008`; `docs/05-roadmap/measurement-plan.md` | None | `PROPOSED` | `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Core 10 | Subscription and sponsored inventory are owner hypotheses; David recommends proof of marketplace value before a bounded manual sponsorship test. No current public marketplace listing monetization was observed. | `docs/01-product/monetization.md`; `docs/05-roadmap/phased-roadmap.md`; `measurement-plan.md`; public audit §11 | Live Superola public platform, accessed 2026-08-13 | Owner ideas `CONFIRMED` as unvalidated hypotheses; recommendation `PROPOSED`; public absence claim `OBSERVED` with historical state `UNKNOWN` | `OWNER_INTERVIEW`, `LIVE_PLATFORM`, `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Core 11 | Legacy use requires approval, controlled access, P05, classification, lawful-use findings, and a separate migration decision; fresh supply may proceed independently. | `docs/05-roadmap/migration-dependency.md`; `docs/06-migration/legacy-data-strategy.md` | None | P05 `NOT STARTED — INTERNAL ACCESS REQUIRED`; strategies `PROPOSED` | `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Core 12 | The roadmap is evidence-gated; roughly six months was discussed but is not a committed schedule. | `docs/05-roadmap/phased-roadmap.md`; `six-month-roadmap.md`; `dependencies.md` | None | Roadmap `PROPOSED`; six-month reference owner-reported/uncommitted | `OWNER_INTERVIEW`, `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Core 13 | Technical bills are USD $75.98/$192.99/$560.79, owner-facing rounded to USD $76/$193/$561; human sensitivities are ~USD $578/$2,592/$14,298 and remain separate. Rates USD $75/h technical and USD $40/h operations; workloads unmeasured. | `docs/03-technology/cost-model.md` §1.1, §1.2, §5; `cost-alternatives.md` | Official vendor pricing sources registered in P03, accessed 2026-08-11; reverify before purchase | `PROPOSED`; volumes, rates, workloads `ASSUMPTION`; not budget/forecast | `EXTERNAL_PRIMARY`, `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Core 14 | Branch T is viable but materially adds structured availability, transaction states, money movement, reconciliation, fraud/disputes/support, and transaction-derived reviews; existing costs exclude it. | `docs/05-roadmap/mvp-definition.md`; `phased-roadmap.md`; `docs/02-architecture/decision-branches.md`; `plans/P03-technology-evaluation.md` | None | Branch T `FUTURE` unless owner selects it; delta `PROPOSED` | `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Core 15–16 + decision sheet | Eight owner decisions and missing materials determine the pilot; authorized access precedes P05; silence is not approval. | `docs/05-roadmap/owner-decision-matrix.md`; `docs/01-product/open-questions.md`; `owner-reconciliation-matrix.md`; `docs/00-context/source-register.md` | None | Open questions / pending sources / process rule | `TECHNICAL_DISCOVERY`, `DAVID_DIRECTIVE` workflow discipline | P07 ADVERSARIAL REVIEWED |
| Appendix A1–A2 | Evidence-boundary explainer and expanded public observations. | `docs/00-context/glossary.md`; `interview-evidence.md`; `source-register.md`; public audit | Live Superola public platform, accessed 2026-08-13 | Mixed as stated: `CONFIRMED` owner-reported, `OBSERVED`, `UNKNOWN`, `PROPOSED` | Mixed as stated | P07 ADVERSARIAL REVIEWED |
| Appendix A3–A4 | One-deployable modular architecture and managed/reversible technology posture; no technology adopted. `ADR-020` remains proposed. | `docs/02-architecture/domain-map.md`; `system-architecture.md`; `ADR-001`; `docs/03-technology/technology-evaluation.md`; `p03-decision-reconciliation.md`; `ADR-020` | Official vendor/technology sources registered in P03, accessed 2026-08-11/12 where applicable | `PROPOSED — DAVID APPROVAL REQUIRED` | `EXTERNAL_PRIMARY`, `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Appendix A5 | Exact cost scenarios, explicit assumptions/exclusions, and price verification date. | `docs/03-technology/cost-model.md`; `cost-alternatives.md` | Official vendor pricing sources registered in P03, accessed 2026-08-11; reverify before purchase | `PROPOSED`; assumptions visible | `EXTERNAL_PRIMARY`, `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Appendix A6–A9 | Full owner decision groups, measurement model, five migration strategies, and Branch L/T delta. | `docs/05-roadmap/owner-decision-matrix.md`; `measurement-plan.md`; `migration-dependency.md`; `mvp-definition.md`; `phased-roadmap.md` | None | Open decisions / `PROPOSED` / `FUTURE` as stated | `TECHNICAL_DISCOVERY` | P07 ADVERSARIAL REVIEWED |
| Appendix A10 | RSVP/digital invitations and automated provider web presence are David proposals kept outside MVP; Digital Consulting is a current public business line. | `plans/P06-mvp-roadmap.md`; public audit §14.2 | Live Superola public platform, accessed 2026-08-13 | Opportunities `FUTURE — DAVID PROPOSAL`; Digital Consulting `OBSERVED` | `DAVID_DIRECTIVE`, `LIVE_PLATFORM` | P07 ADVERSARIAL REVIEWED |

P07 uses no externally sourced visual assets at the content-source stage. A later rendered-deck pass must add asset-level provenance and revalidate every `[Sources]` block after final slide editing.
