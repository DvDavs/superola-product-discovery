# P07 — Owner Presentation Pack

**Owner:** David
**Status:** IN PROGRESS — CONTENT SOURCE COMPLETE; RENDERING NOT STARTED
**Authorization:** Explicit user instruction to execute the owner-facing content package without generating a PPTX in this pass.

## Objective

Create a decision-led, bilingual presentation source that David can use in the next Superola owner interview to explain the current understanding, public evidence, recommended first release, alternatives, costs, access dependency, roadmap, and decisions that remain with the owner.

The communication job is:

> By the end, the Superola owner should understand and be able to challenge David's current recommendation because the recommendation is bounded by observable evidence, explicit unknowns, operating reality, and reversible next steps.

## Non-goals

- Do not create a PPTX or claim visual QA of a rendered deck.
- Do not build the production application or select/adopt a final stack.
- Do not execute P05, inspect private systems, classify legacy records, or promise migration.
- Do not turn public observations into internal facts or owner hypotheses into approved requirements.
- Do not decide Branch L, Branch T, availability semantics, launch market, contact policy, monetization, or success thresholds for the owner.
- Do not build the previously recorded interactive Decision & Cost Explorer in this content-source pass. It remains a later P07 rendering artifact requiring an explicit implementation pass.

## Context and authoritative inputs

This plan integrates the canonical outputs of P01/P01.1, P02, P03/P03.1, P04/P04.1, the bounded public legacy reconnaissance, and P06. Repository state, not prior preview wording, governs conflicts.

Primary inputs:

- `docs/00-context/product-context.md`, `interview-evidence.md`, `source-register.md`, `assumptions.md`, and the 2026-08-13 public audit.
- `docs/01-product/` product, monetization, open-question, and owner-reconciliation artifacts.
- `docs/02-architecture/` architecture overview, decision branches, and current ADR states.
- `docs/03-technology/` evaluation, reconciliation, and the cost model as the single arithmetic source of truth.
- `docs/04-ux/` design canon, customer/provider journeys, intake, discovery, and operator surfaces.
- `docs/05-roadmap/` MVP definition, roadmap, measurement, owner decisions, risks, and migration dependency.
- Existing owner-facing previews and presentation requirements under `presentation/`.

## Known constraints and evidence boundaries

- `PROPOSED — OWNER VALIDATION REQUIRED`: Branch L, Marketplace Leads/RFQ, is David's current recommendation, not an accepted owner decision.
- `FUTURE` unless the owner changes the boundary: Branch T adds booking, payment, payout, cancellation, refund, dispute, fraud, guarantees, reconciliation, support, and transaction-derived reviews.
- `OBSERVED`: the public platform published 43,361 listing URLs on 2026-08-13. This is not a count of providers, users, registrations, accounts, or migration-ready records.
- `OBSERVED`, sample-bound: 47 of 53 sampled pages referenced user id `1`; the real role and corpus-wide ownership distribution are unknown.
- `UNKNOWN — INTERNAL ACCESS REQUIRED`: private booking/payment state, internal ownership, consent, activity, monetization history, and migration viability.
- P05 remains `NOT STARTED — INTERNAL ACCESS REQUIRED`.
- `G-06` remains `UNSATISFIED`; accepting requests is not a promise of date availability.
- `ADR-020` remains `PROPOSED — DAVID APPROVAL REQUIRED` after P04's recommendation.
- Infrastructure/service cash cost and illustrative human operating sensitivity remain separate quantities.
- Owner-facing monetary figures use explicit USD notation.

## Narrative and visual strategy

The core deck uses 16 slides and a cumulative arc:

1. confirm the business interpretation;
2. show the investigation and its limits;
3. translate public observations into design lessons;
4. state the opportunity and recommended V1;
5. make both marketplace journeys concrete;
6. explain explicit-consent alternatives, relevance, and monetization;
7. separate legacy access, roadmap, and cost realities;
8. show Branch T without fearmongering;
9. ask the owner for a small set of decisions;
10. close on the next reversible step.

Visible slide copy is English. Spanish is reserved for David's speaker notes, with occasional short bilingual prompts only in the decision sheet. Each slide has one primary claim and one visual idea; evidence detail is kept in notes or appendix.

## Tasks and delegation strategy

- [x] Read repository status, planning conventions, presentation outline, and the Presentations skill guidance.
- [x] Extract product/public-evidence claims through a read-only product review.
- [x] Extract architecture/technology/cost claims through a read-only solution-architecture review.
- [x] Extract UX/roadmap/migration claims through a read-only UX review.
- [x] Reconcile current canonical state against stale preview wording.
- [x] Draft the 16-slide core source.
- [x] Draft Spanish speaker notes aligned one-to-one with the core source.
- [x] Draft a separate appendix and concise bilingual decision sheet.
- [x] Run the requested adversarial review and correct every high-severity finding.
- [ ] Produce or update rendered presentation artifacts in a separately authorized implementation pass.

Specialist outputs were advisory. The primary agent reconciled contradictions and remains the canonical writer.

## Decisions made and ADR impact

- `PROPOSED`: use a 16-slide core deck plus appendix; this is an editorial decision for the pack, not a product decision.
- `PROPOSED`: place the Branch L recommendation within the first five minutes, then show journeys and evidence.
- `PROPOSED`: frame legacy findings as lessons and audit priorities, not as a teardown.
- `PROPOSED`: keep the full stack and one-deployable architecture in the appendix; the core deck uses only the operating principle.
- No ADR was accepted, rejected, or otherwise changed by P07.

## Open questions and approval gates

Owner decisions intentionally remain open:

1. Branch L or Branch T for the first release.
2. Meaning of availability.
3. Single-recipient baseline.
4. Pilot geography and categories.
5. Legacy expectation, permission, and access process.
6. Contact/phone disclosure.
7. Monetization priority and experiment timing.
8. Pilot success, stop, and expansion criteria.

Required missing inputs include the promised feature/monetization document and prior UI/UX materials. P05 requires separate owner approval, authorized access, and data-handling controls.

## Validation and acceptance criteria

- [x] Every core and appendix slide has a title, owner-facing English text, recommended visual, Spanish speaker notes, evidence/source notes, and a decision or takeaway.
- [x] Branch L is a recommendation; Branch T is a viable material alternative.
- [x] Alternative-provider reuse requires deliberate selection, review, and a new explicit request; no automatic fan-out is V1.
- [x] Eligibility precedes ordering; sponsored placement cannot buy eligibility.
- [x] P05 and migration remain access-dependent and unexecuted.
- [x] Cost figures reconcile to the canonical P03 model and keep human sensitivity separate.
- [x] Human-sensitivity rates/workload assumptions and the price-verification date are visible beside owner-facing cost figures.
- [x] P07 claims are registered in `presentation/sources.md`.
- [x] RSVP and automated provider web presence are labeled `FUTURE — DAVID PROPOSAL` and kept outside MVP.
- [x] The owner receives no internal gate IDs, ADR numbers, or repository jargon on the main canvas.
- [ ] Rendered deck QA, overlap checks, typography checks, and full-slide visual inspection are deferred because no PPTX was authorized in this pass.
- [x] Run repository validation commands after adversarial review and final edits.

## Deliverables

- `plans/P07-presentation-pack.md`
- `presentation/superola-owner-review-v1.md`
- `presentation/superola-owner-review-speaker-notes-v1.md`
- `presentation/superola-owner-review-appendix-v1.md`
- `presentation/owner-decision-sheet-v1.md`

## Handoff and recommended next step

Use the decision sheet to prepare the owner interview. Rehearse the core deck as a 30–45 minute conversation, leaving time after slides 5, 14, and 15 for owner reactions. After owner feedback, reconcile every answer and received artifact before rendering a presentation or authorizing implementation.

The next P07 milestone is a separately authorized visual-production pass: select a visual system, render the deck, inspect every slide, and decide whether the recorded Decision & Cost Explorer is still required as a complementary artifact.

*Content-source milestone completed on 2026-08-13.*
