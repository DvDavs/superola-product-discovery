# P00 — Workspace Bootstrap

## Metadata

- Owner: David (final human technical decision-maker)
- Status: COMPLETED
- Started: 2026-08-11
- Last updated: 2026-08-11

## Objective

Create a durable repository-based source of truth and a rigorous human + AI operating system for later Superola product discovery, architecture, research, UX, migration, roadmap, and presentation work.

## Non-goals

- Building production application code.
- Auditing the live website or legacy database.
- Selecting a final architecture, cloud, framework, datastore, payment provider, map provider, search engine, or AI provider.
- Executing P01 or producing owner-facing deliverables.

## Inputs

- `CONFIRMED`; provenance `DAVID_DIRECTIVE`: The P00 brief was supplied by David; P00.1 later separated its owner evidence and project directives into distinct canonical sources.
- `OBSERVED`; provenance `PROJECT_DATA`: The repository was an empty, locally initialized Git repository with no commits or remotes at inspection time.
- `OBSERVED`; provenance `EXTERNAL_PRIMARY`: The current Codex manual was consulted for project custom-agent and concurrency configuration syntax.
- `CONFIRMED`; provenance `OWNER_INTERVIEW`: The owner reports that a feature/monetization document and UI/UX materials exist. Availability: NOT RECEIVED.
- `ASSUMPTION`: Authorized legacy data/schema/export and analytics can be made available. Availability: NOT RECEIVED.
- `ASSUMPTION`: Validated financial/pricing inputs can be produced. Availability: NOT VALIDATED.

## Constraints

- Repository state must remain understandable without chat history.
- Evidence labels must prevent assumptions and proposals from becoming facts.
- Internal canonical and presentation-support documents are English only; only artifacts intended for direct owner presentation require Spanish + English.
- The repository must remain safe for private technical collaboration and contain no secrets or real private customer data.
- Subagents are advisory; the primary agent owns canonical writes.

## Tasks

- [x] Inspect Git, existing files, Codex availability, and local constraints.
- [x] Normalize the canonical branch name to `main`.
- [x] Create root guidance, planning conventions, and repository hygiene.
- [x] Configure conservative project-scoped specialist agents without model pinning.
- [x] Create context, evidence, source, assumption, glossary, and open-question registers.
- [x] Scaffold later product, architecture, technology, UX, roadmap, migration, research, diagram, prototype, and presentation work.
- [x] Define a lightweight ADR format.
- [x] Validate structure, TOML syntax, evidence usage, scope boundaries, privacy, and Git diff/status limitations.
- [x] Complete P00 handoff and mark this plan completed.

## Delegation strategy

P00 canonical writing remains with the primary agent because the repository starts empty and the artifacts are tightly coupled. The configured specialists are intended for independent read-heavy work and adversarial review in later phases. No parallel canonical edits are permitted.

## Workflow decisions

1. Git branch `main` is canonical.
2. Repository files, not chat history, are the project source of truth.
3. The six-label evidence model is mandatory.
4. Subagents are read-only and advisory by default; the primary agent integrates their output.
5. Plans and ADRs carry phase state and material decisions respectively.
6. Internal canonical and presentation-support docs use English only; direct owner-facing artifacts and presentation diagrams must support Spanish and English.
7. Project agent concurrency is capped at four spawned agents; model selection inherits the current environment.

These are repository/workflow decisions only. No product architecture or technology candidate has been accepted.

## Open questions

- Engram persistent-memory tools were not exposed in the active session; repository persistence is available and authoritative, while external memory integration remains unverified.
- P01 Track A can proceed from current evidence after explicit authorization; Track B depends on receiving and reconciling pending owner materials.

## Validation and acceptance criteria

- Expected files and directories exist and contain either canonical P00 content or explicit not-yet-executed placeholders.
- All `.toml` agent/config files parse successfully.
- `AGENTS.md` is concise and points to deep context rather than duplicating it.
- Evidence labels are consistent and technology candidates remain unaccepted.
- No production code, secrets, credentials, access tokens, or real customer PII exist.
- Git diff/status is reviewed and the absence or presence of a remote is reported.

## Validation record — 2026-08-11

- Inventory review found 64 trackable workspace files across every required P00 path; later-phase files are explicit hypotheses, indexes, or NOT STARTED placeholders.
- Python `tomllib` parsed `.codex/config.toml` and all six custom-agent files; required agent fields are present. The active Codex session exposed all six project roles, providing a runtime load check.
- The current OpenAI Codex manual confirmed the standalone project-agent schema and `agents.max_concurrent_threads_per_session`; `codex.exe --version` could not run in this Windows session because the executable returned access denied.
- Evidence scan found no hybrid or unsupported evidence labels after review corrections. Owner-reported metrics, launch geography, booking/payment scope, and external-profile viability remain explicitly unverified.
- Architecture, product, cost, and adversarial reviewers found no accepted production stack, fake ADR, production code, or accidental conflation of messaging/notifications or organic/sponsored ranking.
- UTF-8, final-newline, trailing-whitespace, local Markdown-link, production-code-extension, and high-confidence secret-pattern checks passed.
- Sensitive legacy-data safety nets were added to `.gitignore`; repository guidance still requires production-derived data to remain outside the repository.
- Git inspection confirmed branch `main`, no commits, no remote, and all P00 files untracked. `git diff`/`git diff --check` are empty because no tracked baseline exists, so full-file inventory and content checks were used for the untracked bootstrap.

## Deliverables

- Root repository guidance and plan system.
- Project-scoped Codex configuration and six specialist agents.
- Canonical P00 context, evidence, sources, assumptions, and glossary.
- Architecture principles, ADR template, later-phase indexes, and explicit placeholders.
- Final P00 handoff to David.

## Handoff

P00 is complete. Recommend P01 Product Discovery next: use current evidence to resolve the highest-value scope questions and produce a `PROPOSED` launch-slice/MVP hypothesis, then reconcile pending owner materials when they arrive. Do not start P01 without David's explicit request.
