# Changelog

## 2026-08-11 — P02 domain and system architecture completed

- Produced **one** `PROPOSED` logical architecture, not several: ten marketplace modules (Identity & Access, Provider, Catalog, Geography, Demand, Conversation, Discovery, Notification, Marketplace Operations, Marketplace Analytics), one application layer as the single write path, one cross-cutting audit facility, and one conditional legacy boundary.
- Rejected as modules, each with a promotion trigger: Availability (`V1 has no availability model`), Monetization (a reserved policy seam), Quote (an entity inside the request aggregate), Media, Public Distribution, Legacy as a runtime module, and the pre-P02 Bookings, Payments, Subscriptions, Reviews, AI/Matching, and Favorites capabilities.
- Added `docs/02-architecture/domain-model.md` with conceptual entities, twenty-three required distinctions, the category-extensibility decision, a geography model with three-valued location eligibility, and six Mermaid lifecycle state machines.
- Added `system-architecture.md` (deployment shape `PROPOSED — TO BE VALIDATED IN P03`, interaction and consistency map, transactional boundaries, the durable-deferred-work requirement that is explicitly **not** an event bus, extension points with an admissibility test, and twenty-one checkable principles), `search-architecture-requirements.md`, `internationalization-architecture.md`, `security-privacy-architecture.md`, `decision-branches.md`, and `p03-decision-inputs.md` (`D-01`–`D-15`).
- Added `ADR-001`–`ADR-012`, all `PROPOSED`. Recorded four decisions deliberately **not** written as ADRs, including "no event bus", because the absence of a mechanism is not a decision worth an ADR.
- Added bilingual `presentation/architecture-preview-v0.1.md` with a "what changes if…" section covering six owner alternatives, stated as what does **not** change alongside what does.
- Recorded honestly that the P01.1 release gate was **not satisfied** at P02 start: `G-06` (availability claim) and `G-10` (owner material status) unsatisfied; `G-01`, `G-04`, `G-05`, `G-07`, `G-09` partial. P02 proceeded under that document's own statement that the gate is a readiness test rather than an authorization. **P02 does not authorize P04.**
- Renamed the transaction cluster from the brief's "V1 Phase 2" to **Transaction Extension — `FUTURE`, separately owner-approved**, because a scheduled phase name silently resolves `A-004`.
- Separated the architecture capability envelope from launch operational scope, with named scenarios `S-1` and `S-2`, and stamped every artifact with the scenario it assumes. Recorded the finding that **locale does not follow country**: owner-reported category evidence indicates a Spanish-speaking segment inside the United States.
- Ran the three-way category-variability comparison `docs/01-product/feature-inventory.md` chartered for P02, rather than accepting the envelope's pre-selected answer; recorded the outcome as `ADR-007` and the envelope's preference as `A-018`.
- Added assumptions `A-015`–`A-025`, questions `Q-019`–`Q-032` (several marked as legal questions with counsel as decision owner), and risks `R-023`–`R-032`. Registered the P02 brief as `SRC-013`, noting that no hash is recorded because it was supplied as conversation input.
- Revised `docs/00-context/glossary.md` substantially: retired `Vendor` for the supply side and `Listing`; renamed `Service area` to `ServiceAreaDeclaration`, `Quote` to a kind of `ProviderResponse`, `Claimable profile` to `LegacyProviderRecord` plus `OwnershipClaim`; downgraded `Event` to a value object; and marked date and resource `Availability` as `REJECTED` for V1.
- Advanced the three pre-P02 architecture placeholders so they no longer contradict P02, including correcting the staged search hypothesis to read as a branch that may never be taken rather than an inevitability with a schedule.
- Selected **no** technology, cloud, database, search product, message broker, or payment vendor; introduced **no** AI protocol as architecture; invented **no** microservice requirement; assumed **no** full legacy migration; and published **no** numeric non-functional-requirements table, deliberately.
- P01 and the Marketplace Leads recommendation remain unchanged and `PROPOSED — OWNER VALIDATION REQUIRED`; P03–P07 remain NOT STARTED.

## 2026-08-11 — P01.1 owner reconciliation prep completed

- Added `docs/01-product/owner-reconciliation-matrix.md` with a reconciliation record model, seventeen seeded baseline topics (`OR-001`–`OR-017`), an intentionally empty reconciliation ledger, the owner-material intake process, the P02/P04 release gate, and the owner-material checklist.
- Added bilingual `presentation/owner-meeting-guide-v0.1.md` with seven CORE owner decisions, five IF TIME topics, a practical meeting sequence, the materials request, and a capture template mapped to matrix IDs.
- Refined `presentation/discovery-preview-v0.1.md`: an explicit statement of what Superola is, and a closing section on how owner answers are reconciled and what follows.
- Registered the meeting guide and the new preview section in `presentation/sources.md`.
- Separated BLOCKING gates for P02/P04 from uncertainty that may remain open, so pricing, sponsored mechanics, mobile implementation, protocols, and technology cannot hold the gate.
- No architecture, technology, data model, API, wireframe, or cost figure was introduced. P01 and the Marketplace Leads recommendation remain unchanged and `PROPOSED — OWNER VALIDATION REQUIRED`; P02–P07 remain NOT STARTED.

## 2026-08-11 — P01 product discovery completed

- Compared three launch slices and recommended direct-RFQ Marketplace Leads as `PROPOSED — OWNER VALIDATION REQUIRED`.
- Completed customer/provider/operator outcomes, category variability, product-state journeys, V1 trust minimum, monetization staging, and success-signal definitions.
- Preserved booking/payment, general matching, premature sponsorship, universal realtime availability, protocol adapters, and unproven AI complexity outside the recommended V1 boundary.
- Added a seven-question owner-meeting core and bilingual `Discovery Preview v0.1` with claim-level source mapping.
- Product, cost, and architecture-scope re-reviews passed. Initial final critical review found four HIGH issues; all were corrected and focused critical re-review returned `PASS`, closing P01 on current evidence.
- Clarified that internal canonical and presentation-support documentation is English only; bilingual Spanish + English is reserved for owner-facing presentation artifacts and diagrams.

## 2026-08-11 — P01 competitive and AI-distribution addition

- Benchmarked current public GigSalad and The Bash marketplace patterns without promoting them into requirements.
- Added AI discoverability, future agent-action foundations, protocol classifications, and explicit V1 anti-inflation boundaries.
- Reconciled product questions, risks, feature inventory, launch-scope placeholders, and optional presentation concept.

All notable workspace changes are recorded here. This log covers discovery artifacts and operating conventions, not a production application.

## [Unreleased]

### Added

- Completed P00 repository bootstrap with canonical context, evidence registers, planning conventions, ADR template, six specialist Codex agents, later-phase work-package scaffolding, and a persisted validation record.

### Changed

- Normalized evidence labels, preserved launch geography and booking/payment as open scope, clarified source provenance, added a provisional P01 launch-slice boundary for later work, and strengthened legacy-data safety guidance.
- P00.1 separates evidence status from provenance, persists a sanitized owner-evidence ledger, allows P01 current-evidence discovery before owner materials arrive, authorizes a bilingual working preview, and makes phase sequencing explicitly iterative.
- Started P01 Track A with bounded GigSalad/The Bash benchmarking and distinct internal-AI, external-discoverability, agent-distribution, and emerging-protocol research workstreams.
