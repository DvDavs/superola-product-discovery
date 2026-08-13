# P06 — MVP and Roadmap

## Metadata

- ID: P06
- Title: MVP and Roadmap
- Owner: David
- Status: `COMPLETED`
- Authorization: explicit instruction from David (`SRC-016`) to execute P06 without waiting for P05. This phase approves no owner decision, performs no legacy audit, and authorizes no implementation.

## Objective

Produce a decision-ready MVP definition, dependency-led roadmap, owner decision matrix, launch measurement plan, and migration dependency record that can guide a bounded pilot without pretending that legacy data or owner strategy questions have been resolved.

P06 must answer what the smallest coherent launch slice is, what is deliberately deferred, which choices materially change scope or operating cost, what may start while decisions remain open, what requires owner approval, and what cannot proceed before authorized internal access and P05.

## Non-goals

- No production code, application scaffolding, prototype, database change, migration script, authentication test, payment integration, or new public crawl.
- No P05 execution and no inference about internal data from the public platform audit.
- No delivery dates, staffing commitments, numeric KPI targets, or budget commitments without evidence.
- No promotion of a `PROPOSED` recommendation to `ACCEPTED`.
- No reopening of `ADR-004`; one Account may act as customer and provider under the accepted `DAVID_DIRECTIVE`.
- No P07 presentation or owner-facing artifact. Internal canonical documents remain English only.

## Context and authoritative inputs

| Input | P06 use |
|---|---|
| `AGENTS.md`, `PLANS.md`, `README.md`, `plans/README.md` | Repository governance, phase status, evidence discipline, and plan structure. |
| `plans/P01-product-discovery.md`, `plans/P01.1-owner-reconciliation-prep.md`, `docs/01-product/` | Launch-slice hypothesis, owner questions, monetization hypotheses, actors, journeys, and unresolved reconciliation. |
| `plans/P02-domain-architecture.md`, `docs/02-architecture/`, relevant ADRs | Existing domain seams and decision status. Capabilities are not treated as deployment units. |
| `plans/P03-technology-evaluation.md`, `plans/P03.1-decision-reconciliation.md`, `docs/03-technology/` | Proposed technology path, cost/TCO evidence, holds, and reconsideration triggers. |
| `plans/P04-ux-architecture.md`, `plans/P04.1-reconciliation.md`, `docs/04-ux/` | Proposed customer/provider journeys, surface and operator inventory, request-draft behavior, rendering evidence, and P05/P06 handoff. |
| `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md` | `OBSERVED — LIVE_PLATFORM` evidence only. It is not P05 and does not evidence internal capability or internal/corpus-wide data quality; public defects and sampled signals remain bounded to what was observed. |
| `docs/00-context/product-context.md`, `source-register.md`, `interview-evidence.md`, `assumptions.md`, `glossary.md` | Claim provenance, owner-reported context, source availability, and controlled vocabulary. |
| Existing `docs/05-roadmap/` records | P01 baseline, dependency map, and canonical risk register to reconcile rather than duplicate. |

## Evidence and decision rules

- Repository labels remain `CONFIRMED`, `OBSERVED`, `PROPOSED`, `ASSUMPTION`, `FUTURE`, and `REJECTED`.
- Provenance remains distinct from evidence status: `OWNER_INTERVIEW`, `DAVID_DIRECTIVE`, `LIVE_PLATFORM`, and other glossary sources are not substitutes for evidence labels.
- `43,361` means public listing URLs. It never means providers, users, accounts, registrations, or migration-ready supply.
- Ownership findings are signals from a 53-page sample, not corpus-wide distribution.
- Public booking/reservation language does not prove an internal booking, payment, refund, payout, or dispute lifecycle. The separate guarantee-marketing claim in the authorization brief is not present in the canonical public audit and remains an owner-reconciliation question until evidence is registered.
- P05 remains `NOT STARTED`; its execution depends on owner approval, authorized access, and data-handling controls.
- `G-06` remains `UNSATISFIED`. The working assumption is `accepting requests != guaranteed date availability`; V1 contains no universal booking calendar.
- `ADR-020` remains `PROPOSED — DAVID APPROVAL REQUIRED`.

## Work performed

1. Reconciled the P01 launch hypothesis with P02 architecture seams, P03 cost/TCO evidence, P04 UX scope, and the public legacy reconnaissance.
2. Defined Branch L, the recommended leads/RFQ V1, and Branch T, the high-scope transactional alternative available only after explicit owner direction.
3. Classified capabilities by launch importance, value, complexity, evidence confidence, owner dependency, P05 dependency, and recommended stage.
4. Defined a dependency- and evidence-gated roadmap rather than a date promise.
5. Defined day-one telemetry, pilot marketplace metrics, alternative-provider measures, and later monetization measures without inventing targets.
6. Compared legacy-supply strategies without choosing one before P05.
7. Reconciled and extended the canonical risk register.
8. Preserved P07 presentation inputs, including the two `P07 FUTURE BUSINESS OPPORTUNITY — DAVID PROPOSAL` ideas, without moving either into architecture or MVP.

## Delegation and reconciliation

Specialist review was advisory and read-only. The primary agent remained the canonical writer and reconciled product, architecture/cost, UX/measurement, and legacy/roadmap findings against current repository files before integration. No specialist was authorized to change canonical files.

## Recommendations produced, not decisions made

| Topic | P06 disposition | Status |
|---|---|---|
| Launch model | Branch L: a bounded Marketplace Leads/RFQ pilot. | `PROPOSED — OWNER VALIDATION REQUIRED` |
| Provider alternatives | Preserve the seam in V1; test the deliberate alternative-selection and request-reuse flow during the bounded pilot after the single-recipient path works. | `PROPOSED — OWNER VALIDATION REQUIRED` |
| Multi-provider sending | Not V1. A future explicit-consent branch only; never automatic fan-out. | `FUTURE` |
| Sponsored ranking | Eligibility first; paid placement may reorder only eligible inventory and must be disclosed and measurable. | `PROPOSED — OWNER VALIDATION REQUIRED` |
| First monetization experiment | A manually operated, time-bounded, fixed-fee sponsored placement experiment in one eligible market cell after organic liquidity is credible; no auction, CPC engine, or generic ad platform. | `PROPOSED — OWNER VALIDATION REQUIRED` |
| Transactions | Branch T is a consequence model, not the selected roadmap. Consider only after an explicit owner requirement or later evidence gates. | `FUTURE` unless owner changes the first-release boundary |
| Legacy supply | Keep fresh, claim-first, curated, broad-migration, and hybrid strategies open until P05. | `UNKNOWN — INTERNAL ACCESS REQUIRED` |
| Digital Consulting | Existing public business line; strategy unknown; outside core V1 architecture. | `OBSERVED — LIVE_PLATFORM`; owner decision required |

## Approval gates and open questions

The prioritized gate list is canonical in `docs/05-roadmap/owner-decision-matrix.md`. The highest-leverage unresolved questions are:

1. Does the first release stop at leads/RFQ, or must it implement booking/payment?
2. What does “available” promise a customer?
3. Does the owner accept deliberate single-recipient RFQs as the baseline?
4. Which geography and categories define the pilot?
5. What legacy migration outcome is expected, and is there permission to use or contact legacy records?
6. Is provider phone public, conditionally disclosed, or kept in-platform?
7. Which monetization hypothesis is worth testing first, and under what trust rules?
8. Do Digital Consulting and shop/merchandise survive the rebuild?
9. What are the owner’s pilot success and stop criteria?
10. When will the feature/monetization and UI/UX materials be supplied and reconciled?

## What may begin before additional owner decisions

- Requirements/contracts, acceptance criteria, analytics schemas, privacy classification, taxonomy/place governance, publication/eligibility rules, and reversible technical spikes for the Branch L baseline.
- Curated fresh-supply planning that makes no legacy use.
- Dual-role Account behavior already governed by accepted `ADR-004`.
- Organic `placementBasis`, legacy isolation, and the request-reuse seam as dormant structural facts, without activating sponsored placement, migration, or alternatives UX.

Productive implementation must not silently decide Branch L/T, launch market/categories, availability wording, contact disclosure, pilot success criteria, monetization, legacy use, held authentication/geocoding decisions, or `ADR-020`. Those require their named approvals before the affected work begins or ships.

## Contradictions and evidence gaps left open

- `docs/01-product/user-journeys.md` still says providers receive requests they “can actually serve.” That wording can imply confirmed feasibility and must be reconciled before owner approval because `G-06` remains unsatisfied; P06 does not silently reinterpret it.
- The public audit section title says there is no booking/quote, while its body correctly limits the finding to what is publicly demonstrated and marks internal state unknown. P06 uses the bounded body claim only.
- The authorization brief reports money-back-guarantee marketing, but the canonical public audit does not record it. P07 must not present it as an observed audit finding without a registered source or owner confirmation.
- The P04 handoff counted nine queues while its own next row and operator canon counted eight case queues plus one cross-queue index. P06 corrected this documentation contradiction; no UX design was reopened.
- Owner-reported approximately 43,000 registrations and 43,361 observed public listing URLs are different units whose relationship is unknown until P05.

## Validation and acceptance criteria

P06 is complete only when:

- All required P06 artifacts exist and are internally consistent.
- The capability table includes every capability named in the authorization brief.
- Every roadmap stage has a goal, scope, entry and exit evidence, decisions, risks, dependencies, explicit exclusions, and metrics.
- Metrics have definitions but no fabricated business targets.
- The migration record states what the public audit can and cannot support and keeps all five supply strategies open pending P05.
- The decision matrix includes D1–D18 and preserves existing accepted/proposed statuses.
- The canonical risk register is updated without duplicating existing risks unnecessarily.
- Adversarial review finds no accidental assumption that public URLs are usable supply, P05 is complete, booking is absent internally, “Destacado” is paid, sponsorship outranks eligibility, multi-recipient fan-out is V1, or future David ideas are requirements.
- `git diff --check`, `git diff --name-only`, and `git status --short` are recorded.
- No production code, P05 artifact, migration implementation, commit, or push is created.

## Adversarial review result

`PASS`. The final read-only review verified all six required new artifacts, D1–D18, all required capabilities, all nine roadmap stages and their fields, evidence labels, owner-disagreement branches, P05/G-06/ADR statuses, the public-URL and sample boundaries, single-recipient alternatives reuse, optional monetization, causal-language gates, TCO framing, team/capacity responsibilities, P07-only business ideas, risk deduplication, and repository status reconciliation.

Material issues found and corrected before the pass included: premature completion status; human-cost rates not shown next to sensitivity figures; monetization accidentally made a mandatory roadmap predecessor; causal “incremental” language without an experimental gate; unsafe fresh-consent wording; missing UX/accessibility/privacy/content approval for the alternatives delta; missing source registration (`SRC-016`); stale nine-queue wording; duplicated alternatives risk; and stale root/roadmap completion semantics.

## Validation results

- `git diff --check` — `PASS`; only informational LF→CRLF working-copy warnings on Windows.
- `git diff --name-only` — recorded all modified tracked canonical documents; untracked required P06 artifacts were verified separately because `git diff --name-only` does not list them.
- `git status --short` — only documentation changes; no production code, P05 audit artifact, migration script, commit, or push.
- Required artifacts — all six exist.
- Coverage checks — 30 requested capabilities, nine stages (`0`–`8`), and 18 owner decisions (`D1`–`D18`) present.
- Forbidden-claim scan — no claim that 43,361 means providers/users/accounts/registrations; no P05 completion; no `G-06` satisfaction; no `ADR-020` acceptance; no automatic fan-out in V1.
- P07 scope scan — RSVP invitations and automatic provider cards/web pages occur only as `P07 FUTURE BUSINESS OPPORTUNITY — DAVID PROPOSAL` inputs.

## Deliverables

- `plans/P06-mvp-roadmap.md`
- `docs/05-roadmap/mvp-definition.md`
- `docs/05-roadmap/phased-roadmap.md`
- `docs/05-roadmap/owner-decision-matrix.md`
- `docs/05-roadmap/measurement-plan.md`
- `docs/05-roadmap/migration-dependency.md`
- Updated `docs/05-roadmap/risks.md`
- Updated `docs/05-roadmap/dependencies.md` and replaced the stale six-month placeholder with a timing note pointing to the evidence-gated roadmap
- Corrected the P04 handoff's stale “9 queues” claim to the canonical eight case queues plus one cross-queue index
- Updated `plans/README.md`

## P07 handoff

P07 must turn the following into bilingual Spanish/English owner-facing decision material, not simply copy internal documents:

- the proposed V1 journey and Branch L recommendation;
- the public legacy findings, including `43,361 public listing URLs` versus owner-reported registrations;
- the sample-bound ownership-concentration signal from 53 pages;
- location and taxonomy quality examples;
- public booking marketing versus the absence of public evidence for a structured transaction lifecycle, plus the authorization brief's guarantee claim clearly marked as awaiting registered evidence or owner confirmation;
- owner decision branches, roadmap gates, cost/TCO, monetization candidates, sponsored placement, provider alternatives, and the P05 access dependency;
- Digital Consulting as an existing public business line with strategy unknown;
- `P07 FUTURE BUSINESS OPPORTUNITY — DAVID PROPOSAL`: customer-side RSVP/digital invitations;
- `P07 FUTURE BUSINESS OPPORTUNITY — DAVID PROPOSAL`: automatic provider cards/web pages, potentially a scalable extension of Digital Consulting.

Neither future business idea is an MVP requirement or architecture input.

## Recommended next step

Use the owner decision matrix for a focused owner interview. Engineering planning may refine reversible foundation work after David approves the held technical decisions, but launch implementation must not be represented as approved until the owner settles the high-leverage product gates. P05 should run only after owner approval, internal access, and data-handling controls exist.

---

*Execution record: P06 performed on 2026-08-13 against repository baseline `abe8b01`. No commit or push was requested.*
