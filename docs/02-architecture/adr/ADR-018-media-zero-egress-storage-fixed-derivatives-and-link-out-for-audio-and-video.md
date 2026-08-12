# ADR-018 — Media: zero-egress object storage, pre-generated fixed derivatives, and link-out for audio and video

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/03-technology/technology-evaluation.md` §4.6; `cost-model.md` §4; `p03-decision-inputs.md` `D-06`; `domain-model.md` §1.3; `ADR-012`; `security-privacy-architecture.md` §6, §12; `R-011`, `R-042`, `R-043`, `R-048`; `SRC-013`
- Supersedes / superseded by: N/A

## Problem / context

`D-06` requires object storage with a rights acknowledgement per asset, **a small fixed derivative set**, **a processing state so no half-processed media is publicly visible**, deletion reaching derivatives and caches, upload scanning, and hard per-provider count and per-file size caps.

`D-06` also states the cost shape plainly: **"served bytes per anonymous public view is the dominant variable line — not stored bytes. It is the one cost that grows with acquisition success rather than with revenue-bearing requests."** And it asks P03 to evaluate an alternative the repository had never named: **for performers, where audio and video are category-central, link out to provider-hosted media rather than hosting it.**

## Constraints

Public pages are image-heavy by design — that is what the acquisition hypothesis requires. One part-time operator moderates uploads. Media is an input to no marketplace decision: not to eligibility, not to ranking, not to the request loop. Precise provider base location is **provider-private** and must never reach a projection (§12).

## Options considered

### Option A — Zero-egress object storage, derivatives pre-generated at upload in the worker, audio and video linked out (recommended)

Originals and a small fixed derivative set in one bucket. The worker generates derivatives as deferred work (`ADR-015`) and transitions the processing state. Performer audio and video are embedded from the provider's own hosting.

### Option B — Metered-egress object storage plus a delivery network

Viable and cheap in absolute terms. Rejection factor: it retains a per-GB line on the cost that grows with acquisition success, and one candidate's regional rate varies 4.5× with **the relevant region's classification unverified** — an unverified multiplier on the dominant variable line.

### Option C — A managed media platform with on-the-fly transformation

**Rejected as architecturally ineligible, not outbid.** `D-06` requires *"a small fixed derivative set"* **and** *"a processing state so no half-processed media is publicly visible."* **A derivative generated on first request has no state to gate publication on.** The two requirements are logically incompatible with on-the-fly transformation. **P02 made this decision without noticing it made it**, and it is recorded explicitly here so a transformation service is not later sold in on a cost comparison it was never eligible for. Worth ~$177/month at Growth if adopted anyway.

### Option D — Hosting audio and video

Rejection factors in the Decision below.

## Decision

**Option A**, with five rules:

1. **Zero-egress object storage** for originals and derivatives.
2. **Derivatives pre-generated at upload** in the worker process, with an explicit processing state.
3. **Audio and video are linked out, not hosted.** Images stay hosted.
4. **Strip all metadata during the derivative re-encode** (`R-048`).
5. **Publication gates on the database fact that media exists, never on the vendor fact that it is currently servable** (`R-042`).

**Approval still required:** David.

## Rationale

**On storage: the right response to a dominant variable line is to eliminate it, not optimise it.** Verified zero egress makes the Growth media line $3.53/month against $50 on a cheap-egress origin and **$375/month on a $0.15/GB host — more than all compute combined.** It is also the best exit story in the whole evaluation: the same API in and out, and **you are not charged to walk out.**

**On link-out, four arguments, and the fourth is new to this repository:**

1. **Cost.** Hosted video at Growth ≈ **$429/month** — 45–140× the entire image stack — and the delivery line scales with anonymous views, which is exactly the wrong scaling direction under `D-06`'s own framing.
2. **Operator cost, which is larger than the infrastructure cost.** Per-asset human moderation: images ≈ $0.03, hosted video ≈ $0.45 — **15×, because a person must watch or listen in real time.** 600 videos per month cost more operator time than 8,000 images. `D-06` calls this *"an operator cost masquerading as a storage cost"*; the arithmetic confirms it. Linked media drops to ~$0.15 per asset, because the host platform has already adjudicated legality and Superola is judging **suitability**.
3. **Rights, and no P02 document names this.** When Superola hosts, **Superola makes the copy and publishes it**, and `MediaRightsDeclaration` is the only thing between Superola and a claim. **For mariachis, norteño groups, bands and DJs — the exact owner-reported categories — the performance may be the provider's but the composition almost never is.** Hosting imports music-licensing exposure that appears nowhere in the cost model. Embedding leaves the copy where its own rights adjudication already ran. **Link-out materially strengthens the rights position, in precisely the category where audio and video are central** (`R-043`).
4. **The failure modes are asymmetric.** Link rot is real and needs a sweep — but a freshness sweep **must exist regardless**, because request intake decays on a governed window, so a dead-media check is marginal. And a dead link degrades **one profile**; hosting's failure mode is **a bill that grows with your own success.**

**Images stay hosted** because they are verifiably ~$3/month, because presentation consistency on the primary discovery surface is the product, because no third-party image host with an embed contract exists, and because the fixed derivative set and processing state require Superola to own that pipeline.

**On metadata stripping:** provider photographs routinely carry precise GPS in their metadata. §12 classifies precise provider base location as provider-private, *"never emitted to a projection, a search result, a notification, or analytics."* **A published photograph with intact metadata emits it to the projection.** No P02 document names this path. **The fix is one flag on a re-encode that already has to happen — and it is free only because rule 2 forces that re-encode.** Under an on-the-fly model it would be a separate control.

## Consequences

Positive: the dominant variable line is eliminated; the largest per-asset operator cost is removed with the media that caused it; a rights exposure is avoided rather than mitigated; exit is free.

Negative: dead links on performer profiles, needing a sweep and an operator queue; loss of presentation control and availability for embedded media; and a dependency on third-party embed terms. **One evaluated platform's embed permission was verified as compatible; others were not checked, and one changed its embed endpoint to require a token — that must be confirmed before it is promised to anyone.**

Operational: image moderation remains the recurring line — roughly $240/month at Growth — and it is **25–170× the infrastructure bill.** `D-06` is filed as a technology decision and is substantially an operator-capacity decision.

**One design trap named:** *"required media roles"* in the publication requirements must gate on the **database fact**, not on servability. *"This profile has media"* is a database fact; *"the delivery network can currently serve it"* is a vendor fact. **Conflating them means a delivery outage silently unpublishes supply** — a correctness failure caused by a media outage, which is precisely what this decision exists to prevent (`R-042`).

## Cost implications

Storage and delivery: **$0.00 → $0.29 → $3.53** per month across the three scenarios, with egress $0.00 at all three. Avoided: ~$429/month of video hosting at Growth, ~$177/month of on-the-fly transformation, and the difference between $3.53 and up to $375 in egress. Human moderation: ~$240/month at Growth for images; ~$90/month for linked video against ~$270 hosted. Full model in `cost-model.md`.

## Lock-in and exit implications

**LOW–MEDIUM, and lower than it first appears.** Storage is S3-compatible, so migration is a sync plus a delivery change, **and the egress to leave is free.**

The usual binding concern would be that public media URLs are embedded in the published projection — **but §6 already requires the projection to be rebuildable from source at any time.** A URL-scheme change is therefore **a projection rebuild, not a migration. The architecture already paid for this exit.**

**Unresolved and highest priority before adoption:** whether objects served through a custom domain on the chosen provider's entry plan fall under its developer-platform exemption or its content-delivery restriction on serving *"a disproportionate percentage of pictures"*. **Superola's public pages are image-heavy by design, so that clause is a live exposure, not a theoretical one**, and it must be confirmed before commitment.

## Security and privacy implications

Metadata stripping closes a live path by which provider-private location reaches the public projection (`R-048`). Content type is verified **by magic bytes, not by declared type or file extension**, and **the uploaded bytes are never served back** — the derivative set means they never need to be, which removes an entire attack class at zero marginal cost. Upload scanning runs in the worker; its detection rate on targeted content is modest, so the size and count caps plus the re-encode carry most of the value.

Deletion must reach originals, derivatives and the delivery cache as three steps, one of which is easy to forget. **And the cheapest moderation is the media you never host** — link-out removes the audio and video moderation surface entirely.

## Reconsideration trigger

**Switch to hosting audio and video only if BOTH hold:** measured link-rot above 8% of external media references per quarter, sustained across two consecutive quarters, **and** a measured relative gap greater than 15% in request conversion between performer profiles with live media and those with dead media. **Two conditions, because link rot without measured harm is not a reason to take on a line that scales with anonymous traffic.**

**Reconsider the storage provider** if: served bytes per anonymous public profile view exceeds 400 KB, twice the design assumption — which measures the actual dominant line rather than the bill; **or** the provider invokes its content-type restriction; **or** monthly media spend exceeds $75, at which point **audit cache hit ratio and derivative sizes before changing vendor**, because at these volumes a $75 bill means a design defect, not a pricing problem.

## Validation

Verified 2026-08-11 from official pricing pages and terms: storage and operation rates, the zero-egress clause, the content-delivery restriction clause, transformation billing models, and one video platform's per-minute encoding, storage and delivery rates. One major video platform's embed permission was verified as compatible with a marketplace profile page; **others are NOT VERIFIED.** Upload-scanning service prices are **NOT VERIFIED**, so the moderation cost above is human-only and is therefore a **floor, not a total.**

Required instrumentation: served bytes per anonymous public profile view; cache hit ratio on media; link-rot rate per quarter; request conversion segmented by live versus dead media; and a test asserting no derivative carries location metadata.
