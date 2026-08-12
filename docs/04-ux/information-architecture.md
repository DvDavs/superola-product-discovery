# Information Architecture — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document defines a **conceptual hierarchy** — what surfaces exist, what contains what, what is reachable without an account, and what must be true for a node to deserve to exist. It **does not finalize URL implementation**: no route table, no slug format, no locale prefix strategy, and no sitemap is decided here. URL shape is stated as principle only, and every principle below is conditional on the separate public-distribution gate (`Q-015`), which human-public browsing does not satisfy. This document also does not decide navigation labels, visual hierarchy, component structure, or wording. It does not restate the journeys — see `docs/04-ux/customer-journey.md`, `docs/04-ux/request-intake.md`, `docs/04-ux/provider-workspace.md`, and `docs/04-ux/operator-surfaces.md`.

## 1. Public (unauthenticated) structure

Anonymous discovery is the acquisition hypothesis (`WA-05`). Nothing in this branch may sit behind a login wall.

```text
Home / need entry                                    `UX-01`
├── Search / discover — results                      `UX-04`
├── Category / archetype browse                      `UX-02`
│   └── Market — Category × Place                    `UX-03`
├── Provider profile (public)                        `UX-05`
│   └── Request this provider ─────────────┐
├── Request composer (anonymous)           │         `UX-07`
│   ├── Request review and send            │         `UX-08`
│   └── Identity and channel verification  │         `UX-09`  → TRANSITIONAL
├── Sign in / sign up                      │         `UX-16`
└── Informational / trust pages            │         `UX-06`
                                           │
   entry from `UX-03`, `UX-04`, `UX-05` ───┘  → composer carries prior context
```

### 1.1 What each public node is for and what justifies it

`R-011` is the governing risk: thin or duplicate public pages convert a discovery strategy into search-quality debt. Every node below therefore carries a **minimum substance condition**. A node that cannot meet it is **not generated** — an absent page is cheaper than a thin one.

| Node | ID | What it is for | What it must contain to justify existing | Anti-thin rule |
|---|---|---|---|---|
| Home / need entry | `UX-01` | Convert an undefined need into a governed `Category` plus a governed `Place` | The governed type-ahead (`I-1`), a small set of high-supply entry paths, and an honest statement of what the platform does | One page. **Never templated into per-keyword landing variants.** |
| Search / discover | `UX-04` | Deterministic eligible results for `Category` × `Place`, ranked, with a per-slot ranking explanation and `placementBasis` = `organic` | Result slots, ranking explanation, freshness indicator on `Stale`, `RequestIntake` surfaced, `undetermined` shown with its uncertainty, and the `ADR-005` non-claim (`V1 has no availability model`) | A query surface, **not** an indexable page inventory. Query-parameter permutations are **not** distinct public entities. |
| Category / archetype browse | `UX-02` | Explain what a governed `Category` covers and route into supply | The governed category definition and its localized labels/synonyms, its `CategoryArchetype`, what a request in it will ask for, and real routes into `UX-03`/`UX-04` | Exists **only for a governed `Category` that exists in the taxonomy.** A category with no governed attribute definitions has no page. |
| Market — Category × Place | `UX-03` | The people-first page for "this service, in this place" | Real eligible `Published`/`Stale` supply, a coarse `Place` label, a declared-coverage statement in words, differentiated locally-true content, and the `ADR-005` non-claim | **The hardest rule in this document.** A `Market` page is generated only where there is **real eligible supply and differentiated value for a person reading it**. **No mass generation of the Category × Place cross-product.** No zero-supply market page. No page whose only difference from another is a substituted place name. |
| Provider profile (public) | `UX-05` | The provider's own case, and the entry point to a deliberate request (`WA-02`) | Identity, `ServiceOffering`s with governed category attributes, narrative with explicit `contentLocale`, media, coarse location and declared coverage in words, publication freshness, `RequestIntake`, `verificationBasis` on every trust-flavoured fact, and the `ADR-005` non-claim | One canonical page per approved public entity. A `LegacyProviderRecord` is **structurally incapable** of appearing here (`ADR-009`). `Draft`, `PendingReview`, `Rejected`, `Closed` produce no public page. |
| Informational / trust pages | `UX-06` | Policy, safety, how the marketplace works, contact and correction paths | Substantive first-party text: what a request is, what a response is, what the platform does **not** promise, reporting and correction paths, privacy and consent notices as versioned content | Authored, not generated. **No per-market, per-category, or per-locale templated multiplication of policy text.** |
| Request composer (anonymous) | `UX-07` | Progressive, category-governed intake (§5.1 of `docs/04-ux/design-canon.md`) | Only the next question the selected `CategoryArchetype` requires, a named-stage indicator, and a local `RequestDraft` | A functional surface with no public entity identity. **Not a landing target.** |
| Request review and send | `UX-08` | Let the customer see and correct everything before it can reach a provider | The full assembled `EventContext` and category answers, the named recipient, and what will and will not be shared | Functional. No public entity identity. |
| Identity and channel verification | `UX-09` | Prove control of a contact channel before delivery (`DB-12`) | The channel, the proof step, and the guarantee that the request is already durably stored as `PendingVerification` | Transitional. Never indexable, never linkable as an entity. |
| Sign in / sign up | `UX-16` | Establish or resume an `Account` | Nothing else. No role selection — `Account` is not typed by role (`ADR-004`) | One page. **Never placed in front of `UX-01`–`UX-08`.** `UX-08` is public and anonymous and is the last anonymous surface under `WA-05`, so the wall must not fall in front of it either. |

### 1.2 Public-node invariants

- Every public node states the `ADR-005` non-claim in substance where a customer could otherwise infer date availability. "Not accepting requests" is **not** date unavailability and is never worded as either.
- Customers see only `Published` and `Stale`. **`Suspended` must not be distinguishable from `Deactivated`** on any public surface.
- No money-shaped language appears on any public node (`DB-02`).
- No `verified` badge exists without a defined auditable event; `verificationBasis` is shown instead.
- The UX never implies AI is the marketplace source of truth. In V1 there is no AI on the critical path at all.
- The reporting affordance shown on `UX-04` and `UX-05` is a link, not a form: it routes through `UX-16` and lands on `UX-39`, because `domain-model.md` requires a `Report` to come from an authenticated participant. **This is an authentication requirement on a reporting action, not a login wall on discovery** — nothing in `UX-01`–`UX-08` moves behind it. It is also what bounds `UX-27`'s queue volume, which anonymous intake would leave unrate-limited and unbounded by design.

## 2. Authenticated structure

```text
Authenticated shell — one `Account`, one session, no mode toggle
├── Hiring  (always present)
│   ├── My requests                                  `UX-11`
│   │   └── Request detail: response + conversation  `UX-12`
│   │       └── Outcome capture                      `UX-14`
│   ├── Messages  (a capacity-labelled list of threads; every thread opens in the request it belongs to)
│   ├── My drafts                                    `UX-13`
│   └── Request submitted confirmation               `UX-10`
├── My business  (PRESENT ONLY WHEN A `BusinessMembership` EXISTS)
│   ├── Workspace home / request inbox               `UX-18`
│   │   └── Request detail and respond               `UX-19`
│   │       └── Provider conversation                `UX-20`
│   ├── Profile editor                               `UX-21`
│   ├── Offering editor                              `UX-22`
│   ├── Media manager                                `UX-23`
│   ├── Publication state and requirements checklist `UX-24`
│   ├── Provider settings and notifications          `UX-25`
│   └── Provider onboarding (progressive)            `UX-17`
├── Account and settings  (locale, notifications, consent)   `UX-15`
├── Report intake and block confirmation             `UX-39`
│      REACHED FROM `UX-12`, `UX-20`, AND FROM `UX-04` / `UX-05` VIA `UX-16`
│      NOT A NAVIGATION NODE — ALWAYS ENTERED AGAINST A NAMED TARGET
└── Operator surfaces  — SEPARATE TREE, NOT A BRANCH OF EITHER CONTEXT
    ├── Case queue index                             `UX-26`
    ├── Report / moderation case detail              `UX-27`
    ├── Publication review queue                     `UX-28`
    ├── Duplicate suspicion and ownership claims     `UX-29`
    ├── `CategoryProposal` review                    `UX-30`
    ├── Geocode / `undetermined` / eligibility       `UX-31`
    ├── Delivery-attempt failure queue               `UX-32`
    ├── Staleness / `RequestIntake` decay            `UX-33`
    └── Audited private-content access               `UX-34`
```

### 2.1 Rules attached to this tree

| Rule | Statement |
|---|---|
| Messages is a list, not an inbox | A `Conversation` is attached to a `ServiceRequest` and always **opens inside that request** (`UX-12` for Hiring, `UX-20` for My business) — there is no standalone message surface, because a message read outside its request loses the `EventContext` and the offer state that make it legible. The Messages node exists so a returning user has an entry point that does not depend on a notification link surviving. Its rows span both contexts, and **every row carries its capacity label** (*as yourself* / *as {Business}*) for the same reason `UX-12` and `UX-20` do (`ADR-004`). It states no unread count that a `Conversation` the user cannot access could contribute to. |
| Drafts are Hiring-only | A `RequestDraft` has no recipient and is **never visible to any provider** (`ADR-003`). It never appears in the My business tree. |
| My business is conditional, not hidden | An `Account` with no `BusinessMembership` has **no** My business context — it is absent from the navigation, not greyed out and not behind an upsell gate. |
| Operator surfaces are a separate tree | They are **role-gated by domain authorization** (`ADR-011`), never by a route guard, a template condition, or a client check. An unauthorized request to an operator surface fails in the domain. The separation is informational, not a security boundary — the security boundary is the domain decision. |
| Every private-content access is audited | `UX-34` records the access, the reason tag, and the actor. `Q-026`'s auditability limb is resolved; its disclosure limb is open. |
| Reporting and blocking are authenticated, and that is the rate limit | `UX-39` sits in this tree because a `Report` must come from an authenticated participant (`domain-model.md`). The public affordance on `UX-04` and `UX-05` routes through `UX-16` and returns to `UX-39` with the target carried — **an authentication requirement on a reporting action, not a login wall on discovery.** It is what bounds `UX-27`'s queue volume: anonymous intake would make operator case creation unrate-limited and **unbounded by design**. **Report intake must not require the reported content to still be visible**, so a deleted or edited target never blocks submission. The reporter's free-text note is classified **sensitive** and never reaches a public surface or a notification body (`ADR-010`). A `Block` survives closure of any request and applies to future ones. |
| Notification bodies are not a surface in this tree | `UX-35` is channel-bound and allowlist-constrained (`ADR-010`). It carries a non-guessable link **into** this tree and never carries its content. |

## 3. The dual-role navigation model

`ADR-004` assigned P04 a specific consequence: *"interface design must make the current role obvious."* This is the answer.

### 3.1 Two persistent contexts, side by side — not a mode toggle

**Hiring** and **My business** are shown **simultaneously** in the authenticated shell. There is no switch that changes global state, no "you are now in provider mode", and no session variable that reinterprets the rest of the application.

| Property | Rule |
|---|---|
| Presence | Hiring is always present. My business is present **only when a `BusinessMembership` exists**. |
| Simultaneity | Both contexts are visible and navigable at once. Entering one does not leave the other. |
| Persistence | Neither context is a mode. Nothing is remembered about "which side you were on" that any decision then reads. |
| Global state | **No global role state exists to toggle.** There is one `Account` and one session. |
| Capacity disclosure | Every surface where capacity is ambiguous states it in words: a conversation shows *You are replying as **{Business}*** or *You are writing as yourself*; a request detail states which side of it you are on. |

### 3.2 The authorization rule, stated exactly

> **No authorization decision depends on the displayed context.** (`ADR-011`)

The displayed context is a **presentation fact**. It selects what is rendered and what capacity is disclosed to the reader. It is never an input to an authorization decision. Every decision — may this `Account` read this `ServiceRequest`, may it post this `ProviderResponse`, may it edit this `ProviderProfile` — is decided in the domain from the `Account`, the `BusinessMembership`, and the target aggregate. If a user reaches a My business surface for a `Business` they do not belong to, the domain refuses; the navigation being visible or invisible is irrelevant to that outcome.

The corollary is a testable one: **removing the entire context UI must not change a single authorization outcome.**

### 3.3 Becoming a provider is additive

| State | What the user sees |
|---|---|
| `Account` with no `BusinessMembership` | Hiring only. **No provider navigation exists at all** — no disabled items, no teaser, no "upgrade" affordance beyond an ordinary entry point into `UX-17`. |
| Same `Account` completes `UX-17` and a `BusinessMembership` is created | My business appears **in the same account, same session, same identity**. No second account. No account-type flip. No migration. No re-authentication as a different persona. |
| A provider hires another provider | Uses the ordinary customer path with **no special case** — a venue owner planning a quinceañera, a DJ hiring a photographer. This is the routine case, not an edge case. |
| A user leaves a `Business` | My business disappears; Hiring is untouched; the `Account` is unchanged, because it was never typed. |

The `Account` is opaque and permanent and is **not typed by role** at any point in this model.

## 4. URL and locale principles — conceptual, not final

Everything in this section is a **principle**, not a route table. Concrete paths are illustrative shapes only. Nothing here is approved, and the public-distribution gate (`Q-015`) is separate and unsatisfied.

### 4.1 Identity and canonicality

| Principle | Statement |
|---|---|
| One canonical URL per approved public entity | Each approved public entity — a `ProviderProfile`, a governed `Category`, a `Market` — resolves to exactly one canonical URL per locale. Duplicates and parameter permutations are not entities. |
| Slug is a locale alias, not identity | Identity is the stable language-neutral identifier. A slug is a localized, human-readable alias over it. **A translated string is never a key** — that is the single largest cause of unusable multilingual marketplace data. |
| Retained redirect history | When a slug changes, the previous slug is **retained and redirects**. History is never dropped. Category deprecation records its merge target so search, public URLs, measurement, and migration all survive. |
| Redirects never redefine identity | A redirect or alias moves a reader; it does not change what the entity is. |
| Illustrative shape only | `/{locale}/{category-slug}` and `/{locale}/{category-slug}/{place-slug}` are drawn here to make the hierarchy legible. **The final form is not decided in P04.** |

### 4.2 Locale

| Principle | Statement |
|---|---|
| Locale is a dimension of URL identity | Locale distinguishes URLs; it does not create a different entity. |
| Distinct locale URLs with explicit language links | When more than one public locale ships, each locale has its own URL, with explicit language links and reciprocal `hreflang` annotations. **Do not rely on client-hint, cookie, or address-based switching alone.** |
| Locale does not follow country | Country and locale are independent decisions (`internationalization-architecture.md` §2). The launch production locale may be Spanish even if the launch geography is the United States. No URL rule may assume `country → locale`. |
| Three locale roles, never one field | `contentLocale` (the language of one piece of authored content), `uiLocale` (an account preference on `UX-15`), `marketLocales` (what a `Market` supports). A public page must not present a `contentLocale` mismatch silently, and machine-translated content is **explicitly marked** so no surface presents a translation as the provider's own words. |
| Locale expansion is a scope decision | A second public locale is a MEDIUM-HIGH, permanent content and staffing cost, not a structural one. Structure is cheap now; scope is approved separately. |
| `Q-020` is open | The canonical locale for a `ProviderProfile` and for a request `Conversation` is a P04-named question. P04 records a recommendation; it does not claim resolution. |

### 4.3 Machine access is a separate switch from human publication

> **Human-public browsing does not authorize crawling.** (`ADR-012`, `docs/07-research/ai-discoverability.md`)

| Principle | Statement |
|---|---|
| Deny by default | Machine access is an explicit per-surface, per-crawler-class switch that **defaults to deny**, with **one enforcement point and one owner** (`ADR-012`, `DB-13`). |
| Indexability is not emergent | Publication-to-humans and machine-access authorization are two independent switches. A page being reachable decides nothing about whether it may be crawled. |
| Locale expansion authorizes nothing | Adding a public locale does **not** implicitly approve indexing of that locale. |
| Machine claims never exceed human claims | In any locale, a machine-readable claim must never exceed what the visible human page shows. |
| A JSON endpoint is a machine-access surface | An endpoint built for a client island (`I-1`, `I-2`, `I-3`) is bound by the same single enforcement point. **No JSON API built "just in case"** (`R-012`). |
| **Ordering** | **`Q-027` must be settled before `Q-015`.** Superola cannot guarantee removal from third-party indexes or corpora it authorized, so approving a crawler class before the deletion path exists makes the first deletion request permanently unanswerable. The gate stays closed until the deletion path exists — not after. |
| Deletion has URL consequences | Deletion requires an unambiguous gone response, a sitemap drop, and a suppression record preventing re-publication by a later re-import. |

## 5. What is public versus authenticated

Keyed to the canonical surface inventory (`docs/04-ux/design-canon.md` §6; full records in `docs/04-ux/surface-inventory.md`).

| ID | Surface | Exposure | What an unauthenticated visitor may see |
|---|---|---|---|
| `UX-01` | Home / need entry | Public | Everything on it |
| `UX-02` | Category / archetype browse | Public | Governed category definitions, labels, synonyms, archetype |
| `UX-03` | Market page (Category × Place) | Public | Coarse `Place` label, eligible `Published`/`Stale` supply, declared coverage in words |
| `UX-04` | Search results | Public | Result slots, `placementBasis` = `organic`, ranking explanation, freshness, `RequestIntake`, `undetermined` with its uncertainty |
| `UX-05` | Provider profile | Public | Provider-published facts only, per the allowlist-derived projection (`ADR-012`) |
| `UX-06` | Informational / trust pages | Public | Everything on it |
| `UX-07` | Request composer | Public | Their own local `RequestDraft` only |
| `UX-08` | Request review and send | Public | Their own draft only |
| `UX-09` | Identity and channel verification | Transitional | Their own verification step only |
| `UX-16` | Sign in / sign up | Public | The form |
| `UX-10` | Request submitted confirmation | Auth | Nothing |
| `UX-11` | My requests | Auth | Nothing |
| `UX-12` | Request detail: response + conversation | Auth | Nothing |
| `UX-13` | My drafts | Auth | Nothing |
| `UX-14` | Outcome capture | Auth | Nothing |
| `UX-15` | Account and settings | Auth | Nothing |
| `UX-17` | Provider onboarding | Auth | Nothing |
| `UX-18` | Provider workspace home / request inbox | Auth | Nothing |
| `UX-19` | Provider request detail and respond | Auth | Nothing |
| `UX-20` | Provider conversation | Auth | Nothing |
| `UX-21` | Profile editor | Auth | Nothing — but it authors `UX-05` content |
| `UX-22` | Offering editor | Auth | Nothing — but it authors `UX-05` and `UX-04` content |
| `UX-23` | Media manager | Auth | Nothing — but it authors `UX-05` media |
| `UX-24` | Publication state and requirements checklist | Auth | Nothing. **Provider-visible states never surface publicly** beyond `Published`/`Stale` |
| `UX-25` | Provider settings and notifications | Auth | Nothing |
| `UX-39` | Report intake and block confirmation | Auth | Nothing. The affordance on `UX-04` / `UX-05` is visible; the surface itself is not. It routes through `UX-16` — **an authentication requirement on a reporting action, not a login wall on discovery** — and requiring it is what bounds `UX-27`'s queue volume. **Report intake must not require the reported content to still be visible.** The reporter's free-text note is classified **sensitive** |
| `UX-26`–`UX-34` | Operator surfaces | Auth, role-gated in the domain | Nothing. **Eight domain queues plus a cross-queue index, on nine surfaces**; `UX-29` carries two case kinds. Existence of a case, its reason, its reporter, and its contents are all non-public |
| `UX-35` | Notification message | Channel | Not a web surface. Allowlist-bound body (`ADR-010`) |
| `UX-36` | Legacy profile claim | Auth, `FUTURE` / conditional | Nothing. **Disclose nothing before a claim grant — a masked contact hint is an enumeration oracle** (`ADR-009`) |
| `UX-37` | Sponsored placement surfaces | `FUTURE` | n/a in V1 |
| `UX-38` | Booking / payment / payout surfaces | `FUTURE` | n/a in V1 |

### 5.1 Fields that never reach a public surface — hard list

No surface in the Public column above, and no notification body, may contain any of the following. This list is not negotiable per-surface.

| Field | Note |
|---|---|
| Precise provider base location | Coarse `Place` label and declared coverage in words only. A `GeoPoint` with street-level precision is never published. |
| Customer name | — |
| Customer contact channel | Resolves at delivery/render time through a recorded disclosure decision (`ADR-010`) — never an attribute of a request, message, or notification |
| **Event address** | **The highest-harm field in V1.** |
| Event date | Also never a search filter — request context only |
| Guest count | — |
| Budget | `QUALITY`, optional, band-shaped, reaches the provider inside the authenticated request surface only |
| Request free text | Classified as possibly containing contact data regardless of policy |
| Conversation text | — |
| Offer amount and terms | A `PriceStatement` is authenticated-only. No money-shaped language publicly (`DB-02`) |
| `Suspended` as a state | Customers must not be able to distinguish `Suspended` from `Deactivated` |
| Response-rate and response-time badges | — |
| `ResponsivenessObservation`, `FreshnessObservation` | Internal observations, not public facts |
| Report contents and reporter identity | A blocked party sees a policy-level reason only — never who reported, never the content |

## 6. Navigation depth and entry points

The design target is **5 composer answers on the discovery path**, of which 3 are reused from discovery, rising to as many as 8 on direct arrival, and **10–11 total blocking items to submit** with identity counted — compared against a competitor capture of 24 items, roughly twenty of them presented as steps (Evidence `OBSERVED`, competitor behavior as supplied; Provenance `DAVID_DIRECTIVE`; not independently re-verified during P04, and no Superola requirement derives from it). This is a **design target, not a measured improvement** — `SRC-006` is NOT RECEIVED. The measurement that would confirm it is per-stage composer completion instrumented at `draft created` and at each named stage (`R-022`).

Reuse is what buys the reduction, so **every entry point into `UX-07` must carry the context the customer already supplied.**

### 6.1 The three entry paths

```text
FROM SEARCH
`UX-01` → `UX-04` (Category + Place resolved via `I-1`) → provider slot → `UX-05` → Request this provider
  → `UX-07` CARRIES: `Category`, `Place`, selected provider  → 3 discovery answers already reused
  → `UX-08` → `UX-09` → `UX-10` → V1 CONTINUES IN `UX-11`
```

```text
FROM A MARKET PAGE
`UX-02` → `UX-03` (Category × Place already fixed by the page itself) → provider → `UX-05` → Request this provider
  → `UX-07` CARRIES: `Category`, `Place`, selected provider  → the composer never re-asks either
  → `UX-08` → `UX-09` → `UX-10` → V1 CONTINUES IN `UX-11`
```

```text
FROM A DIRECT PROFILE LINK  (shared link, external referral, provider's own promotion)
`UX-05` → Request this provider
  → `UX-07` CARRIES: selected provider, and the `Category` IF the profile has exactly one `ServiceOffering`
  → OTHERWISE the composer asks for the `Category` first, from that provider's offerings only — never the full taxonomy
  → `Place` is asked, because a direct link carries none
  → `UX-08` → `UX-09` → `UX-10` → V1 CONTINUES IN `UX-11`
```

```text
ANOTHER SERVICE FOR THE SAME EVENT  (`WA-02` preserved — no fan-out)
`UX-12` (open or closed request) → Request another service for this event
  → NEW `RequestDraft` PRE-FILLED FROM THE PREVIOUS REQUEST'S `EventContext`, carrying `eventGroupingHint`
  → customer picks a NEW `Category`, answers only that archetype's `CATEGORY` questions
  → selects a new provider DELIBERATELY → `UX-08` → V1 CONTINUES IN `UX-11`
  → NO MULTI-SERVICE RFQ, NO FAN-OUT, NO AUTOMATIC ROUTING
```

### 6.2 What "carrying context" means precisely

| Carried | Rule |
|---|---|
| `Category` | Governed identifier, never free text. Reused as the discovery answer; the composer does not re-ask it. |
| `Place` | Governed `Place` identifier resolved against the internal list with **zero vendor calls** (`ADR-019` Level 1). Reused; not re-asked. |
| Selected provider | `WA-02` — one deliberate recipient. Carried as the intended recipient of the eventual `ServiceRequest`; the draft itself still has **no recipient** until submit. |
| `EventContext` on re-request | Universal answers pre-filled and **editable**, never silently assumed correct. |
| Nothing else | Carried context is limited to what the customer actually supplied. Nothing is inferred, and nothing carried is treated as confirmed without the customer seeing it on `UX-08`. |

### 6.3 Back and exit paths preserve the draft

| Path | Behavior |
|---|---|
| Back within the composer | Returns to the previous named stage with all answers intact. Answered questions are **not reordered** by the return. |
| Back out of the composer to `UX-05` or `UX-04` | The draft survives. It is browser-local and anonymous before an `Account` exists, and server-persisted as a `RequestDraft` from the moment one does (`WA-05`, or earlier on voluntary sign-in). Re-entering resumes at the last stage. |
| Changing `Category` mid-draft | Universal `EventContext` answers are **retained**. Category-specific answers for the previous category are dropped **only after an explicit before-the-fact warning naming what will be lost**, and are held in session so an immediate undo restores them. |
| Changing event type mid-draft | **Nothing is dropped.** Event type is universal and never gates a category-specific answer. |
| Abandoning entirely | Same-device anonymous resume from local state; cross-device resume only once the draft is server-persisted. Abandonment is a measured transition, instrumented separately from verification abandonment because the fixes are different (`R-022`). |
| Abandoning at `UX-09` | The request is already durably stored as `PendingVerification` — **durable but invisible to the provider**. The customer's work is never lost. |
| Exit depth | From any public entry point, the composer is reachable in **one action** from a provider profile, and the confirmation is at most four surfaces from the first discovery answer. Depth beyond that is a defect, not a design choice. |

## 7. Constraints on this architecture

1. **`G-06` is UNSATISFIED and formally blocks P04.** This hierarchy proceeds on `WA-01` — `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` — under explicit David authorization. No node above may be read as resolving what "available" promises a customer.
2. **No URL is decided here.** Section 4 is principle. The route table, slug format, locale prefix strategy, and sitemap are downstream of an approved public-distribution policy (`Q-015`), which this document does not satisfy and cannot satisfy.
3. **`Q-027` must be settled before `Q-015`.** Machine access stays deny-by-default with one enforcement point and one owner (`ADR-012`, `DB-13`) until the deletion path exists.
4. **No authorization outcome may depend on the displayed context.** If removing the Hiring / My business navigation would change any authorization result, `ADR-011` has been violated and the implementation is wrong.
5. **A `Market` page is generated only where real eligible supply and differentiated human value exist.** Mass generation of the Category × Place cross-product is prohibited (`R-011`).
6. **`Q-020` (canonical locale for a profile and for a request conversation) is recorded as open.** The recommendation in §4.2 is a recommendation.
7. Rendering classification per node is **not** decided in this document. The evidence base is `docs/04-ux/surface-inventory.md` and the decision argument is `docs/04-ux/rendering-evidence.md`, feeding `ADR-020`.
