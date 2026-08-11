# Internationalization Architecture Requirements — P02

> **Status:** `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`).
>
> **Hard boundary:** no technology, localization platform, translation service, geocoding source, or cloud region is selected. Tax and payment internationalization is **out of scope** — it belongs to the Transaction Extension, which is `FUTURE` and separately owner-approved.

## 1. The two constructs that must never be conflated

This is the single most important section in this document, because collapsing these two is how a cheap structural abstraction turns into an expensive operational commitment nobody approved.

| | **Architecture capability envelope** | **Launch operational scope** |
|---|---|---|
| Claim | The model must permit additional countries and locales **without structural redesign** | Which countries and locales actually ship |
| Status | `PROPOSED`; provenance `DAVID_DIRECTIVE` | **OPEN.** `OR-003` is `ASSUMPTION`; `OR-004` is `PROPOSED`; `A-002` is OPEN |
| Justified by | `docs/00-context/product-context.md` and `AGENTS.md`, **independent of any launch-market claim** | An owner decision, or a documented bounded scenario set David accepts |
| Cost | NEAR-ZERO now; HIGH and sometimes unrecoverable if retrofitted | MEDIUM-HIGH and **recurring** per additional locale |

**The working envelope says United States plus Mexico and English plus Spanish. `docs/05-roadmap/mvp-scope.md`, `OR-003`, and `OR-004` say one owner-approved geography and one production locale for the costing and learning baseline.** These are incompatible as *launch scope* and compatible as *structure versus scope*. P02 therefore records two named scenarios and stamps every artifact with the one it assumes.

| Scenario | Definition | Cost consequence |
|---|---|---|
| **S-1** *(assumed by every P02 artifact)* | One launch geography, one production locale. Multi-country and multi-locale capable structure. | NEAR-ZERO extra build. One content set to author and maintain, one language of operator staffing, one public content inventory. The second locale becomes a gated expansion **whose cost is then visible as its own decision.** |
| **S-2** | Two launch geographies, two production locales. | Doubles content authoring and permanent maintenance; doubles notification, legal, and policy translation; requires bilingual operator coverage from day one; forces the provider-content-language question to be answered before any evidence exists. Worst of all it doubles content cost in the exact phase where the product is most likely to pivot — **and every pivot re-translates.** |

**Extensibility is not evidence that two countries or two locales launch together.**

## 2. A finding not currently in the repository: locale does not follow country

The repository treats locale as following geography — `OR-003` and `OR-004` are separate rows, but nothing warns that the mapping between them is non-obvious. It is not.

The owner-reported legacy category mix — mariachi, norteño groups, banda — is direct evidence of a **Spanish-speaking customer and provider segment inside the United States**. Therefore:

> **The launch production locale may be Spanish even if the launch geography is the United States.** Country and locale are independent decisions. P02 must not assume `country → locale`, and neither must P04.

This also means the two-locale question is **not purely operational**. `docs/00-context/product-context.md` names vendor-generated multilingual content as a distinct internationalization concern, and provider-authored bilingual content is a **content-model** question, not a staffing question (`A-015`, `Q-020`).

## 3. Conceptual handling

| Concern | Requirement | Cost if carried now | Cost if retrofitted |
|---|---|---|---|
| **Country** | A `Place` of kind country identified by a standards-based stable code as an identifier *concept*. Country is the **policy anchor**: it determines subdivision vocabulary, address shape, candidate currencies, default measurement system, applicable legal and privacy regime, and supported locales. **Never an enum of two. No launch-country literal appears in any entity.** | NEAR-ZERO | HIGH |
| **Administrative subdivisions** | A **variable-depth parent chain with a governed place kind.** A fixed country / state / city triple breaks the moment you leave the origin country; subdivision depth and vocabulary differ per country. Depth is data, not structure. | NEAR-ZERO | HIGH |
| **Language-neutral identifiers** | Every governed vocabulary — category, archetype, attribute definition, allowed values, place kind, response kind, outcome value, verification basis — uses a stable identifier with **separately localized labels**. A label is never an identifier. | NEAR-ZERO | HIGH. A translated string used as a key is the single largest cause of unusable multilingual marketplace data |
| **Localized category labels** | Labels and synonyms per locale over the stable category identifier, plus a locale-bound slug with **retained redirect history**. Category deprecation records its merge target so search, public URLs, measurement, and migration survive. | NEAR-ZERO | HIGH |
| **Content locale** | Every piece of authored text carries an explicit `contentLocale`, and machine-translated content is **explicitly marked as such**. | NEAR-ZERO | MEDIUM-HIGH. Without it you cannot tell what language a description is in, and a consumer may present a translation as the provider's own words |
| **Locale roles** | Three separate roles, never one field: `contentLocale` (the language of one piece of content), `uiLocale` (an account preference), `marketLocales` (what a `Market` supports). | NEAR-ZERO | MEDIUM |
| **Timezone** | An IANA zone identifier resolved from the **service location** — not the provider's base, not the server, not the account. Every stored instant carries the zone context it belongs to. | NEAR-ZERO | **HIGH and often unrecoverable.** Ambiguous historical wall-clock values cannot be reconstructed |
| **Currency** | On **every** monetary value, always explicit, and **independent of country** — a provider in one country may quote in another's currency. `PriceStatement` requires currency, basis, inclusions, exclusions, conditions, and as-of. **No implicit currency, ever.** | NEAR-ZERO | HIGH |
| **Address representation** | Locale-variant structured fields plus a free-form fallback line. Privacy-classified; never required to be fully public. Postal codes are a **zone vocabulary for delivery archetypes**, not a universal geography. | NEAR-ZERO | HIGH. The owner-reported broken legacy location behaviour is direct evidence of this failure |
| **Distance, service areas, units** | Store the value **as entered with its unit** *and* a canonical value; present per locale. "50 miles" and "80 km" are different provider promises, and rounding changes who is eligible. **A named-market list is a first-class alternative to a radius for every archetype**, because a radius that crosses a national border or a metro boundary is a lie. | NEAR-ZERO | HIGH |
| **Measurement system** | Derived from country and locale policy, never hard-coded. | NEAR-ZERO | MEDIUM |
| **Number and date formatting** | From locale data, at the presentation edge. | NEAR-ZERO | LOW-MEDIUM |
| **String composition** | No concatenated user-facing strings; no locale baked into an identifier or a URL-generation rule that cannot take another value. | NEAR-ZERO | MEDIUM |
| **Measurement segmentation** | Every marketplace event carries `market`, `locale`, and `archetype` **in the record shape from day one**, so aggregate numbers cannot hide a dead local market. | NEAR-ZERO | **Impossible.** You cannot segment history that was never recorded segmented |

## 4. Expensive and operational — each needs its own approval

None of these is structural. Each is a scope decision with a recurring cost, and none is implied by the structural work in §3.

| Item | Cost direction | Dominant driver |
|---|---|---|
| A second locale's **content** | MEDIUM-HIGH and **permanent** | Every interface string, notification template, legal and policy text, error state, and empty state — then maintained forever, so **every future feature pays the tax twice** |
| **Bilingual operator and support staffing** | HIGH, **recurring** | The sleeper cost. Every operator queue becomes bilingual, which is a **multiplier on every queue**, not a new queue. `docs/05-roadmap/mvp-scope.md` already requires language coverage to be defined before launch approval — that is a staffing line in a launch with no revenue |
| Provider-generated multilingual content | MEDIUM-HIGH | If profiles are monolingual in a bilingual market, cross-language discovery degrades. The two fixes are machine translation (variable cost, quality and liability, and a **truthfulness** problem when a translated claim differs from the provider's actual claim) or asking providers to author twice (supply friction they will not accept) |
| Translated public location or category page programme | HIGH | A per-locale differentiated content inventory that must be maintained. `R-011` warns this is exactly how a discovery strategy becomes search-quality debt. Plus per-locale URL and language-link correctness |
| Notification templates × locales × channels | MEDIUM | A multiplier, each item needing review |
| Per-market taxonomy label divergence | MEDIUM | Norteño relevance differs by market; governed, but it is content work |

**Cheap alternative worth trying first:** a governed **synonym and alias table** on the taxonomy absorbs a large share of surface-form variance at near-zero marginal cost, and it works across languages because filtering happens on identifiers.

## 5. Multi-country data and policy is not multi-region infrastructure

**Stated bluntly: "United States plus Mexico" is a market decision, not a topology decision. Anyone who draws a second region on an architecture diagram has purchased infrastructure with a marketing statement.**

| | Multi-country data and policy | Multi-region infrastructure |
|---|---|---|
| Verdict | **BUILD NOW** | **REJECT** |
| Cost | NEAR-ZERO now, HIGH retrofitted | HIGH, multiplied per region |
| Justification in evidence | `docs/00-context/product-context.md`, `AGENTS.md` | **None.** No residency requirement, no latency requirement, no traffic evidence (`SRC-006` NOT RECEIVED). Under S-1 the market scope is one geography, making it doubly unjustified |
| Driver | — | Per-region fixed floors; inter-region transfer; every investigation begins with "which region?"; a consistency model that must survive cross-region latency; regional data placement is hard to unwind |

A cache layer in front of public read surfaces addresses perceived latency for the pages that actually matter — public profiles — **without regionalizing the transactional core.**

**Triggers to reconsider:** a legal data-residency obligation in an approved market; **or** measured p95 latency from an approved market exceeding the approved budget *after* public reads are cached. Instrumentation required in V1 to make these observable: latency by client geography, and cache hit ratio on public reads.

**Do not foreclose it, either:** avoid scattering personal data across systems that cannot be relocated. The lock-in and exit row in `docs/05-roadmap/mvp-scope.md` already requires data export and deletion proof.

## 6. Multi-jurisdiction structural implications

Two countries in the working scenario means the legal and privacy regime becomes a per-record property. What is **structurally** required — cheap now, expensive to retrofit — is in `security-privacy-architecture.md` §8. In summary: a **subject reference** rather than contact strings duplicated across tables; retention and deletion parameterizable per record; lawful basis per record; a **jurisdiction attribute per data subject with `unknown` as a real value mapping to the strictest handling among the working markets**; and locale-specific consent and notice text as **versioned content with the version recorded on the consent record**.

**Sharpening for `G-05`:** because multi-jurisdiction is now in the working scenario, the accepted bounded scenario set must **name jurisdictions, not just markets** — otherwise the per-subject jurisdiction attribute has no domain of values and `A-025` cannot be assessed.

**Out of scope for V1 Phase 1:** tax and payment internationalization, invoicing, tax identifiers, and per-market consumer transaction rules. Those belong to the Transaction Extension.

## 7. Public distribution and locale

Conditional on an approved public-distribution policy, which is a separate gate (`Q-015`) that human-public browsing does not satisfy:

- Locale is a **dimension of public URL identity**, not a different entity. Redirects and aliases never redefine entity identity.
- Distinct locale URLs with explicit language links when more than one public locale ships. Do not rely on client-hint, cookie, or address-based switching alone.
- Canonical identity per approved public entity, with retained redirect history on slug change.
- **Machine-readable claims must never exceed visible human claims**, in any locale.
- Indexability and each crawler class remain separately approval-gated. A locale expansion does **not** implicitly approve indexing of the new locale.

## 8. Requirements handed to P03

Stated as questions, not answers. Full list in `p03-decision-inputs.md`.

- Place-resolution and geocoding source coverage and terms **per country**, behind Geography's owned boundary.
- Label and localization storage and rendering approach, including how a label set is versioned.
- Geographic predicate capability for radius, zone, and market-list containment — and the fact that route-corridor semantics do not reduce to containment.
- Public rendering approach for locale-distinct URLs, if and when public distribution is approved.
- Whether any locale expansion requires a content-operations capability that does not currently exist.

## Sources

`AGENTS.md` · `docs/00-context/product-context.md` · `docs/00-context/assumptions.md` (`A-002`, `A-015`, `A-016`, `A-025`) · `docs/01-product/feature-inventory.md` · `docs/01-product/owner-reconciliation-matrix.md` (`OR-003`, `OR-004`, `G-05`) · `docs/00-context/interview-evidence.md` (owner-reported category mix) · `docs/05-roadmap/mvp-scope.md` · `docs/05-roadmap/risks.md` (`R-003`, `R-011`) · `docs/07-research/ai-discoverability.md` · `docs/07-research/maps.md` · `SRC-013`.
