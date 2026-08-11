# Architecture Overview — Discovery Baseline

> Status: NOT SELECTED. P00 records principles and evaluation boundaries only. No production architecture is accepted.

> Provenance: these are `DAVID_DIRECTIVE` repository guardrails and `TECHNICAL_DISCOVERY` evaluation positions, not owner-confirmed requirements or accepted architecture.

## Principles to evaluate and preserve

1. Build for a two-sided marketplace, not only a directory.
2. Prefer clear domain boundaries and incremental delivery.
3. Strongly evaluate a modular monolith before microservices; neither is selected in P00.
4. Avoid premature Kubernetes, microservice, and event-stream complexity.
5. Separate business capabilities from external vendors/providers.
6. Model monetization and entitlements explicitly.
7. Treat internationalization, SEO, geography, privacy/security, and cost as structural concerns.
8. Keep messaging distinct from notification delivery and organic ranking distinct from paid placement.
9. Let future mobile needs influence stable API/domain boundaries without forcing premature mobile implementation.
10. Use AI to augment deterministic marketplace mechanics.
11. Scale infrastructure with evidence and optimize for a small initial engineering team.
12. Give every material decision a reconsideration trigger.

## Architecture questions deferred to later phases

- Validated marketplace and transaction boundary for MVP.
- Domain ownership, consistency boundaries, and integration contracts.
- Legacy coexistence, migration, cutover, and rollback strategy.
- Build-vs-buy boundaries for identity, payments, maps, communications, media, analytics, and AI.
- Security, privacy, compliance, abuse, observability, reliability, and data-retention requirements.
- Deployment topology and technology choices based on measured product and operational needs.

Material outcomes must be captured as ADRs; this file must not silently become an approval record.
