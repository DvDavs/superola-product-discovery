# ADR-016 — Managed platform, single region, no orchestration and no general-purpose infrastructure-as-code

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/03-technology/technology-evaluation.md` §4.10; `infrastructure-options.md`; `cost-model.md`; `p03-decision-inputs.md` `D-10`; `ADR-001`, `ADR-012`; `internationalization-architecture.md` §5; `security-privacy-architecture.md` §7, §10; `R-023`, `R-031`, `R-035`, `R-046`, `R-047`; `OR-003`, `A-002`; `SRC-013`
- Supersedes / superseded by: N/A

## Problem / context

`ADR-001` requires one deployable artifact plus out-of-band execution as a separate process from the **same** artifact, and explicitly rejects container orchestration, multiple deployable units and multi-region topology until triggered. `D-10` adds per-endpoint rate limits, request timeouts, upload caps, per-workload resource limits, kill switches, and a fast pipeline so rollback is cheap.

P03 must choose a hosting model without traffic evidence (`SRC-006` NOT RECEIVED) and without a resolved launch geography (`OR-003`, `A-002` OPEN).

## Constraints

One part-time person builds and operates the system. Modelled load peaks **under about ten requests per second at Growth**. Backups with point-in-time recovery, bulk export and deletion capability are hard requirements. Explicit cost discipline. `internationalization-architecture.md` §5 is binding: *"market scope is not topology."*

## Options considered

### Option A — Managed developer platform plus managed relational database, single region (recommended)

One container for the web process and one for the worker, both from the same artifact, with a pre-deploy migration step. Managed database with point-in-time recovery included. Delivery network in front of public reads. Object storage for media.

### Option B — Self-managed virtual private servers

Lowest cash bill, and the honest comparator. Rejection factor is arithmetic:

| | Cash saved | Extra operator hours/month | **Break-even hourly rate** |
|---|---|---|---|
| Pilot | ~$50 | 6.5 | **~$7.72/h** |
| Early Marketplace | ~$36 | 9.0 | **~$4.03/h** |
| Growth | ~$133 | 15.0 | **~$8.87/h** |

**Self-managing is cheaper only if the operator's time is worth under about $8/hour.** At any rate above roughly $12/hour, at every modelled volume, it loses by $437 to $992 per month. And at Growth it requires high-availability database failover, which under *small-team operability* is **a hiring decision, not a technology decision.**

### Option C — A major cloud with managed services

Not rejected on capability. Rejected on fixed floor plus operator surface for one person, where the floor is not the compute but the **supporting cast**: load-balancer hours, network-gateway hours plus per-GB, public-address hours, log ingestion, and a support-plan minimum. **None of those prices was confirmed from an official page, and none was estimated from memory** — so this option is **deferred with a trigger, not costed.**

### Option D — Container orchestration

Rejected by `D-10`. One deployable plus one worker does not need an orchestrator, and it would add a control plane, a node pool, an ingress controller, and a permanent specialist dependency.

## Decision

**Option A**, with six scoping rules:

1. **Select the host now; select the region at the geography gate.** `OR-003` and `A-002` are OPEN, and naming a region now would settle a product question by infrastructure side effect. Under S-1 there is exactly one region regardless. At Pilot volumes a region move is a maintenance window, not a re-platform.
2. **No general-purpose infrastructure-as-code.** A committed platform-native declarative file instead — free, reviewed with the code, and **without a state file.** The threshold at which a general tool pays is **any two** of: two environments that must be provably identical after a drift incident; resources spanning ≥2 providers in a dependency order; more than one person modifying infrastructure; more than ~20–30 managed resources; or a tested whole-environment rebuild requirement. **At Pilot, zero of the five hold.**
3. **Two and a half environments.** Production, local, and one **small** non-production environment for migration rehearsal and vendor sandbox credentials — not a full-fidelity clone. **Never seeded with production personal data.**
4. **An independent off-platform encrypted backup**, at a different vendor, with the key held outside every account that could be terminated.
5. **Expand/contract migration discipline** (`R-047`), because *"rollback is cheap"* is true for code and false for schema.
6. **No heuristic bot mitigation while the crawler gate is closed** (`R-046`).

**Approval still required:** David, plus a region decision at the geography gate.

## Rationale

At these volumes the managed platform buys, for $36–133 per month, the exact capabilities whose absence costs 6.5–15 operator hours: backups with point-in-time recovery, rolling deploys with health checks, a pre-deploy migration step, private networking, per-process resource limits from one artifact, and automatic certificate handling. **Human cost is 88–96% of total cost of ownership, so a cheaper bill that consumes hours is a worse total.**

On infrastructure-as-code, the carrying cost is not the tool but the **state file** — a new stateful artefact that must be stored, locked and backed up, and whose corruption is an outage-class event. **Introducing a component whose failure mode is "you can no longer safely change infrastructure" to manage eight resources is a strictly negative trade.** Reversal is low: eight resources import in an afternoon.

On the independent backup, the argument is not that hosts lose data. **It is that the host account can be terminated, suspended over a billing dispute, or compromised — and in all three the host's backups are inside the blast radius.** With one operator and no second account holder, account loss is the most plausible catastrophic failure in this product, and host recovery is worth exactly zero against it. **Under $1/month at Pilot: the cheapest insurance in the model and the item most likely to be skipped.**

## Consequences

Positive: no seat fee, no plan fee, no minimum commitment; no egress cliff; point-in-time recovery included; deployment safety native rather than bespoke; one investigation surface.

Negative: a higher cash bill than self-managing, and platform-shaped constraints on what can be configured. **Vertical headroom is finite** — the reconsideration triggers below name where it ends.

Operational: **restore rehearsal is a scheduled task with a named cadence** — quarterly at Pilot, monthly from Early Marketplace — and **the recorded wall-clock time to restore is the deliverable, not the fact that a backup exists.** The specific ways a backup is unusable in this design are all caught by a rehearsal and by nothing else: a dump taken by a role lacking read access on the audit schema and silently excluding it; a key inside the terminated account; extensions not captured; or a truncated dump whose exit code nobody checked. **And any restore must be followed by re-application of the suppression list, or `R-023` fires — restoring to a point before a deletion silently un-deletes it.**

## Cost implications

Fixed: ~$75 → ~$187 → ~$505 per month across the three scenarios. Variable: ~$1 → ~$6 → ~$56, dominated by email and geocoding, with **media egress at $0.00**. Human: ~3.25 → 5 → 9 hours per month of infrastructure operations. Full model in `cost-model.md`.

**The largest single cost decision in this ADR is not the host — it is putting media on zero-egress object storage**, which turns a $375/month line at Growth on a metered-egress host into $3.53.

## Lock-in and exit implications

**LOW today, and the window closes with data volume.** The application is a container; the database is stock; media is an S3-compatible bucket with **zero egress to leave.** At Pilot the whole platform is a dump, a sync and a DNS change inside one maintenance hour. Above roughly 100 GB the database move needs logical replication and becomes a project. **Exit is cheapest now and gets monotonically more expensive** — which is a reason not to over-think this choice, and a reason to keep every vendor behind an owned interface (P10).

## Security and privacy implications

Private application-to-database networking; automatic certificates; volumetric denial-of-service protection at the delivery layer, which makes no crawler-class decisions.

**Heuristic bot mitigation is refused while the crawler gate is closed** (`R-046`): every managed bot product ships a vendor-owned allowlist of "verified good bots", and **enabling it approves `Q-015` by accident, before `Q-027` has been settled** — violating the stated ordering, on a decision `security-privacy-architecture.md` §6 calls irreversible on third-party surfaces. Required instead: **one governed policy record, both the published directives and the edge rules generated from it, neither hand-edited, and a deploy-time check that fails on divergence.** And the honest limit (`R-035`): robots directives *"are not a form of access authorization"*, so deny-by-default holds for the decision point and is not enforceable against a determined client.

**The non-production environment is never seeded with production personal data** — it would place every customer-private class into an environment with weaker access control and no audit, silently voiding the operator-access directive.

Secrets: platform-native variables plus encrypted pipeline secrets, at $0. **The largest secrets risk is single-operator lockout, and no product fixes it** — an offline sealed copy of host recovery codes, the backup key, and the registrar and DNS credentials does.

## Reconsideration trigger

**Scale up within the platform:** measured p95 on request-submit or the eligibility query above budget on the **largest shared-CPU instance** after tuning, with profiling attributing it to CPU contention rather than the query — *then move to dedicated CPU on the same platform, not to another platform*; or database working set exceeding memory, as sustained cache-hit ratio below 99% for seven days; or platform incidents exceeding the approved availability budget across two consecutive months.

**To a major cloud**, any one: a genuinely required managed service exists only there; a compliance or residency obligation names a region only available there; or the platform bill exceeds the equivalent cloud bill **including** its supporting-cast lines, verified rather than assumed.

**To self-managed:** the managed bill exceeds $500/month **and** a person exists whose allocated role includes infrastructure operations **and** database high availability is either unnecessary or that person's demonstrated skill. **All three.** At Growth the first is met; the other two are not.

**To orchestration:** ≥3 independently deployed units with genuinely divergent scaling profiles **and** ≥2 teams blocked on one release train. **Both.**

**To infrastructure-as-code:** any two of the five conditions in `infrastructure-options.md` §9.

## Validation

Verified 2026-08-11 from official documentation and pricing pages: extension availability, point-in-time recovery windows, pre-deploy job support, worker components from a single artifact, egress rates and allowances, and delivery-network plan capabilities. Region and datacenter lists **must be confirmed before provisioning** — they were not verified.

Required instrumentation: latency by client geography and cache hit ratio on public reads (without which `internationalization-architecture.md` §5's trigger is decoration); database cache-hit ratio; recorded restore-rehearsal time; and a recorded time from *decide to roll back* to *previous version serving*.
