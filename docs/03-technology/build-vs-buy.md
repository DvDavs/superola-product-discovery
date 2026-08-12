# Build vs Buy — Decided by P03

> **Status:** `PROPOSED — DAVID APPROVAL REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. **Scenario stamp: S-1.**
>
> The P00 framework classified capabilities as *likely build* or *strong buy* heuristically. P03 ran the comparison. **Two of the P00 heuristics were wrong for this product**, and both are recorded below with the reason.

## 1. The decisions

| Capability | P00 heuristic | **P03 decision** | Deciding axis |
|---|---|---|---|
| Marketplace listing, profile, taxonomy, geography rules, requests, quotes, ranking | build | **BUILD** | Differentiating; unchanged |
| Durable deferred work | *"cache/queues only where justified"* | **BUILD — a job table in the existing database** | **No new always-on component.** A broker would need the same outbox table **plus** a relay, so it is strictly additive at 0.096 jobs/second |
| Transactional email | strong buy | **BUY** | Deliverability apparatus and failure-signal fidelity |
| Object storage and delivery | strong buy | **BUY** | Zero-egress eliminates the dominant variable line |
| Media transformation | strong buy | **BUILD — pre-generated derivatives in the worker** | **Architecturally forced.** See §2.1 |
| Audio and video hosting | strong buy | **NEITHER — link out** | Cost, moderation labour, and rights |
| Place resolution and geocoding | strong buy | **HYBRID — build the governed `Place` list, buy geocoding for provider base addresses only** | See §2.2 |
| Map rendering | strong buy | **NEITHER — no map assumed in V1.** `ADR-019` Level 3: **decided by P04**, not here | A pin at a provider's exact location has no lawful public surface. **A coarse or area-level surface does, and P04 may want one** — at which point this row is re-costed |
| **Authentication** | **strong buy** | **BUILD** | **See §2.3. The P00 heuristic was wrong here** |
| Marketplace authorization and business membership | build | **BUILD — and never outsourced** | `ADR-011`; the domain decides |
| Technical monitoring | *"analytics infrastructure"* as buy | **BUY, at the smallest free tier** | Bought small; product measurement is built |
| Product funnel measurement | implied buy | **BUILD — durable domain state, queried** | `D-11`'s named trap |
| Operator tooling | *"do not build a generic admin suite"* | **BUILD — crude but audited** | ~19–27 developer-days. Not optional |
| Search relevance | build | **BUILD — staged, in the database** | No dedicated engine without a measured trigger |
| Natural-language intent | *"AI models"* as buy | **NEITHER — do not spend** | `ai-evaluation.md` |
| CAPTCHA / bot protection | strong buy | **NEITHER while the crawler gate is closed** | Its allowlist is a second crawler policy (`R-046`) |
| Secrets management | not classified | **NEITHER — platform-native** | A dedicated store adds a boot-path dependency and a lockout risk |
| Payment and subscription billing | strong buy | **NOT EVALUATED — gated** | `G-02`, `D-15` |

## 2. The three decisions that reversed a P00 heuristic

### 2.1 Media transformation — buy became build, because the architecture forced it

`D-06` requires **"a small fixed derivative set"** *and* **"a processing state so no half-processed media is publicly visible."** A derivative generated on first request has no state to gate publication on. **These two requirements are logically incompatible with on-the-fly transformation** — so the entire managed-transformation category is *architecturally excluded*, not merely outbid.

**P02 made this decision without noticing it made it.** It is recorded explicitly here because otherwise a transformation service gets sold in later on a cost comparison it was never eligible for. Worth ~$177/month at Growth if adopted anyway.

### 2.2 Geocoding — the hybrid nobody had specified, which is P02's own design

The P00 heuristic said buy. **The correct answer is buy for one narrow use and build for the other, and the build half was already specified in `domain-model.md` §1.4** — a governed `Place` with a stable internal identifier, a variable-depth parent chain, localized names and synonyms, where *"provider-typed place text is a resolution input, never a `Place`."*

Customer-side location resolves against Superola's own governed list at **zero vendor cost**. Only provider base addresses are geocoded, and there are two orders of magnitude fewer of those.

**Cost consequence: ~70× against the same vendor, ~132× against the disqualified platform at Growth.** And the reversal that makes it work — the geocoder's *permanent-storage* tier, the expensive one everybody avoids, becomes the cheapest option in the field once the `Place` list absorbs customer-side volume. **The volume that made "permanent" expensive was never the volume that needed storing.**

**There is also a coherence argument, not only a cost one.** `Market` is a governed (Category × Place) pair. If Markets are governed and enumerable, the customer-side location vocabulary is **already constrained to a governed list by construction** — so free-text customer geocoding is not merely expensive, it is **incoherent with the `Market` concept**.

### 2.3 Authentication — buy became build, and the P00 heuristic was wrong

This is the clearest reversal, and it is worth stating why the heuristic failed.

**The heuristic assumes buying relieves cost at scale. Here it does not:** at 300 / 3,000 / 25,000 monthly active users, two leading vendors are **$0 / $0 / $0** and a third is $25 flat. **The ordinary case for buying is simply absent**, so the decision falls to architecture fit and exit — where the buy side fails four independent tests:

1. **A cliff already scheduled.** One vendor's free tier ends at exactly **25,000 monthly active users — the Growth target.**
2. **Commercial pressure running opposite to the architecture.** `ADR-011` places authorization in the domain. **One vendor prices the forbidden model at $100/month** — so a future engineer under deadline pressure sees a solved problem rather than a boundary violation.
3. **The `VerificationFact` mismatch.** §2 requires typed, expiring, revocable facts because *"verification is never a boolean on a Business."* Every vendor ships a boolean. **You reimplement the vendor's flagship feature on day one, and the vendor's copy is the one that cannot be revoked.**
4. **Revocation.** §9 requires operators to suspend an actor. **A safety suspension that takes effect "within 15 minutes" is not a suspension.**

**The asymmetry P03 said decides it — corrected by P03.1.** BUILD→BUY is documented and supported: vendors publish bulk import accepting hashed passwords. **BUY→BUILD was verified on 2026-08-12 and is vendor-specific, not uniformly hard:** self-service for **Supabase Auth** (bcrypt in a customer-owned table) and **Clerk** (dashboard CSV including hashed passwords); **gated behind a support case for Auth0**; **no documented mechanism for Cognito** — recorded as *no path found*, never as *proven impossible*. **The blanket claim is withdrawn, `ADR-017` is on `HOLD`, and the recommendation below is a working recommendation rather than a settled decision.** Evidence: `docs/07-research/authentication-vendor-verification.md`.

If no export exists, migrating 25,000 accounts means forcing every user to reset — which, against `R-022`, would be **the largest deliberate abandonment event in the product's life.** The mitigation is not a vendor choice; it is owning the credential.

**Honest cost, stated as an assumption: 8–12 engineering days initially, ~2 days/year maintenance.** Real money and real risk. **The argument is not that BUILD is free — it is that BUILD is the reversible option.**

## 3. The rule this evaluation kept running into

Three separate capabilities were rejected for the same structural reason, and it is worth naming as a rule rather than rediscovering it each time:

> **Do not buy a product whose core feature the architecture forbids you to use.**

- A vendor's email **templates** are unusable, because `ADR-010` requires the body rendered only after a disclosure check the vendor cannot see.
- A vendor's **organizations and roles** are unusable, because `ADR-011` decides authorization in the domain against resource state the vendor cannot see.
- A vendor's **verified-bot allowlist** is unusable, because `ADR-012` requires one governed enforcement point the vendor does not defer to.

In each case the vendor's differentiation is exactly the part Superola must own. **What is left to buy is the commodity underneath** — an SMTP pipe with a good event stream, a credential store, a delivery network. **Buy the commodity; never buy the part that carries domain meaning.**

## 4. Total cost of ownership, not the lowest bill

`AGENTS.md` requires build-versus-buy and total-cost analysis. The headline from `cost-model.md`:

| | Pilot | Early Marketplace | Growth |
|---|---|---|---|
| **Cash bill** | $75.98 | $192.99 | $560.79 |
| **Human cost** | $578 | $2,592 | $14,298 |
| **Human share** | **88%** | **93%** | **96%** |

**Low monthly hosting cost is not low total cost of ownership** — the P00 framework said so, and the numbers now show by how much. Two demonstrations:

- **Self-managed infrastructure** saves $36–$133/month and costs 6.5–15 operator hours/month. **Break-even is $4–$9/hour.** A cheaper bill is a worse total at every modelled volume.
- **Media** costs $3.53/month in infrastructure at Growth and **$190/month in human moderation** — roughly 54× — and that ratio holds only *because* audio and video are linked rather than hosted. Hosting them would add ~$429/month of infrastructure **and** roughly triple the moderation line. `D-06` is filed as a technology decision and is substantially an operator-capacity decision.

## 5. Required decision record — retained from P00, now with the answers

Every material capability above records, in `technology-evaluation.md`: differentiation and required control; product fit; implementation time and opportunity cost; fixed, variable and scale-dependent pricing **with the unit named**; operating, reliability, security and support burden; data access, portability, terms risk, lock-in and exit cost; integration failure modes and degradation behaviour; and a **measurable** reconsideration trigger with the V1 instrumentation that makes it observable.

**Two additions P03 recommends adding to that list permanently:**

1. **Vendor data retention of anything Superola sends it.** §12 classifies a notification body as *"permanently exfiltrated once sent"* — and the body carries a non-guessable link into the authenticated surface. **P02 asks what may appear in a body and never asks how long a vendor keeps it.** One evaluated candidate's verified 30-day retention was disqualifying on that axis alone.
2. **Whether the vendor's terms permit persisting what the domain must persist.** Three geocoding candidates were **disqualified on terms, not price** — one of them because its only permitted persistence mechanism is the vendor identifier the architecture forbids as `Place` identity. **A terms clause eliminated more candidates in this evaluation than cost did.**

## 6. Sources

`docs/03-technology/technology-evaluation.md`, `cost-model.md`, `infrastructure-options.md`, `technology-radar.md`, `ai-evaluation.md` · `docs/02-architecture/p03-decision-inputs.md`, `integration-architecture.md`, `security-privacy-architecture.md`, `adr/ADR-010`, `ADR-011`, `ADR-012` · `docs/05-roadmap/mvp-scope.md` · `AGENTS.md`. External claims accessed **2026-08-11**; **the canonical per-line source register is [`cost-model.md` §12](cost-model.md).**
