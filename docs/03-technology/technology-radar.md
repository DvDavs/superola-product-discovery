# Technology Radar — Discovery Backlog

> Status: NO TECHNOLOGY ADOPTED. All named technologies are candidates for later evidence-based comparison.

## Current engineering context

Evidence: `CONFIRMED`. Provenance: `DAVID_DIRECTIVE` (self-reported project context). David has experience with Kotlin, Java, Spring Boot, React, Next.js, TypeScript, PostgreSQL, PostGIS, Docker, CI/CD, n8n, GoHighLevel, AI-assisted development, and infrastructure/server work. This experience is an evaluation input for delivery risk and total cost of ownership; it does not select a technology.

## Candidate radar

| Area | `PROPOSED` candidates or directions | Required evaluation |
|---|---|---|
| Backend | Kotlin, Java, Spring Boot, Spring Modulith, alternative API/backend approaches | Domain fit, delivery speed, team skill, ecosystem, testing, operations, modularity, exit path. |
| Data | PostgreSQL, PostGIS, staged search evolution | Workload, geography, consistency, indexing, migration, operations, scale trigger. |
| Web | React/Next.js and alternatives | SEO, localization, rendering, accessibility, performance, team productivity, hosting. |
| Mobile | React Native/Expo and alternatives | Actual launch need, reuse value, native capability, delivery/operations cost. |
| Payments | Stripe Billing, Stripe Connect, alternatives | Subscription vs marketplace money flow, countries, tax, compliance, payouts, fees. |
| Storage/media | S3 or compatible services, managed media platforms | Rights, upload/security, transformations, CDN, egress, lifecycle, cost. |
| Cache/queues | Redis or managed alternatives only where justified | Concrete workload, failure semantics, operations, eviction/durability, scale threshold. |
| Maps | MapLibre/OSM ecosystem, Mapbox, Google Maps Platform, alternatives | Geocoding, places, tiles, coverage, terms, attribution, UX, cost, lock-in. |
| Cloud | AWS, GCP, Azure, managed PaaS alternatives | Service fit, small-team operations, regions, cost, security, scaling and exit path. |
| Automation | n8n and alternatives | Non-core workflow fit, observability, ownership, data exposure, failure/recovery. |
| Local development | Floci and alternative emulation/container approaches | Actual cloud choice, fidelity, maintenance, team workflow; never production infrastructure. |
| AI | Provider abstraction and multiple provider/model candidates | Validated use case, quality, privacy, latency, cost, evaluation, fallback, lock-in. |

Movement into an adopted/accepted state requires an ADR and human approval, not familiarity or appearance in this table.
