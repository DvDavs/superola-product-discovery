# Superola Product Discovery

Repository-based source of truth for Superola product discovery, architecture exploration, technology research, UX planning, legacy migration analysis, roadmap definition, and employer-facing presentation work.

## Current phase

`P00 — Bootstrap`, `P00.1 — Evidence & Workflow Hardening`, current-evidence `P01 — Product Discovery`, and `P01.1 — Owner Reconciliation Prep` are complete. P01 recommends Marketplace Leads as `PROPOSED — OWNER VALIDATION REQUIRED`; pending owner materials and owner decisions must be reconciled before the hypothesis is treated as accepted scope. P01.1 added the reconciliation matrix, material intake process, P02/P04 release gate, and the bilingual owner-meeting guide; the gate is defined but not yet satisfied. This repository does **not** contain a production Superola application, and production implementation must not begin without an explicit approved phase.

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
- `docs/02-architecture/`: architecture hypotheses and ADRs.
- `docs/03-technology/`: technology, build-vs-buy, infrastructure, and cost research.
- `docs/04-ux/`: structural user-flow work.
- `docs/05-roadmap/`: MVP, roadmap, risks, and dependencies.
- `docs/06-migration/`: legacy-data discovery and migration strategy.
- `docs/07-research/`: focused research work packages.
- `plans/`: living execution plans and handoffs.
- `.codex/agents/`: project-scoped specialist agents.
- `diagrams/`, `prototype/`: later-phase scaffolds. `presentation/` contains the P01 bilingual working preview and the P01.1 bilingual owner-meeting guide; the mature P07 presentation pack remains not started.

## Evidence discipline

Project claims use one of: `CONFIRMED`, `OBSERVED`, `PROPOSED`, `ASSUMPTION`, `FUTURE`, or `REJECTED`. A proposal is not a requirement, and an assumption is never silently promoted to confirmed fact.

Material claims also record provenance independently using the convention in [docs/00-context/glossary.md](docs/00-context/glossary.md). An owner statement, David directive, live observation, project dataset, owner artifact, external source, and technical discovery are not interchangeable merely because each may support a claim.

## Language policy

Internal canonical documentation is English only. This includes plans, context/evidence registers, product and architecture documents, ADRs, research, UX/roadmap/migration working documents, source registers, agent instructions, technical decision records, internal diagrams, and internal presentation-production support files.

Bilingual Spanish + English is required only for artifacts or diagrams intended to be shown directly to the Superola owner, such as the Discovery Preview and future presentation slides. Do not maintain Spanish duplicates or parallel language versions of internal documents without a concrete owner-presentation need.
