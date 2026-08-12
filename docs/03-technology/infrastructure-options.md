# Infrastructure Options — P03

> **Status:** `PROPOSED — DAVID APPROVAL REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. **Scenario stamp: S-1.**
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). All capability and price claims accessed **2026-08-11** and must be re-verified before purchase.
>
> Vendor names are held in `technology-radar.md` with their movement status. This document records the **shape** of the decision, the eliminators, and the thresholds.

## 1. The requirements that actually eliminate candidates

Drawn from P02, not invented here. A candidate failing any one of these is out, regardless of price.

| # | Hard requirement | Source |
|---|---|---|
| H1 | Geographic predicate capability on the managed database | `D-02` |
| H2 | Automatic backups **plus point-in-time recovery**, plus bulk export | `mvp-scope.md` lock-in row; `D-01` |
| H3 | One deployable artifact **plus a separate worker process from the same artifact** | `ADR-001`, `D-10` |
| H4 | **Per-workload resource limits** expressible from that single artifact | `D-10` — **and this criterion is missing from `D-10`'s own stated list.** A host that cannot express it fails a stated `ADR-001` requirement |
| H5 | Zero-downtime deploy, health checks, fast cheap rollback, and a **pre-deploy migration step** | `D-10`, `R-047` |
| H6 | Private application-to-database networking, TLS, custom domains | `D-10`, `security-privacy-architecture.md` §2 |
| H7 | Operable by one part-time person | Cross-cutting *small-team operability* |
| H8 | No Kubernetes, no multiple deployable units, no multi-region | `D-10`; `internationalization-architecture.md` §5 |

## 2. The eliminator that was assumed, and was not one

The geographic-extension requirement was expected to eliminate hosting candidates. **It does not** — every managed PostgreSQL provider checked on 2026-08-11 offers it, in some cases with a published per-major-version support matrix.

**So the shortlist is decided on H2, price and version currency instead.** Two real eliminations did emerge, and neither was about geography:

- **A managed database that is explicitly *unmanaged*** — backups as a bolt-on and **no built-in point-in-time recovery** — fails H2 outright. Self-managing recovery is a specialist burden H7 forbids.
- **A provider whose geographic extension is enabled only at provisioning time** is a trap rather than an elimination: choosing "no" and needing it later means recreating the database.

A third candidate was eliminated on **architecture**, not capability: its value proposition is a generated API over the database, which contradicts `ADR-011`'s single application layer as the write path. **Adopting it and then refusing to use it is paying for lock-in you disabled.**

## 3. The four hosting models compared

| Model | What it buys | What it costs | Verdict |
|---|---|---|---|
| **Managed developer platform + managed PostgreSQL** | Backups and point-in-time recovery included; rolling deploys with health checks; pre-deploy job step; private networking; worker components from one artifact; no seat or plan fee; **no egress cliff** | Highest cash bill of the four | **RECOMMENDED** |
| **Self-managed VPS** (container runtime, self-run PostgreSQL, self-configured backup tooling, reverse proxy) | Lowest cash bill | **6.5–15 extra operator hours per month.** Break-even $4–$9/hour. At Growth it needs high-availability PostgreSQL with automatic failover — a **hiring decision**, not a technology decision | **RUNNER-UP.** Loses at every modelled volume |
| **Major cloud with managed services** | Capable; nobody disputes it can do this | The floor is not the compute — it is the **supporting cast**: load-balancer hours, network-gateway hours plus per-GB, public-address hours, log ingestion, and a support-plan minimum. **None of those prices was confirmed from an official page and none was estimated from memory** | **DEFERRED**, trigger in §7 |
| **Container orchestration** | Nothing, at one deployable plus one worker | A control plane, a node pool, an ingress controller, and a permanent specialist dependency | **REJECTED** by `D-10` until triggered |

### 3.1 The break-even table, because it is the whole argument

| | Cash saved by self-managing | Extra operator hours/month | **Break-even hourly rate** |
|---|---|---|---|
| Pilot | ~$50 | 6.5 | **~$7.72/h** |
| Early Marketplace | ~$36 | 9.0 | **~$4.03/h** |
| Growth | ~$133 | 15.0 | **~$8.87/h** |

**Self-managing is cheaper only if the operator's time is worth under about $8/hour.** At any rate above roughly $12/hour, at any modelled volume, it loses — by $437 to $992 per month.

## 4. Smallest production-safe footprint

| | Pilot | Early Marketplace | Growth |
|---|---|---|---|
| Web container | 1 vCPU / 2 GiB × 1 | × 2 — **for availability during deploy and single-instance failure, not for load** | 2 vCPU / 4 GiB × 2 |
| Worker (same artifact, separate process) | 1 GiB | 2 GiB | 2 GiB |
| Managed PostgreSQL | 1 GiB / 10 GB | 4 GiB / 25 GB | 8 GiB / 100 GB + standby |
| Non-production | one small web + one small database | same | slightly larger |
| **Peak application load this must serve** | ~0.1 req/s | ~1.5 req/s | **~9–13 req/s** |

**Sizing is driven by the memory floor and by headroom for vacuum, a base backup and a migration running concurrently — not by throughput.** At Growth the entire marketplace peaks under about ten requests per second. Sizing above this is the `R-031` failure.

**Runtime sensitivity:** on a non-JVM runtime the web container drops a tier and the worker halves — roughly $20/month at Pilot, about 25% of the Pilot cash bill, and immaterial by Growth. **The JVM's cost is front-loaded exactly where the budget is tightest**, and that is the honest statement of its downside.

**The database sizing lever is not the marketplace.** Analytics is **77–87% of database size at every scenario**; the marketplace itself never exceeds ~4.7 GB. At Growth, 16 GiB would be bought almost entirely for measurement queries scanning analytics — **with monthly partitioning and pre-aggregated rollups, a smaller instance suffices.** That is a design lever, not a purchase (`Q-034`).

## 5. What breaks first, in order

1. **Media egress, if served from the application origin.** 2,500 GB/month at Growth is $50 on a cheap-egress host and **$375 on a $0.15/GB host — more than all compute combined.** Zero-egress object storage makes it $3.53. **This single decision is worth more than the hosting-vendor choice.**
2. **The analytics event table.** ~96 million rows/year at Growth. Largest table by two orders of magnitude; it will dominate database size, backup duration, and — critically — **restore recovery time.** Partitioning must be decided **before launch**; retrofitting onto 96M rows is a migration.
3. **Database connection count.** Pool size × replicas × worker, against a low ceiling on small tiers. Size the pool explicitly; add a pooler only if measured.
4. **The runtime memory floor.** See §4.
5. **The eligibility-and-ranking query — which will not break.** At Growth the discovery working set is ~395 MB and fits in memory. `search-architecture-requirements.md` §11's conclusion is **confirmed by the volumes**, not merely asserted.
6. **Operator workload — the real constraint at every scenario.** `cost-model.md` §5.

## 6. Region, latency, and the gate it is downstream of

**Select the host now; select the region at the geography gate.** `OR-003` and `A-002` are OPEN. Under S-1 there is exactly **one** region, and `internationalization-architecture.md` §5 is binding: *"'United States plus Mexico' is a market decision, not a topology decision. Anyone who draws a second region on an architecture diagram has purchased infrastructure with a marketing statement."* Naming a region now would settle a product question by infrastructure side effect.

**Latency figures below are arithmetic derivations from distance and the speed of light in fibre, clearly labelled — measured figures could not be verified and were not invented.**

| Route | Theoretical round-trip floor | Realistic at an assumed 2–2.5× routing multiplier |
|---|---|---|
| Mexico City → US-South | ~14 ms | **~30–40 ms** |
| Mexico City → US-East | ~30 ms | **~60–75 ms** |
| Mexico City → US-West | ~37 ms | **~75–95 ms** |

Guadalajara and Monterrey are closer to Texas than Mexico City is, so **a US-South region is the latency-optimal single choice if Mexico is in scope; US-East is the reasonable middle if it is not.**

**What a delivery network fixes, stated so the claim is falsifiable.** It fixes cacheable anonymous public reads — the acquisition surface — and it **terminates the TLS handshake at a nearby point of presence, saving roughly two round trips on every new connection even on a cache miss.** It does **not** fix authenticated reads, location-predicated searches (uncacheable by construction), or any write.

So it closes the gap for 90%+ of request volume and 100% of the acquisition surface, and not at all for the marketplace loop. **At 60–75 ms that residual is acceptable for an asynchronous request product where the fastest human response is measured in hours. It would not be acceptable for a realtime product — and P02 has already established that none is required.** The two conclusions support each other.

**Instrumentation required in V1 or §5's trigger is decoration:** latency by client geography, and cache hit ratio on public reads.

## 7. Staged evolution and the trigger for each step

**Pilot → Early Marketplace**, any one, measured: sustained origin CPU above 60% for seven consecutive days; p95 on request-submit or the eligibility query above the approved budget **after** indexing and tuning; operator queue backlog age above the approved bound.

**Early → Growth**, any one, measured: database working set exceeding memory, as sustained cache-hit ratio below 99% for seven days; restore-rehearsal recovery time exceeding the approved objective; an availability requirement beyond a single database node.

**To a major cloud**, any one: a managed service exists there that is genuinely required and unavailable elsewhere; a compliance or residency obligation names a region only available there; or the platform bill exceeds the equivalent cloud bill **including** its supporting-cast lines, verified rather than assumed.

**To container orchestration:** ≥3 independently deployed units with genuinely divergent scaling profiles **and** ≥2 teams blocked on one release train. **Both**, not either.

**The Growth system is deliberately not designed.** Everything above it is unknowable without traffic evidence.

## 8. Backups, restore, and the failure mode nobody prices

**Point-in-time recovery from the host covers operator error and bad migrations — the common case.** An **independent off-platform backup** covers what the host cannot: **the host account being terminated, suspended over a billing dispute, or compromised.** In all three, the host's own backups are inside the blast radius. With one operator and no second account holder, **account loss is the most plausible catastrophic failure in this product, and host point-in-time recovery is worth exactly zero against it.**

Design: dump from the scheduled worker → **encrypt client-side** → upload to **a different vendor than the application host**, with append-only credentials where supported. **Priced at under $1/month at Pilot and ~$3/month at Growth — the cheapest insurance in the model and the item most likely to be skipped.**

**A backup encrypted with a key stored inside the account it protects against is not a backup.** The key lives in the offline sealed copy described in §9.

**Restore rehearsal as a scheduled task with a named cadence: quarterly at Pilot, monthly from Early Marketplace.** Each rehearsal restores the most recent off-platform encrypted dump into a scratch database, runs a schema-version check, runs assertion queries including **audit row count**, runs the audit-immutability test, and **records the wall-clock time to restore.** *That recorded time is the deliverable — not the fact that a backup exists.*

**The failure mode, named plainly: you do not discover the backup is unusable until the moment you need it, and at that moment the discovery is unrecoverable.** The specific, boring ways it is unusable *in this design*:

- **The dump was taken by a role lacking read access on the audit schema and silently excluded it** — a direct consequence of the grant separation in `D-11`, and exactly the kind of thing that bites.
- The encryption key was inside the terminated account.
- Extensions were not captured, so the restored database will not start the application.
- The dump succeeded but was truncated by a full disk and nobody checked the exit code.

Every one is caught by a rehearsal and by nothing else. Add a **dead-man's-switch ping on the nightly backup job**, because a backup job that fails silently is indistinguishable from one that never existed.

**And a restore rule that belongs in the runbook:** restoring to a point before a deletion **silently un-deletes it**. Any restore must be followed by re-application of the suppression list (`R-023`).

## 9. Secrets, environments, and infrastructure-as-code

**Secrets: platform-native environment variables plus the forge's encrypted pipeline secrets. $0.** A dedicated secret store adds a third party to the application's boot path and a single-operator lockout risk with no second human to recover. A self-hosted secret manager is strictly worse — an always-on component whose failure mode is *the application cannot start*. **Trigger:** short-lived dynamic database credentials become a requirement, or more than ~5 people need differentiated production-secret access.

**The control that actually matters, stated instead of a rotation policy:** no production secret ever exists anywhere a push can reach, enforced mechanically by secret scanning in the pipeline plus a pre-commit hook. At one operator, rotation is **manual and event-driven**; a scheduled rotation policy generates work and prevents nothing.

**The largest secrets risk is single-operator lockout, and no product fixes it.** Mitigation, roughly one hour, worth more than any product: an **offline sealed copy** of recovery material held physically outside every account that could be terminated — host account recovery codes, the backup encryption key, and **the domain registrar and DNS credentials, which are the highest-value item and the one people forget.**

**Environments: two and a half.** Production, local, and **one small non-production environment whose stated purpose is migration rehearsal and vendor sandbox credentials** — not a full-fidelity staging clone, because pipeline tests against a real database do functional testing better and faster than a shared environment.

**Hard rule: it is never seeded with production personal data.** `security-privacy-architecture.md` §7 and `AGENTS.md` both forbid it. A "staging copy of production" is the most common way a marketplace leaks customer-private data, and it would place every customer-private and sensitive class into an environment with weaker access control and no audit — **silently voiding the operator-access directive.**

**Ephemeral per-change environments: no at Pilot.** One person authors and reviews, so the environment reviews nothing — and the real cost is not compute but the **data seeder**, which is recurring developer-days, and an unseeded environment shows an empty search page. **Trigger:** a second reviewer, or a non-technical reviewer who must see a change without running it.

**Infrastructure-as-code: a committed platform-native declarative file. No general-purpose tool at Pilot.** It is free, lives in the repository, is reviewed alongside the code it describes, round-trips with the platform, and **has no state file**.

**The concrete threshold — a general tool pays when any TWO of these hold:**

1. More than one environment must be **provably** identical, and drift has already caused an incident — not "might".
2. Resources span **two or more providers** in a dependency order, so a from-scratch rebuild is a multi-hour manual sequence someone must remember correctly under pressure.
3. **More than one person** creates or modifies infrastructure.
4. Managed resource count exceeds roughly **20–30**, past which console review stops being a reliable audit.
5. A disaster-recovery requirement demands a **stated and tested** whole-environment rebuild time.

**At Pilot: one deployable, one worker, one database, one bucket, DNS ≈ 5–8 resources, one environment that matters, one person. Zero of the five hold.**

The carrying cost is not the tool — it is the **state file**, a new stateful artefact that must be stored, locked and backed up, and whose corruption is an outage-class event. **Introducing a component whose failure mode is *"you can no longer safely change infrastructure"* to manage eight resources is a strictly negative trade.** Reversal is low: eight resources can be imported in an afternoon if the threshold is later crossed.

## 10. Networking, and the enforcement point that must not be duplicated

TLS, certificates, custom domains and private networking are included on the recommended model at no separate charge. **There is no separate address or load-balancer line** — unlike the major clouds, where address-hours, balancer-hours and network-gateway-hours are three of the classic hidden lines.

**Volumetric denial-of-service protection at the delivery layer is fine — it makes no crawler-class decisions.**

**Heuristic bot mitigation is not (`R-046`).** Every managed bot product ships a vendor-owned allowlist of "verified good bots" — search and AI crawlers — permitted by default. **That allowlist is a second crawler policy, owned by the vendor and invisible to the governed record.** Enabling it **approves `Q-015` by accident, before `Q-027` has been settled**, violating the stated ordering — and §6 names crawler approval as irreversible on third-party surfaces.

**Required instead: one governed policy record; both `robots.txt` and the edge rules generated from it; neither hand-edited; and a deploy-time check that fails if the two renderings diverge.** If mitigation is ever needed alongside an approved crawler, the binding condition is that **its verified-bot allowlist is emptied and it defers to the application's own policy**, which remains the single enforcement point with a named owner.

**And record the honest limit (`R-035`):** RFC 9309 states that robots rules *"are not a form of access authorization"* and are *"not a substitute for valid content security measures."* Deny-by-default holds for the decision point; it is not enforceable against a determined non-cooperating client.

## 11. Sources

Capability and price claims accessed **2026-08-11**. **The canonical per-line source register — vendor, plan, price as read, URL and access date — is [`cost-model.md` §12](cost-model.md), together with an explicit `NOT VERIFIED` table.**

This document carries no inline URLs by design. Adversarial review found that deferring citations to work-package records **not present in the repository** made the cost model unfalsifiable and breached `AGENTS.md`'s requirement to persist authoritative primary sources. **One register, held in repository state rather than in a transcript.** RFC 9309 is cited there for the robots-protocol statement.

**Carried as gaps rather than filled from memory:** several managed-platform instance prices where the pricing page did not render to automated retrieval; database standby-node pricing; load-balancer pricing on the self-managed model; every major-cloud supporting-cast line; delivery-network cache-purge propagation time, which **no vendor publishes** — so P20's bounded propagation must be **measured in V1, never assumed**; and host datacenter and region lists, which **must be confirmed before provisioning.**

Repository inputs: `docs/02-architecture/p03-decision-inputs.md` (`D-04`, `D-10`, `D-11`) · `system-architecture.md` §2, §5, §8 · `internationalization-architecture.md` §5 · `security-privacy-architecture.md` §7, §9, §10 · `adr/ADR-001`, `ADR-011`, `ADR-012` · `docs/05-roadmap/mvp-scope.md` · `risks.md` (`R-031`, `R-035`, `R-046`, `R-047`) · `docs/03-technology/technology-evaluation.md`, `cost-model.md` · `SRC-013`.
