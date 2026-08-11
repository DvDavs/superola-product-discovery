# Changelog

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
