# Plan Index

| ID | Phase | Status | Purpose | Approval gate |
|---|---|---|---|---|
| P00 | Bootstrap | COMPLETED | Establish repository, evidence, planning, decision, and multi-agent conventions. | Handoff complete; P01 still requires explicit authorization. |
| P00.1 | Evidence & Workflow Hardening | COMPLETED | Separate evidence/provenance, persist owner evidence, and harden P01/P07 workflow before the baseline commit. | Validation passed; baseline committed. |
| P01 | Product Discovery | COMPLETED | Defined actor outcomes, category variability, journeys, trust/monetization gates, success signals, benchmark/AI implications, and a Marketplace Leads launch hypothesis. | Specialist and final critical re-reviews passed; recommendation remains `PROPOSED — OWNER VALIDATION REQUIRED` pending owner reconciliation. |
| P01.1 | Owner Reconciliation Prep | COMPLETED | Prepared the owner reconciliation matrix, intake process, P02/P04 release gate, material checklist, and bilingual owner-meeting guide. | Meeting and material reconciliation still pending; the release gate is defined but not satisfied. |
| P02 | Domain Architecture | NOT STARTED | Convert validated product needs into domain boundaries and architecture alternatives. | Owner reconciliation and explicit authorization confirm a bounded decision problem; P02 may then iterate alongside P04. |
| P03 | Technology Evaluation | NOT STARTED | Compare technology and managed-service options against validated needs and costs. | Product/domain/UX work exposes a concrete decision problem and evaluation criteria. |
| P04 | UX Architecture | NOT STARTED | Define structural customer, vendor, admin, and monetization journeys. | Product flows and open questions are prioritized; P04 may iterate alongside P02. |
| P05 | Legacy Audit & Migration | NOT STARTED | Audit the legacy system/data and classify migration viability. | Authorized access and data-handling controls exist. |
| P06 | MVP & Roadmap | NOT STARTED | Reconcile and seek approval for staged scope, dependencies, uncertainty, team needs, and delivery roadmap. | The P01 launch-slice hypothesis and relevant architecture, UX, migration, cost, and team evidence are adequate. |
| P07 | Presentation Pack | NOT STARTED | Produce bilingual owner-facing recommendations and decision material. | Recommendations have passed evidence and critical review. |

Create a dedicated plan file only when its phase is explicitly started. P01 is complete on current evidence in `plans/P01-product-discovery.md`; pending owner materials trigger reconciliation, not silent approval or an automatic later phase.

P01.1 (`plans/P01.1-owner-reconciliation-prep.md`) prepared that reconciliation. The readiness test for starting P02/P04 lives in `docs/01-product/owner-reconciliation-matrix.md`; it is a readiness test, not an authorization.
