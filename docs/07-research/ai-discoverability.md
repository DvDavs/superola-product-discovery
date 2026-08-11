# AI Discoverability and Agent Distribution

> Status: `PROPOSED`; provenance `TECHNICAL_DISCOVERY`. No protocol, vendor, architecture, public API, or launch scope is accepted.
> Scope: bounded P01 research authorized by `SRC-012`.
> Access date for all linked sources: 2026-08-11.

## Decision problem

How can Superola support human discovery, machine discovery, and plausible future agent-mediated actions without replacing sound marketplace search with a language model, adopting immature protocols, or inflating V1?

## Recommended principle

`PROPOSED`:

> Superola should maintain a governed marketplace model that is useful to humans and legible by machines, able to serve web/mobile interfaces, conventional search, internal intent interpretation, and—when evidence justifies it—authorized external agents through separately approved future channels.

This is an AI-native **distribution principle**, not an AI-first product commitment. It does not require MCP, A2A, A2UI, UCP, ACP, `llms.txt`, an AI microservice, or a public API in V1.

## A. Internal AI-assisted discovery

### Proposed boundary

`Natural-language intent → structured constraints → deterministic eligibility/filtering → deterministic or governed ranking → explained results`

The model may interpret phrases such as event type, service, geography, date, guest count, budget, and preferences. It must not invent unavailable filters, bypass hard constraints, silently promote sponsored results, or become the authoritative store for taxonomy, availability, price, or ranking.

### Preconditions

- Governed service taxonomy with language-neutral identifiers and localized labels.
- Explicit base location, event location, service area, country, timezone, and date semantics.
- Deterministic search/ranking baseline with measurable relevance and zero-result behavior.
- Evaluation set covering English/Spanish ambiguity, incomplete intent, unsupported constraints, and adversarial input.
- Confidence/fallback behavior and an explanation of applied constraints.
- Privacy, retention, prompt-injection, abuse, latency, cost, observability, and provider-portability controls.

### P01 conclusion

Internal AI is a `SEPARATE OPTIONAL EXPERIMENT`, not a launch foundation or V1 critical-path exception. Reconsider it only with independent approval/budget after deterministic search is testable and representative queries show measurable incremental value.

## B. External AI discoverability

For the reviewed channels—Google AI Mode/AI Overviews and ChatGPT Search—external AI discovery currently rests primarily on honest public-web discoverability, not a parallel “GEO/AEO” architecture. This applies only after the business approves indexability and the relevant crawler; human-public browsing does not grant crawler authorization. Applying the conclusion to another assistant or distribution channel is a `PROPOSED` inference until that channel's primary guidance and measured behavior are reviewed.

### Human-public foundation and separately approved indexing

| Concern | `PROPOSED` requirement | Evidence boundary |
|---|---|---|
| Human-rendered content | Approved public provider/service pages return useful visible HTML for people. Rendering approach is a later technology decision. | Human-public publication does not itself authorize indexing, AI-search crawling, or training access. |
| Indexability/crawlers | Indexability and each crawler class require an approved acquisition/content/privacy/legal policy. | Googlebot, OAI-SearchBot, and GPTBot have different purposes; training access is decided independently. |
| URL identity | Stable canonical URL per approved human-public entity; redirects and aliases do not redefine entity identity. | If indexing is approved, canonicals, internal links, and sitemaps should agree. |
| Discovery maps | After indexing approval, XML sitemaps use accurate canonical absolute URLs and meaningful `lastmod`; partition only as scale requires. | A sitemap is a discovery hint, not an indexing guarantee or crawler authorization. |
| Semantic public data | Visible, useful service, service-area, location, price/availability, review, and trust facts when accurate and permitted. | Machine-readable claims must not exceed visible human-facing claims. |
| Content quality | People-first provider/service/location content with real supply and differentiated value; no mass thin pages. | Google states no special AI markup is required for AI Mode/Overviews. |
| Reviews | First-party review provenance, visibility, moderation, incentives, and aggregation rules. | Do not import/aggregate third-party reviews as if collected by Superola or misuse self-serving review markup. |
| Locale | Distinct locale URLs, explicit language links, and `hreflang` when multiple public locales ship. | Do not rely only on IP, cookie, or `Accept-Language` switching. |
| Measurement | Search Console/analytics plus preserved referral attribution, conversion, lead quality, and freshness monitoring. | Eligibility is not placement; measure outcomes, not citation anecdotes. |

### Crawler policy must be explicit

- `Googlebot`: controls Google Search crawling, including eligibility for Google AI features through the normal Search index.
- `OAI-SearchBot`: controls OpenAI search discovery/summaries. OpenAI says public sites may appear in ChatGPT Search and documents `utm_source=chatgpt.com` referral attribution.
- `GPTBot`: relates to potential training use and must be governed separately from OAI-SearchBot.
- `noindex` and snippet controls are separate from `robots.txt`; a crawler must access a page to observe its meta directives.
- CDN/WAF/bot mitigation must not accidentally contradict the approved crawler policy.

`PROPOSED` narrow default for review: allow public-search crawlers needed for approved acquisition goals while deciding training access separately with legal/privacy/content owners. This is a policy gate, not an automatic implementation.

### Structured-data cautions

- Structured data/JSON-LD is a conditional enhancement for a named eligible search surface, not required for ordinary indexing. Its V1 inclusion needs a maintained entity mapping, visible-page consistency, and a measurable discovery hypothesis.
- JSON-LD is semantic support, not an AI-ranking hack.
- `Event` markup describes an actual public, bookable event on a dedicated page. A provider or event service is not an `Event` merely because Superola serves the events market.
- Price, availability, reviews, and service-area claims require freshness and visible-page consistency.
- `LocalBusiness` may fit some provider/location representations, but schema type and properties must follow the actual entity and current search-engine eligibility rules.

## C. Future agent-friendly Superola actions

Potential actions, without implementation commitment:

1. Search providers with explicit constraints and provenance-aware organic/sponsored results.
2. Read public profile, services, service area, availability state, price indicator, and freshness.
3. Request or check availability without exposing private calendar detail.
4. Create a consented service request/RFQ with deduplication and recipient provenance.
5. Read quote/request status under authorized identity.
6. Later, and only if approved, confirm booking or initiate payment with human confirmation.

OpenAI currently documents a beta, approved-partner **Local Services Get Quote** conversion specification using a provider/business feed and an MCP tool. It is closer to event-service RFQ than retail ACP, but it does not establish V1 access, target-market availability, commercial value, or booking support. It is evidence to preserve clean identifiers and action boundaries—not to implement the integration.

### Product and security properties for later architecture evaluation

- Account/person, business, provider profile/service, category, geography, RFQ, quote/offer, conversation, and any future booking/payment concept must not be conflated. Identifier design is a later architecture decision and future concepts need no V1 mechanism.
- Approved public profiles need stable canonical URLs and truthful localized labels; URL/identity implementation remains a later decision.
- Base location, service area, event/service location, country, and timezone are distinct product meanings where the launch cohort needs them.
- Any availability claim needs explicit meaning, source, freshness, timezone, and category context. `A-006` is `SUPERSEDED`; Q-004/Q-007 and provider research still own the unresolved semantics.
- RFQ, quote/offer, conversation, and any future booking/payment event must remain truthfully distinct in product language and audit evidence.
- Retries must not create duplicate marketplace effects; actor/channel provenance and outcomes must be auditable. Mechanisms such as idempotency keys, correlation IDs, command/query separation, or adapter architecture belong to P02/P03 alternatives and ADR gates.
- Public data needs per-field publication/privacy and freshness/correction policy.
- Organic and sponsored information remain distinct in human and machine representations.
- Sensitive or irreversible actions require authorization, explicit consent/confirmation, abuse controls, auditability, rate controls, and provider opt-out appropriate to the approved risk.

These properties do not select services, deployment units, APIs, protocols, identifier mechanics, or adapter architecture.

### Data and model privacy gate

Before any internal-AI experiment processes event or user data, an approved data-flow and threat assessment must define:

- permitted fields and prohibited sensitive fields across event date/location, budget, preferences, contact, conversation, provider, and account data;
- minimization, pseudonymization/redaction, purpose limitation, and consent or other lawful basis;
- model/vendor retention, no-training terms, subprocessors, regional processing/residency, access controls, and incident obligations;
- prompt/response/telemetry logging, log redaction, retention period, deletion and data-subject correction/deletion paths;
- server-side validation, prompt-injection isolation, human review, and the deterministic fallback.

Until this gate passes, use synthetic or explicitly approved de-identified evaluation data only.

## D. Protocol watch

The applicability values below are portfolio classifications requested by `SRC-012`, not repository evidence labels. `DESIGN-FOR` records a research/watch posture only; it creates no current design, adapter, API, feed, or transport obligation.

| Mechanism | P01 classification | Current fit and boundary | Reconsideration trigger |
|---|---|---|---|
| Google AI Mode / AI Overviews | `CURRENTLY RELEVANT TO V1` through ordinary Search eligibility | Google says core SEO, indexability, useful text, internal links, page experience, and truthful structured data apply; no special AI schema or AI text file is required. | Material Search guidance or target-market behavior changes. |
| OAI-SearchBot | `CURRENTLY RELEVANT TO V1` only when OAI-SearchBot/indexing is separately approved | Search discovery crawler, distinct from GPTBot. Human-public pages do not automatically authorize it; it is not an action protocol or ranking guarantee. | Approved public acquisition policy or measured ChatGPT referral behavior changes. |
| OpenAI Local Services Get Quote | `DESIGN-FOR`; implementation `FUTURE` | Beta/approved-partner feed + MCP quote-request flow; closer to Superola than retail commerce, but access/economics/booking are unproven. | Partner access, target-country support, stable RFQ semantics, measurable qualified demand, acceptable security/economics. |
| Open MCP | `DESIGN-FOR`; implementation `FUTURE` | General agent-to-tool/resource protocol. No current design action: compare transport/integration alternatives through an ADR only after a named client and approved business case exist. | An approved distribution client requires it and ROI supports authorization, monitoring, support, and compliance. |
| A2A | `EXPERIMENTAL/FUTURE` | Agent-to-agent task interoperability across independently operated agents. Not ranking, not ordinary API invocation, and unnecessary until a real cross-organization agent case exists. | Approved independent-agent workflow that conventional APIs cannot serve economically. |
| A2UI | `EXPERIMENTAL/FUTURE` | Declarative agent-generated UI rendered through host-approved components. It is **not** search ranking or discoverability. | Stable release, target-host adoption, accessibility/security validation, and evidence over deterministic UI. |
| Google UCP | `NOT CURRENTLY APPLICABLE` to V1 | Commerce profile/catalog/checkout/fulfillment/order capabilities remain shopping/merchant-led; no validated general event-service RFQ model. | Official service-marketplace/RFQ support in target countries and acceptable merchant, payment, privacy, and partner terms. |
| OpenAI ACP | `NOT CURRENTLY APPLICABLE` to V1 | Product catalogs, promotions, product discovery, checkout, delegated payment, and merchant operations remain retail-product oriented. | Official service marketplace, quote, booking, and target-market semantics. |
| `llms.txt` | `EXPERIMENTAL/FUTURE` | A proposed Markdown content index, not access control. Google says it is unnecessary for AI Search eligibility; official OpenAI Search guidance reviewed does not establish third-party ingestion. Support varies, and publishing one proves nothing about consumption. | A material consumer documents support and a bounded experiment shows measurable benefit without stale duplication. |

### Explicit non-claims

- A2UI does not improve search ranking.
- MCP and A2A adoption do not make them Superola requirements.
- UCP and ACP retail progress does not establish fit for negotiated event-service quotes or bookings.
- `llms.txt` does not replace `robots.txt`, sitemaps, semantic HTML, structured data, or useful content.
- No protocol belongs in V1 solely to claim that Superola is AI-native.

## Launch-slice impact

### Required human-public foundation — conditional on human-public launch

- Useful human-rendered public HTML, stable public meanings/URLs, permissioned visible facts, and correction/freshness policy.
- Deterministic search/ranking before internal AI.
- Privacy/publication rules and freshness for public provider, service area, price, review, and availability data.
- Locale-aware URLs and structured geography/timezone semantics when multiple locales/markets ship.
- RFQ/quote lifecycle foundations only if those human journeys are included.

### Separately approved indexing/discovery layer

- Explicit indexability policy plus crawler-specific decisions for Googlebot and OAI-SearchBot; GPTBot/training access is decided independently.
- Only after approval: indexable useful HTML, metadata, sitemap/canonical/robots correctness, and referral/lead-quality measurement.
- JSON-LD only for a named eligible surface with visible-data consistency and maintenance economics.

### Separate optional experiment — outside the V1 critical path

- Natural-language interpretation to validated filters with deterministic fallback, independent approval/budget, quality baseline, privacy boundary, cost ceiling, and stop rule.

### Future interoperability

- Any read-only partner feed, requiring a separately authorized change, named partner/channel, measurable hypothesis, and approved economics/security/privacy/operations boundary.
- External authenticated availability, RFQ, quote-status, booking, and payment actions.
- OpenAI Local Services Get Quote pilot or another partner-specific adapter.
- Remote MCP, A2A, A2UI, UCP, ACP, delegated authorization, partner certification, and machine-to-machine commercial operations.
- `llms.txt` experiment with a named consumer and metric.

### Anti-inflation rule

V1 must not contain a deliverable whose only consumer is an emerging protocol. A foundation belongs in V1 only when it already improves human journeys, web discovery, consistency, security, privacy, or operational integrity.

## Risks and controls

| Risk | Required control before implementation |
|---|---|
| Stale price, availability, or profile cited by an agent | Source/freshness metadata, visible-page consistency, cache policy, provider correction path. |
| Duplicate or automated RFQ spam | Retried actions cannot create duplicate effects; require consent, recipient limits, deduplication behavior, abuse controls, and provider opt-out. Implementation mechanism is a later decision. |
| Sponsored result presented as organic | Separate eligibility/ranking fields and explicit disclosure in every channel. |
| Sensitive calendar/contact/location exposure | Per-field publication policy, least privilege, coarse availability, authorization and audit. |
| Prompt injection/tool abuse | Server-side validation, allowlisted actions, least-privilege scopes, confirmation, audit, isolation. |
| Wrong identity or unauthorized write | Strong actor binding, delegated authorization, consent evidence, traceable outcome, and revocation. |
| Quote interpreted as booking | Distinct lifecycle states, explicit confirmation and user-visible terms. |
| Protocol churn inflates or distorts the product | No V1 protocol-only deliverable; later architecture compares isolation/replacement options only for a named approved channel. |
| Channel lock-in or lost attribution | Canonical first-party records, exportability, channel provenance, referral/conversion measurement. |

## Recommendation alternatives

| Alternative | Tradeoff |
|---|---|
| **Public-web/data foundation, protocol-agnostic** — recommended `PROPOSED` principle when public acquisition is in scope | Stable semantics and indexable content benefit human browsing and ordinary search; incremental AI work remains optional. Needs an approved acquisition hypothesis and maintenance cost estimate. |
| Public-web foundation without internal/agent AI | Preserves stable IDs, semantic HTML, SEO, and crawler policy while avoiding model/runtime and action-channel cost. Strong default if internal-AI value and partner demand are unproven. |
| Protocol/agent-first | May accelerate a concrete partnership, but adds churn, security, certification, support, and vendor dependence before marketplace semantics are validated. Not recommended without partner evidence. |

## Primary sources

### Google Search and AI discovery

- [AI features and your website](https://developers.google.com/search/docs/appearance/ai-features)
- [JavaScript SEO basics](https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics)
- [Dynamic rendering](https://developers.google.com/search/docs/crawling-indexing/javascript/dynamic-rendering)
- [Build and submit a sitemap](https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap)
- [Canonical URLs](https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls)
- [Structured data introduction](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data)
- [Structured data policies](https://developers.google.com/search/docs/appearance/structured-data/sd-policies)
- [LocalBusiness structured data](https://developers.google.com/search/docs/appearance/structured-data/local-business)
- [Review snippets](https://developers.google.com/search/docs/appearance/structured-data/review-snippet)
- [Event structured data](https://developers.google.com/search/docs/appearance/structured-data/event)
- [Multilingual and multi-regional sites](https://developers.google.com/search/docs/specialty/international/managing-multi-regional-sites)
- [Googlebot](https://developers.google.com/search/docs/crawling-indexing/googlebot)

### OpenAI discovery and integrations

- [Publishers and Developers FAQ](https://help.openai.com/en/articles/12627856-publishers-and-developers-faq)
- [OpenAI crawlers](https://developers.openai.com/api/docs/bots)
- [ChatGPT Search](https://help.openai.com/en/articles/9237897-chatgpt-search)
- [Local Services Request Quote specification](https://developers.openai.com/plugins/guides/local-services-request-quote-conversion-spec)
- [OpenAI plugins](https://developers.openai.com/plugins)
- [OpenAI commerce / ACP](https://developers.openai.com/commerce)
- [Buy it in ChatGPT](https://openai.com/index/buy-it-in-chatgpt/)
- [Product discovery in ChatGPT](https://openai.com/index/powering-product-discovery-in-chatgpt/)

### Open protocols

- [MCP specification](https://modelcontextprotocol.io/specification/2026-07-28)
- [A2A specification](https://a2a-protocol.org/latest/specification)
- [A2UI project](https://github.com/a2ui-project/a2ui)
- [Google developer guide to agent protocols](https://developers.googleblog.com/en/developers-guide-to-ai-agent-protocols/)
- [Google UCP guide](https://developers.google.com/merchant/ucp)
- [UCP project](https://ucp.dev/)
- [`llms.txt` proposal](https://llmstxt.org/)

## Reconsideration gates

Protocol implementation requires a bounded business case plus the applicable product, architecture/ADR, security, privacy, legal, operations, and commercial approvals. Revisit when an approved partner supplies access and qualified demand; RFQ/availability semantics stabilize; measured AI referrals cross an approved threshold; or an official protocol adds service-marketplace support in Superola's target markets.
