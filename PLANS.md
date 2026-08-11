# Execution Plans

Plans are living coordination documents for substantial phases. They make work recoverable by humans, fresh Codex sessions, and external reviewers without depending on chat history.

## Required plan sections

Each plan under `plans/` must include:

1. ID, title, owner, status, and last updated date.
2. Objective and non-goals.
3. Context and authoritative inputs.
4. Known constraints and evidence labels.
5. Tasks and delegation strategy.
6. Decisions made or ADRs required.
7. Open questions and approval gates.
8. Validation and acceptance criteria.
9. Deliverables.
10. Handoff and recommended next step.

## Statuses

Use `NOT STARTED`, `IN PROGRESS`, `BLOCKED`, or `COMPLETED`. A completed phase must preserve unresolved questions rather than hiding them.

## Change control

- Update the plan when scope, sequencing, evidence, or validation changes.
- Link material decisions to ADRs; do not bury them in task lists.
- Mark claims with the repository evidence labels.
- Subagent work is advisory until the primary agent integrates it.
- Treat phase dependencies as evidence gates, not an automatic waterfall; bounded phases may overlap when their inputs and confidence limits are explicit.
- Do not begin the next phase merely because the current plan is complete; follow the explicit approval gate.
