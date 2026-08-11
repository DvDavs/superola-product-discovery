# Source Register

| Source ID | Provenance | Class | Source | Availability | Evidence treatment | Notes / next action |
|---|---|---|---|---|---|---|
| SRC-001 | `OWNER_INTERVIEW` | Sanitized extracted source representation | [Superola owner interview evidence](sources/owner-interview-superola-2026-08-11.md) | AVAILABLE IN REPOSITORY | `CONFIRMED` means owner-attributed in the supplied P00 representation; unaudited facts remain qualified | No verbatim transcript/audio was supplied. Use the ledger limitations and entry IDs when verifying canonical owner claims. |
| SRC-002 | `LIVE_PLATFORM` | Existing product | [superola.com](https://superola.com) | AVAILABLE, NOT AUDITED IN P00/P00.1 | Future authorized direct findings may be `OBSERVED`; current implementation claims remain preliminary until verified | Schedule a dedicated, bounded audit rather than inferring future requirements. |
| SRC-003 | `OWNER_MATERIAL` | Primary, pending | Owner's full feature/monetization document | NOT RECEIVED | No document claims may be inferred | Register version/date and reconcile in P01 Track B when received. |
| SRC-004 | `OWNER_MATERIAL` | Primary, pending | Owner's existing UI/UX materials | NOT RECEIVED | No design claims may be inferred | Register version/date and reconcile in P01 Track B/P04 when received. |
| SRC-005 | `PROJECT_DATA` | Authoritative project data, pending | Legacy database/schema/export | NOT RECEIVED | Provider, user, category, activity, and migration claims remain unaudited | Require authorized access, privacy controls, and a scoped P05 audit plan. |
| SRC-006 | `PROJECT_DATA` | Authoritative project data, pending | Analytics/traffic/SEO data | NOT RECEIVED | Traffic, geography, acquisition, and activity claims remain unverified | Request source systems, date ranges, definitions, and authorized access. |
| SRC-007 | `OWNER_MATERIAL` | Business input, pending | Validated financial/pricing assumptions | NOT VALIDATED | Prices and revenue examples remain hypotheses | Define pricing and unit-economics research ownership. |
| SRC-008 | `EXTERNAL_PRIMARY` | Official product documentation | [OpenAI Codex manual](https://developers.openai.com/codex/codex-manual.md) | CONSULTED 2026-08-11; helper reported local manual current | Authoritative only for P00 Codex custom-agent/config syntax | Not a Superola product-requirement source. |
| SRC-009 | `EXTERNAL_SECONDARY` | Referenced analysis, pending | Unidentified prior external analysis cited by the P00 brief | NOT PROVIDED | Supports only an `ASSUMPTION` until obtained, assessed, and independently verified | Obtain author, date, artifact/location, and methodology; remove the implementation claim if provenance cannot be recovered. |
| SRC-010 | `DAVID_DIRECTIVE` | Composite project brief | P00 workspace bootstrap brief supplied 2026-08-11 | ORIGINAL ATTACHMENT SESSION-LOCAL; CANONICAL DIRECTIVES IN REPOSITORY | Supports P00 scope, workflow, evidence, architecture-guardrail, and presentation directives; it is not itself proof of owner intent | `pasted-text.txt`, 38,988 bytes, 1,641 lines, SHA-256 `1B5139AFF6342E19D0EA1028F6965427827119C8CDD9CC345F810A23EEF07DF3`. Owner-attributed content is durably extracted in SRC-001. |
| SRC-011 | `DAVID_DIRECTIVE` | Workflow hardening brief | P00.1 evidence provenance and workflow hardening brief supplied 2026-08-11 | ORIGINAL ATTACHMENT SESSION-LOCAL; CANONICAL PLAN IN REPOSITORY | Supports P00.1 workflow changes only | `pasted-text.txt`, 8,047 bytes, 313 lines, SHA-256 `CD330AA0D669381BAC403FB4BFBEAD6C35B735CFCDEB1A628F0C0E34E46BA8C1`; canonical plan: `plans/P00.1-hardening.md`. |

## Source quality rules

- Record source owner, version/date, access date, and location when new material arrives.
- Prefer authoritative project data for current-system facts and primary/official sources for technology research.
- Record provenance separately from evidence status; neither field substitutes for the other.
- A source can support a claim without converting an owner idea into an approved requirement.
- Do not add private owner materials to a public repository or copy unnecessary PII into this register.
