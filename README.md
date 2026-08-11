# Superola Product Discovery

Repository-based source of truth for Superola product discovery, architecture exploration, technology research, UX planning, legacy migration analysis, roadmap definition, and employer-facing presentation work.

## Current phase

`P00 — Bootstrap` and `P00.1 — Evidence & Workflow Hardening` are complete. `P01 — Product Discovery` has not started and still requires David's explicit authorization. This repository does **not** contain a production Superola application, and production implementation must not begin without an explicit approved phase.

## Start here

1. Read [AGENTS.md](AGENTS.md) for repository rules.
2. Read [docs/00-context/product-context.md](docs/00-context/product-context.md) for the durable project summary.
3. Read [docs/00-context/interview-evidence.md](docs/00-context/interview-evidence.md) before treating a claim as a requirement.
4. Check [docs/01-product/open-questions.md](docs/01-product/open-questions.md) and [docs/00-context/assumptions.md](docs/00-context/assumptions.md) for unresolved matters.
5. Use [PLANS.md](PLANS.md) and [plans/README.md](plans/README.md) to start or continue a phase.

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
- `diagrams/`, `prototype/`, `presentation/`: later-phase outputs; currently scaffolded only.

## Evidence discipline

Project claims use one of: `CONFIRMED`, `OBSERVED`, `PROPOSED`, `ASSUMPTION`, `FUTURE`, or `REJECTED`. A proposal is not a requirement, and an assumption is never silently promoted to confirmed fact.

Material claims also record provenance independently using the convention in [docs/00-context/glossary.md](docs/00-context/glossary.md). An owner statement, David directive, live observation, project dataset, owner artifact, external source, and technical discovery are not interchangeable merely because each may support a claim.

## Language policy

Internal engineering documentation is canonical in English. Anything prepared for direct presentation to the Superola owner must support Spanish and English.
