# Dependency Map — Discovery Baseline

| Downstream work | Required upstream evidence |
|---|---|
| P01 Product Discovery | Explicit authorization plus current interview evidence, assumptions, questions, and hypotheses. Pending owner materials are Track B inputs, not a Track A start blocker. |
| P02 Domain Architecture | Validated actors, journeys, marketplace boundaries, invariants, scope-changing answers, and the current `PROPOSED` launch-slice hypothesis. **Executed against a David-accepted design envelope (`SRC-013`) rather than owner validation; the P01.1 release gate was not satisfied — see `docs/02-architecture/decision-branches.md`.** |
| P03 Technology Evaluation | P02 decision problems, non-functional needs, operating model, scale/cost scenarios, and only the capabilities implicated by the current launch-slice hypothesis or explicit future-proofing constraints. **Now available as `docs/02-architecture/p03-decision-inputs.md` (`D-01`–`D-15`). Several problems are additionally gated: `D-13` needs a zero-result corpus first; `D-14` needs `G-09` and its legal questions; `D-15` must not be evaluated while `G-02` is unresolved.** |
| P04 UX Architecture | Product journeys, actors, policy, trust, monetization, channel scope, and the current launch-slice hypothesis with confidence limits. **Executed under explicit David authorization using documented working assumptions; no owner gate was silently treated as satisfied.** |
| P05 Legacy Audit & Migration | Authorized system/data access, privacy handling, schema/export, migration questions, and the launch-data needs being tested. |
| P06 MVP & Roadmap | P01 launch-slice hypothesis reconciled with architecture alternatives, UX structure, migration/access uncertainty, and cost/team constraints. **Executed under explicit David authorization without P05; completion is a conditional decision package, not an approved MVP or schedule.** |
| P07 Presentation Pack | Reviewed recommendations, approved decisions, citations, and bilingual editorial review. |

## P01 evidence tracks

- **Track A — Current-evidence discovery:** use SRC-001, current assumptions, open questions, and product hypotheses to produce a `PROPOSED` discovery model and launch-slice hypothesis. An explicitly authorized P01 may begin this track before pending owner materials arrive.
- **Track B — Owner-material reconciliation:** when feature/monetization or UI/UX materials arrive, register source/version/date, compare them with Track A, identify confirmations and contradictions, update assumptions, and revise the launch slice. Missing materials reduce confidence; they do not freeze Track A.

## Iterative sequencing

This is an iterative dependency map, not a waterfall gate. P01's `PROPOSED` launch slice bounds deeper work; later evidence may send the team back to revise it. Where evidence is sufficient, bounded P02 Domain Architecture and P04 UX Architecture work may progress partially in parallel and feed each other. P03 Technology Evaluation begins only for concrete decision problems exposed by product/domain/UX requirements, not technology curiosity. P05 still requires authorized legacy access. P06 reconciles scope and roadmap; P07 produces the mature presentation pack.

Phases may overlap only when inputs and confidence limits are explicit and contradictions are reconciled by the primary agent. Completion of P00/P00.1 does not authorize P01 automatically.
