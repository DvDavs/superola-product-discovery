# Superola Product Discovery

Repository-based source of truth for Superola product discovery, architecture exploration, technology research, UX planning, legacy migration analysis, roadmap definition, and employer-facing presentation work.

## Current phase

`P00 — Bootstrap`, `P00.1 — Evidence & Workflow Hardening`, current-evidence `P01 — Product Discovery`, `P01.1 — Owner Reconciliation Prep`, `P02 — Domain & System Architecture`, and `P03 — Technology & Cost Evaluation` are complete. P01 recommends Marketplace Leads as `PROPOSED — OWNER VALIDATION REQUIRED`; pending owner materials and owner decisions must be reconciled before the hypothesis is treated as accepted scope. P01.1 added the reconciliation matrix, material intake process, P02/P04 release gate, and the bilingual owner-meeting guide.

P02 produced **one** `PROPOSED` logical architecture — ten domain modules, conceptual entities and lifecycles, an interaction and consistency map, search/internationalization/security requirements, twelve `PROPOSED` ADRs, fourteen decision branches, and P03 decision inputs. It was authorized explicitly by David and executed against a **David-accepted design envelope** (`SRC-013`), not against owner validation: the P01.1 release gate was **not satisfied** at P02 start (`G-06` and `G-10` unsatisfied; `G-01`, `G-04`, `G-05`, `G-07`, `G-09` partial). No architecture is accepted, and **P02 does not authorize P04**.

`P03 — Technology & Cost Evaluation` is also complete. It answered `D-01`–`D-15` and produced **one** recommended technology architecture with source-backed current prices, three cost scenarios, total-cost-of-ownership analysis, `ADR-013`–`ADR-019`, and a bilingual owner-facing technology preview. **Nothing is adopted** — every technology selection is `PROPOSED — DAVID APPROVAL REQUIRED`. `ADR-004` moved to `ACCEPTED` on a `DAVID_DIRECTIVE` and is the first accepted ADR in the repository. Payment technology was **not evaluated** (`G-02` unresolved), the AI gate was found **unanswerable during P03**, and the legacy pipeline remains gated on `G-09`. **P03 does not authorize P04**, which still needs `G-06`.

`P04 — UX Architecture` is complete. It designed the primary working UX scenario **without waiting for the remaining owner answers**, using five explicit working assumptions (`WA-01`–`WA-05`, recorded in [docs/04-ux/README.md](docs/04-ux/README.md)) rather than by treating any unresolved gate as satisfied. It produced one customer journey and one provider journey, a **progressive `CategoryArchetype`-governed request intake** replacing the universal-form model, the `RequestDraft` user model, thirty-nine named surfaces (thirty-six V1, three `FUTURE`) with their states and degraded cases, the eight-domain-queue-plus-index operator surface set, the dual-role UX that discharges `ADR-004`'s assigned consequence, accessibility and responsive baselines, low-fidelity wireframes, owner-facing diagrams and a bilingual UX preview. **It delivered recommendations for two decisions the technology track handed to it and gave the third a disposition:** `ADR-020` web rendering remains `PROPOSED` and is recommended as **Option A** — server rendering plus progressive enhancement plus bounded client islands. `UX-07` does contain navigation-persistent anonymous browser-local `RequestDraft` state; P04 proposes handling it within the bounded `I-2` island rather than adopting a richer client rendering model. This does not imply a SPA, React, a client router, or Option B, and remains an implementation hypothesis until `RQ-03` is measured. `ADR-019` Level 3 is answered with **no rendered map in V1**, deferred with three named triggers and with the privacy invariant explicitly **not** used as the reason; AI-assisted request intake is **`FUTURE`**, worth testing only under four named conditions. **Nothing is approved.** Both decisions are recommendations requiring David's individual approval. **`G-06` is still unsatisfied**, `Q-007` is still open, `SRC-004` is still NOT RECEIVED, and `OR-016` reconciliation still precedes acceptance of any P04 design claim. **P04 does not authorize P05, P06, or P07.**

`P04.1 — UX Reconciliation`, the bounded Public Legacy Reconnaissance, and `P06 — MVP & Roadmap` are complete. P06 was executed under explicit David authorization (`SRC-016`) and produced a **conditional decision package**, not an owner-approved MVP, dated schedule, budget, migration, or implementation authorization. `P05 — Legacy Audit & Migration` remains `NOT STARTED — INTERNAL ACCESS REQUIRED`; the public audit does not replace it. `P07 — Presentation Pack` is `IN PROGRESS — CONTENT SOURCE COMPLETE`: the 16-slide owner-review source, Spanish speaker notes, appendix, and decision sheet exist, while rendered-deck production and visual QA have not started. The canonical phase table is [plans/README.md](plans/README.md).

This repository does **not** contain a production Superola application, and production implementation must not begin without an explicit approved phase.

## Start here

1. Read [AGENTS.md](AGENTS.md) for repository rules.
2. Read [docs/00-context/product-context.md](docs/00-context/product-context.md) for the durable project summary.
3. Read [docs/00-context/interview-evidence.md](docs/00-context/interview-evidence.md) before treating a claim as a requirement.
4. Check [docs/01-product/open-questions.md](docs/01-product/open-questions.md) and [docs/00-context/assumptions.md](docs/00-context/assumptions.md) for unresolved matters.
5. Before acting on any owner answer or owner material, use [docs/01-product/owner-reconciliation-matrix.md](docs/01-product/owner-reconciliation-matrix.md) for the intake process and the P02/P04 release gate.
6. Use [PLANS.md](PLANS.md) and [plans/README.md](plans/README.md) to start or continue a phase.

## Repository map

- `docs/00-context/`: canonical context, evidence, sources, assumptions, and terminology.
- `docs/01-product/`: product discovery artifacts.
- `docs/02-architecture/`: the P02 architecture recommendation and ADRs. Start at [architecture-overview.md](docs/02-architecture/architecture-overview.md), which indexes every P02 artifact. [decision-branches.md](docs/02-architecture/decision-branches.md) records what changes under each owner alternative and which release gates were unsatisfied.
- `docs/03-technology/`: the P03 technology recommendation, as reconciled by P03.1. **Start at [p03-decision-reconciliation.md](docs/03-technology/p03-decision-reconciliation.md)** — it holds the per-decision disposition table and the approval packet, and records that **three decisions are on `HOLD`**. [technology-evaluation.md](docs/03-technology/technology-evaluation.md) carries the recommended stack and the per-decision records; [cost-model.md](docs/03-technology/cost-model.md) holds the three cost scenarios and is the **single arithmetic source of truth** for cost; [cost-alternatives.md](docs/03-technology/cost-alternatives.md) explains why each cost decision was taken and what would flip it; [technology-radar.md](docs/03-technology/technology-radar.md) is the one place vendor names carry a recommendation.
- `docs/04-ux/`: the P04 UX architecture. **Start at [README.md](docs/04-ux/README.md)** — it carries the five working assumptions every other P04 document depends on, the artifact map, and what P04 did and did not decide. [surface-inventory.md](docs/04-ux/surface-inventory.md) is the evidence base for the rendering decision; [rendering-evidence.md](docs/04-ux/rendering-evidence.md) and [map-decision.md](docs/04-ux/map-decision.md) carry the two decisions returned to product by P03.1.
- `docs/05-roadmap/`: MVP, roadmap, risks, and dependencies.
- `docs/06-migration/`: legacy-data discovery and migration strategy.
- `docs/07-research/`: focused research work packages.
- `plans/`: living execution plans and handoffs.
- `.codex/agents/`: project-scoped specialist agents.
- `diagrams/`, `prototype/`: later-phase scaffolds. `presentation/` contains the bilingual owner-facing previews, the P01.1 owner-meeting guide, the P03.1 [cost explainer](presentation/cost-explainer-v0.1.md), and the P07 owner-review content source, speaker notes, appendix, and decision sheet. The rendered P07 deck and the self-contained *Superola Decision & Cost Explorer* HTML artifact have not been produced; their remaining production requirements are persisted in [presentation/outline.md](presentation/outline.md) and [plans/P07-presentation-pack.md](plans/P07-presentation-pack.md).

## Evidence discipline

Project claims use one of: `CONFIRMED`, `OBSERVED`, `PROPOSED`, `ASSUMPTION`, `FUTURE`, or `REJECTED`. A proposal is not a requirement, and an assumption is never silently promoted to confirmed fact.

Material claims also record provenance independently using the convention in [docs/00-context/glossary.md](docs/00-context/glossary.md). An owner statement, David directive, live observation, project dataset, owner artifact, external source, and technical discovery are not interchangeable merely because each may support a claim.

## Language policy

Internal canonical documentation is English only. This includes plans, context/evidence registers, product and architecture documents, ADRs, research, UX/roadmap/migration working documents, source registers, agent instructions, technical decision records, internal diagrams, and internal presentation-production support files.

Bilingual Spanish + English is required only for artifacts or diagrams intended to be shown directly to the Superola owner, such as the Discovery Preview and future presentation slides. Do not maintain Spanish duplicates or parallel language versions of internal documents without a concrete owner-presentation need.
