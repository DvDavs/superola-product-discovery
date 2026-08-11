# Superola Discovery Agent Guide

## Mission and current boundary

Build a durable, evidence-led product discovery and architecture workspace for Superola, a two-sided event-services marketplace. The repository is in discovery mode. Do not build the production application or select a final stack unless an approved plan explicitly requests it.

## Source of truth

- Repository state outranks chat history. Persist material findings, decisions, questions, and handoffs here.
- Start with `docs/00-context/product-context.md`, `interview-evidence.md`, `source-register.md`, and `assumptions.md`.
- Never invent missing owner materials, legacy data, analytics, prices, or requirements.

## Evidence labels

Use exactly: `CONFIRMED`, `OBSERVED`, `PROPOSED`, `ASSUMPTION`, `FUTURE`, `REJECTED`.

- Never promote `ASSUMPTION` to `CONFIRMED` without authoritative evidence.
- Never promote `PROPOSED` to a requirement or accepted decision without recording approval and rationale.
- Qualify owner-reported but unaudited numbers explicitly.
- Record material claim provenance using `docs/00-context/glossary.md`; evidence status and provenance are separate.
- Treat repository workflow/architecture guardrails as `DAVID_DIRECTIVE` unless an owner or approved-decision source explicitly says otherwise.

## Architecture guardrails

- Treat Superola as a real two-sided marketplace, not only a directory.
- Prefer clear domain boundaries, incremental delivery, small-team maintainability, and evidence-based scaling.
- Evaluate a modular monolith before distributed systems; do not assume either outcome.
- Keep messaging separate from notifications and organic ranking separate from paid placement.
- Treat geography, internationalization, SEO, security/privacy, monetization, and cost as structural concerns.
- Use build-vs-buy and total-cost-of-ownership analysis. Record reconsideration triggers for material decisions.
- A capability is not automatically a service or deployment unit. AI must augment sound deterministic design.

## Plans, decisions, and research

- Every substantial phase needs a living plan under `plans/` following `PLANS.md`.
- Material decisions require an ADR using `docs/02-architecture/adr/README.md`; do not create fake decisions.
- Technology/API/pricing research must record access date, authoritative primary sources, alternatives, tradeoffs, risks, costs, lock-in, and a reconsideration trigger.
- P01 may proceed from current evidence before pending owner materials arrive; reconcile those materials as a later track and revise hypotheses when needed.
- Internal canonical documentation is English only, including plans, evidence/context registers, product/architecture/technology/UX/roadmap/migration/research documents, ADRs, agent instructions, internal diagrams, technical decision records, and internal presentation-production support files.
- Bilingual Spanish + English is required only for artifacts and diagrams intended to be shown directly to the Superola owner. Do not create translated duplicates of internal documents or parallel language versions without a concrete owner-presentation need.

## Multi-agent collaboration

- The primary agent is the canonical writer and reconciles contradictions.
- Specialist agents are advisory and read-only by default. Their output is not canonical until reviewed and integrated.
- Delegate independent research, audits, comparisons, and adversarial review. Avoid concurrent edits to the same canonical file.
- Every handoff must cite repository inputs, label evidence, surface contradictions, and identify unresolved questions.

## Security and privacy

- Never commit secrets, credentials, tokens, production data, or unnecessary PII.
- Keep authorized legacy exports and production-derived datasets outside this repository; ignored local paths are only a safety net, not an approved storage workflow.
- Use synthetic examples. Treat contact, payment, conversation, and location data as sensitive.
- Do not approve scraping or third-party data acquisition without legal, privacy, copyright, terms, and technical review.

## Definition of done

A phase is done only when its plan is updated; deliverables and evidence are persisted; assumptions and decisions are traceable; citations are recorded; privacy and scope boundaries are checked; validation is documented; open questions and risks are updated; and a concise handoff identifies the next explicit phase.
