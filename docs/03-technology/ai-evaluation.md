# AI Evaluation — `D-13` Natural-Language Intent Mapping

> **Status:** `PROPOSED — DAVID APPROVAL REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1.**
>
> **Access date for every external source in this document: 2026-08-11.**
>
> **The recommendation is about whether to spend, not about which model to pick. No model provider is selected, and selecting one is not the decision in front of Superola.**

## 1. Gate status — stated first, because everything after it is conditional

**`D-13` is CLOSED. It is not closed pending a vendor evaluation. It is closed pending evidence that does not exist and cannot exist yet.**

`p03-decision-inputs.md` `D-13` states the prerequisite verbatim:

> *A recorded corpus of real zero-result and abandoned queries whose failure is demonstrably **interpretive** rather than **coverage**-driven.*

Four separate facts keep it shut. The fourth is new and was not previously in the repository.

| # | Obstacle | Status |
|---|---|---|
| 1 | **The corpus does not exist.** `SRC-006` (analytics and traffic) is NOT RECEIVED. There is no traffic evidence at all — not a thin corpus, none | Unsatisfied |
| 2 | **The instrumentation that would produce it is not built.** `search-architecture-requirements.md` §11 lists it as required V1 instrumentation: zero-result and abandoned-query logs with the constraint set attempted, plus supply counts per `Market` "so an interpretation failure can be told apart from a coverage failure". That instrumentation ships **with** V1; it does not precede it | Unsatisfied |
| 3 | **The corpus needs operating time after V1 ships.** Even with perfect instrumentation on day one, a corpus large enough to separate interpretive from coverage failure per `Market` and per category needs months of real traffic | Unsatisfied |
| 4 | **The corpus that opens the gate is itself gated.** Zero-result and abandoned-query logs contain **customer-typed free text**, which `security-privacy-architecture.md` §12 classifies **customer-private, assume-contains-contact-data**. `docs/07-research/ai-discoverability.md`'s privacy gate requires an approved data-flow and threat assessment *before any internal-AI experiment processes event or user data*, and mandates synthetic or explicitly approved de-identified data until it passes. **No document in this repository assigns that assessment to anyone** | **Not merely unsatisfied — not yet satisfiable** |

### The consequence P03 must record

**`D-13` is not answerable in P03 at all.** It is answerable at the earliest in a post-launch measurement phase. Any artifact that selects a model provider for this problem is selecting against an unstated hypothesis.

`p03-decision-inputs.md` says P03 must not *"answer `D-13` before the zero-result corpus exists"*. It does not state that the corpus **cannot** exist during P03. That time component is recorded here.

### What would open the gate — all of these, in order

| # | Condition | Owner | Status today |
|---|---|---|---|
| 1 | V1 ships with `search-architecture-requirements.md` §11 instrumentation intact | Engineering | Not built |
| 2 | Enough operating time to accumulate a corpus per `Market` | Time | Not started |
| 3 | An approved de-identification and lawful-basis decision for query text | Owner + counsel | **Not identified as a task anywhere** — now `Q-033` |
| 4 | The governed synonym and alias table shipped, and its residual failure measured | Engineering + Catalog governance | Not built |
| 5 | The guided structured entry flow shipped, and its residual failure measured | P04 + Engineering | Not designed |
| 6 | Residual failures classified as interpretive, exceeding an approved share of search sessions | Named owner — **unnamed today** | No share, no owner |
| 7 | Separate owner approval and separate budget | Owner | Not sought |

## 2. The structural finding that dominates the whole evaluation

`DB-08` and `search-architecture-requirements.md` §2 bind the mapper to emit *"the same structured constraint set the deterministic form produces, and nothing the form cannot produce"*, with unmapped constraints **rejected, not approximated**. That boundary is correct and this document endorses it without reservation.

It has a consequence no P02 document states:

> **The mapper's value ceiling is the form's expressiveness. Superola's V1 constraint set is deliberately tiny — a category or archetype reference, a place reference, category-scoped filter values, and a date that is context only. The maximum achievable value of a natural-language front end over that vocabulary is "typing a sentence instead of using a picker."**

This is a stronger argument against funding the mapper than any cost figure in §6. **The smaller the output vocabulary, the less a language model buys over a good form — and P02 made the vocabulary as small as it could, deliberately.**

## 3. The two cheaper alternatives — costed

`D-13`, `D-03`, `ADR-006`, and `search-architecture-requirements.md` §9 all require these be exhausted first. Neither is a new capability; both are already owed.

### (a) Governed synonym and alias table

**Already required as a data model.** `internationalization-architecture.md` §3 and `D-12` require *"localized labels and synonyms as data over stable language-neutral identifiers, with a versionable label set"*. Only the resolver and the content are incremental.

Mechanically: Unicode normalisation plus diacritic folding plus case folding (`norteño` → `norteno`) · light per-language stemming (`mariachis` → `mariachi`) · exact and prefix match against governed synonym rows · fuzzy match above a governed confidence threshold for typos · anything unresolved is **rejected and shown to the customer**, which §2 requires anyway.

| Line | Estimate | Note |
|---|---|---|
| Resolver, accent and plural normalisation, index | 3–6 developer-days | The index is the fiddly part, not the logic |
| Operator CRUD surface for synonyms | 2–4 developer-days | Deferrable to migration-seeded rows at launch (~0.5 day) |
| "Promote a zero-result term to a synonym" review queue | 1–2 developer-days | Rides the existing operator queue; do not build a second one |
| **Build total** | **5–10 developer-days** (3–5 with the operator surface deferred) | |
| Content seeding, launch cohort | 2–5 person-days, **not developer time** | ~5–20 surface forms per category × 2 languages; a 30–60 category cohort is 150–1,200 rows |
| Recurring maintenance | ~2–4 operator-hours/month at Pilot, rising with category count | `R-010` guarantees taxonomy change; every change needs synonyms |

The recurring cost is **operator, not engineering**, and `DB-05` already names taxonomy governance the highest-ranked operator cost driver in the product. This rides an existing queue rather than creating one — a materially different cost shape from a model, which creates a permanent engineering obligation (prompt versioning, harness runs, provider-drift monitoring) that never ends.

### (b) Guided structured entry flow

**Already required.** `DB-08` states it flatly: *"A structured search surface must exist and be sufficient alone."* The incremental cost over a plain filter form is progressive disclosure, type-ahead, and the relaxation interface.

| Line | Estimate | Note |
|---|---|---|
| Progressive disclosure — archetype → category → category-scoped filters | 4–7 developer-days | Filters are already per-`CategoryAttributeDefinition` and per-`Market` |
| Type-ahead over governed categories plus synonyms | 2–4 developer-days | **Not free** — an additional read path with its own latency budget on the primary store, which `D-01`/`D-03` must cost |
| Progressive relaxation with "which constraint was relaxed" disclosure | 2–4 developer-days | `search-architecture-requirements.md` §8 requires it regardless of this decision |
| **Build total** | **8–15 developer-days incremental**, plus P04 design time | |

A guided flow **eliminates interpretation for every constraint it exposes, by construction** — the customer picks a governed value, so there is nothing to interpret. It converts an *interpretation* problem into a *discoverability* problem, and that problem is answered by the type-ahead, which is answered by the synonym table. **(a) and (b) are one system.**

### What share of the problem each solves

Any single percentage would be invention. The decomposition instead:

| Plausible zero-result failure class | Synonym table | Guided flow |
|---|---|---|
| Surface-form variance — accent, plural, gender, regional term | **Solves essentially all of it.** In a Spanish/English market with owner-reported mariachi / mariachis / norteño / banda, very likely the largest *interpretive* class | Solves it via type-ahead over the same table |
| Vocabulary with no governed category behind it | **Solves nothing** — a taxonomy gap (governance) or a coverage gap (supply) | Surfaces the gap honestly, which beats silence |
| Compositional multi-constraint sentences | **Solves nothing** | **Solves it by construction** — each constraint is picked, not parsed |
| Place text that will not resolve | Solves nothing — this is `D-07` geocoding coverage | Solves it via governed-`Place` type-ahead |
| Genuine coverage failure — no supply | Solves nothing. **Neither does a model.** | Nothing solves this except supply acquisition |

**Combined: ~13–25 incremental developer-days plus 2–5 content days.** Against that, the model path needs the *same* labelled evaluation set (§7), *plus* the mapper, *plus* rate limiting, *plus* prompt versioning, *plus* the privacy gate, *plus* a permanent provider-drift monitoring obligation.

**The cheap alternatives are cheaper by a large multiple, and they are prerequisites for measuring whether a model would help at all.**

## 4. The non-model classification alternative — the sharpest technical point available

**The output space is closed.** A category or archetype identifier from a governed set of tens to low hundreds; a place reference from a governed `Place` table; filter values from `CategoryAttributeDefinition` enumerations; a date. Nothing else is emittable, because §2 forbids it.

**A closed output vocabulary is a classification and entity-resolution problem, not a generation problem.** Classification is one to three orders of magnitude cheaper, deterministic, explainable by construction, and testable with the same labelled set.

| Property | Rules and synonym pipeline | Model mapper |
|---|---|---|
| Variable cost per query | **~$0** | $0.00011 – $0.0132 (§6) |
| Latency | Sub-millisecond, in process | One network round trip. `NOT VERIFIED` for any provider on this task shape |
| Explainability — §6 of the search requirements | **By construction** — the matched row *is* the explanation | Requires a separate mechanism |
| Privacy gate | **Not triggered** — no data leaves the boundary | Full gate applies |
| Rate-limit attack surface | **None** | Launch blocker (§6) |
| Kill switch needed | **No — it is the deterministic path** | Yes |
| Quality regression risk | Changes only when you change it | **A provider can change the model under you with no code change on your side** |
| Compositional multi-constraint sentences | Weak | **Genuinely stronger** |
| Novel or unanticipated phrasing | Weak | **Genuinely stronger** |

**Tier 2 — a small fine-tuned classifier.** A multilingual sentence encoder plus a linear head over the governed category set handles paraphrase a synonym table misses, at near-zero inference cost, self-hostable, with no per-query billing and no privacy export. It needs labelled training data — **which is the same labelled evaluation set `D-13` demands anyway**.

### Where a model genuinely wins, stated precisely rather than dismissed

Pulling four distinct governed constraints out of one unstructured sentence — *"mariachi para una quinceañera en Santa Ana el 14 por menos de $1,200"*, where `quinceañera` implies an event type, `Santa Ana` needs place resolution, `el 14` is a date, and `$1,200` is a budget filter. A rules pipeline handles each token; composing them reliably and knowing which is which is where a model earns its cost.

**And the guided structured entry flow captures exactly that composition, with zero interpretation risk, zero variable cost, and zero privacy exposure. The model's one genuine advantage is the advantage alternative (b) was designed to remove.**

### A cost nobody had named

**Enumerating the governed taxonomy in the prompt or the JSON schema is what makes the model path expensive — and `R-010` guarantees the taxonomy changes.** Every taxonomy change invalidates the prompt cache (a byte change in the cached prefix invalidates everything after it) and potentially invalidates gold labels in the evaluation set.

`ADR-006` already rejects a separate search store partly because *"every taxonomy change is a full reindex"*. **The model mapper carries the isomorphic cost.** The alternative — let the model emit free text and resolve it deterministically downstream — is cheap, but then the synonym table is doing the real work and the model is a paraphraser. **Either way, the synonym table is the engine.**

## 5. Conditional model comparison — if and only if the experiment is later approved

Prices per **million tokens (MTok)**, text. Accessed **2026-08-11**. Every figure carries its official source.

| Provider / model | Input | Output | Structured-output guarantee | Official source |
|---|---|---|---|---|
| Anthropic `claude-haiku-4-5` | $1.00 | $5.00 | **Strongest verified language.** Constrained decoding: *"Always valid… Guaranteed field types and required fields… No retries needed for schema violations."* Plus `strict: true` tool use | [pricing](https://platform.claude.com/docs/en/about-claude/pricing) · [structured outputs](https://platform.claude.com/docs/en/build-with-claude/structured-outputs) |
| Anthropic `claude-sonnet-5` | $2.00 ⚠ | $10.00 ⚠ | Same | Same |
| Anthropic `claude-opus-5` | $5.00 | $25.00 | Same | Same |
| OpenAI `gpt-5-nano` | $0.05 | $0.40 | Strong — *"ensures the model will always generate responses that adhere to your supplied JSON Schema"*; strict function calling | [pricing](https://developers.openai.com/api/docs/pricing) · [structured outputs](https://developers.openai.com/api/docs/guides/structured-outputs) |
| OpenAI `gpt-5-mini` | $0.25 | $2.00 | Same | Same |
| Google `gemini-2.5-flash-lite` | $0.10 | $0.40 | **Weakest of the three majors. No conformance guarantee:** *"While output is syntactically correct JSON, always validate values in your application."* Also *"very large or deeply nested schemas may be rejected"* | [pricing](https://ai.google.dev/gemini-api/docs/pricing) · [structured output](https://ai.google.dev/gemini-api/docs/structured-output) |
| Google `gemini-3.5-flash-lite` | $0.30 | $2.50 | Same | Same |
| Groq `llama-3.1-8b-instant` | $0.05 | $0.08 | **`NOT VERIFIED`** | [Groq models](https://console.groq.com/docs/models) |
| Groq `openai/gpt-oss-20b` | $0.075 | $0.30 | **`NOT VERIFIED`** | Same |
| Together `gpt-oss-20B` | $0.05 | $0.20 | **`NOT VERIFIED`** | [Together pricing](https://www.together.ai/pricing) |
| Mistral `Ministral 3 (3B)` | $0.10 | $0.10 | **`NOT VERIFIED`** | [Mistral API pricing](https://mistral.ai/pricing/api) |
| AWS Bedrock | `NOT VERIFIED` | `NOT VERIFIED` | `NOT VERIFIED` | Not fetched |

> ⚠ **A dated price expiry, flagged because a blanket "prices change" qualifier does not cover a *known* increase.** The Sonnet-tier rates above are an **introductory rate that expires 2026-08-31** — twenty days after the access date — after which the standard rate applies and **every Sonnet-tier figure in §6, including the abuse row, rises by roughly 1.5×**. The Haiku and Opus tiers carry no such expiry and their figures reproduce exactly. This does not change the recommendation, which is not to spend at all; it is recorded so nobody quotes a Sonnet figure after August without re-checking.

> **Finding worth carrying: the cheapest options per token are exactly the ones whose structured-output guarantees and retention terms could not be verified.** For a task whose entire correctness property is *"emit a constraint set the domain can validate, or reject"*, an unverified schema guarantee is a material gap, not a footnote.

### Retention and privacy — the criterion that actually discriminates

| Provider | Default retention | Training on API data | Zero-retention option |
|---|---|---|---|
| Anthropic | *"Conversation content… is not retained by default."* Structured outputs is ZDR-**qualified**: prompts and outputs are not stored, but **the JSON schema is cached up to 24 hours since last use** | No, without express permission | **Yes** — ZDR, but requires contacting sales; enabled per organisation. Flagged content may be retained up to 2 years regardless ([source](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention)) |
| OpenAI | Abuse-monitoring logs *"up to 30 days, unless longer retention is required by law"* | *"not used to train or improve OpenAI models (unless you explicitly opt in)"* | **Yes** — *"subject to prior approval by OpenAI and acceptance of additional requirements"* ([source](https://developers.openai.com/api/docs/guides/your-data)) |
| Google (Gemini, paid tier) | Logged *"for a limited period of time, solely for detecting and preventing violations"* — exact window `NOT VERIFIED` | Paid tier: not used to improve products. **Free tier does — and human reviewers may read API input and output** | No documented equivalent found ([source](https://ai.google.dev/gemini-api/terms)) |
| Groq / Together / Mistral / Bedrock | `NOT VERIFIED` | `NOT VERIFIED` | `NOT VERIFIED` |

Three consequences:

1. **Zero data retention requires an enterprise sales conversation at both Anthropic and OpenAI.** Under the `ai-discoverability.md` privacy gate this is a **contract negotiation on the critical path of the experiment**, not a technical task.
2. **A free-tier Gemini key is disqualifying.** Google's own terms state free-tier content is used for product development and that human reviewers may read it. Search query text is customer-typed.
3. **No customer-typed value may ever appear in a schema, enumeration, or pattern.** Anthropic caches the schema for up to 24 hours. Governed taxonomy is public data and acceptable there; customer text is not.

### English and Spanish evidence

| Provider | Published Spanish evidence | Verdict |
|---|---|---|
| Anthropic | Per-language table, human-translated MMLU relative to English = 100%: **Spanish 98.2% (Claude Sonnet 4.5), 96.4% (Claude Haiku 4.5)** — Spanish is the strongest non-English language listed ([multilingual support](https://platform.claude.com/docs/en/build-with-claude/multilingual-support)) | Best published evidence of the three |
| OpenAI | Human-translated MMLU across 13 languages; **Spanish: GPT-5-thinking 0.910, GPT-5-main 0.881; GPT-5.2-thinking 0.913** ([GPT-5 system card](https://cdn.openai.com/gpt-5-system-card.pdf)) | Good, but published for flagship models — **not for the nano tier you would actually use** |
| Google | `NOT VERIFIED` — no per-language Spanish figure located on official pages | Gap |
| Open-weights hosts | `NOT VERIFIED` | Gap |

**Three cautions that matter more than the numbers:**

- **Every figure is translated-MMLU — academic multiple-choice knowledge.** None of it is evidence about mapping colloquial Mexican-American Spanish event-service vocabulary to a governed taxonomy. `norteño`, `banda`, `quinceañera`, `bautizo`, `padrinos` are register and regional dialect, not MMLU.
- **The published evidence covers the expensive tier; the affordable tier is unmeasured.** No provider publishes Spanish figures for its nano, lite, or 8B tier. Under `D-13`'s traffic-scaled economics you would ship the cheap tier, so **the published evidence does not cover the model you would ship.**
- **`DB-04`'s finding cuts here: locale does not follow country.** If the launch production locale is Spanish inside the United States — which the owner-reported mariachi/norteño/banda mix directly evidences — this is a **Spanish-primary** problem, and Spanish quality is the headline criterion rather than a secondary one.

### Portability, observability, fallback

- **Portability is good, and it is a genuine architectural win of the P02 boundary.** The mapper sits outside and in front of the domain, no module imports it, it persists no authoritative value. Swapping providers replaces one adapter behind an interface expressed in Superola's vocabulary. **The lock-in that matters is not the provider — it is the labelled evaluation set and the prompt corpus, and both are Superola's.**
- **Observability must be built.** No vendor dashboard answers `D-13`'s requirement for *"separately attributable measurement"*.
- **Fallback is trivially correct, and it is the boundary's second win.** On timeout, error, refusal, rate limit, cost-ceiling breach, or unmappable input the answer is always the same: show the structured form. **Set an aggressive timeout — on a top-of-funnel action the fallback is strictly better than a slow success.**

## 6. Cost model — arithmetic shown, assumptions labelled

### Assumptions

Per the standing rule that no number appears without a source and a volume scenario. These are **assumptions**, not measurements.

| ID | Assumption | Basis |
|---|---|---|
| `AI-A1` | Stable prompt prefix = **2,000 input tokens** — instructions, governed category and synonym vocabulary for the launch cohort, filter enumerations, JSON schema | Estimated. **Grows with taxonomy size, and `R-010` guarantees growth** |
| `AI-A2` | User query = **30 input tokens** | Estimated from typical search-query length |
| `AI-A3` | Output = **120 tokens** — the structured constraint set plus a rejected-constraints list | Estimated. **Assumes no extended thinking**, which would multiply output several-fold |
| `AI-A4` | **One model call per natural-language search.** No retries, no clarification turn | Optimistic; a clarification turn doubles it |
| `AI-A5` | Uncached baseline | Caching treated separately below |

Per query: **2,030 input tokens, 120 output tokens.**

### Per-query cost

| Model | Input | Output | **Per query** |
|---|---|---|---|
| `llama-3.1-8b-instant` | 2,030 × $0.05 / 1e6 = $0.00010150 | 120 × $0.08 / 1e6 = $0.00000960 | **$0.0001111** |
| `gpt-5-nano` | 2,030 × $0.05 / 1e6 = $0.00010150 | 120 × $0.40 / 1e6 = $0.00004800 | **$0.0001495** |
| `gemini-2.5-flash-lite` | 2,030 × $0.10 / 1e6 = $0.00020300 | 120 × $0.40 / 1e6 = $0.00004800 | **$0.0002510** |
| `gpt-5-mini` | 2,030 × $0.25 / 1e6 = $0.00050750 | 120 × $2.00 / 1e6 = $0.00024000 | **$0.0007475** |
| `claude-haiku-4-5` | 2,030 × $1.00 / 1e6 = $0.00203000 | 120 × $5.00 / 1e6 = $0.00060000 | **$0.0026300** |
| `claude-sonnet-5` | 2,030 × $2.00 / 1e6 = $0.00406000 | 120 × $10.00 / 1e6 = $0.00120000 | **$0.0052600** |
| `claude-opus-5` | 2,030 × $5.00 / 1e6 = $0.01015000 | 120 × $25.00 / 1e6 = $0.00300000 | **$0.0131500** |

### Monthly cost at the volume scenarios

**Worst case defined:** the natural-language entry point is the **default** entry point — every search passes through it, not only opted-in searches — receiving anonymous public traffic under the stated acquisition hypothesis. Modelled at **10 × Growth = 6,000,000 calls/month**.

| Model | Pilot 8,000 | Early 80,000 | Growth 600,000 | **Worst case 6,000,000** |
|---|---|---|---|---|
| `llama-3.1-8b-instant` | $0.89 | $8.89 | $66.66 | **$666.60** |
| `gpt-5-nano` | $1.20 | $11.96 | $89.70 | **$897.00** |
| `gemini-2.5-flash-lite` | $2.01 | $20.08 | $150.60 | **$1,506.00** |
| `gpt-5-mini` | $5.98 | $59.80 | $448.50 | **$4,485.00** |
| `claude-haiku-4-5` | $21.04 | $210.40 | $1,578.00 | **$15,780.00** |
| `claude-sonnet-5` | $42.08 | $420.80 | $3,156.00 | **$31,560.00** |
| `claude-opus-5` | $105.20 | $1,052.00 | $7,890.00 | **$78,900.00** |

Worked example: `claude-haiku-4-5` at Growth = 600,000 × $0.00263 = $1,578.00.

### The abuse row — `D-13`'s rate-limiting clause, quantified

One unremarkable script from one machine sustaining **10 requests per second** against an anonymously reachable endpoint for 30 days = 10 × 86,400 × 30 = **25,920,000 calls**.

| Model | Cost of one un-rate-limited month |
|---|---|
| `gpt-5-nano` | **$3,875** |
| `gemini-2.5-flash-lite` | **$6,506** |
| `claude-haiku-4-5` | **$68,170** |
| `claude-sonnet-5` | **$136,339** |
| `claude-opus-5` | **$340,848** |

**No revenue is generated. There is no marketplace effect. The amplification factor is bounded only by the attacker's patience.** This is why `D-13` calls rate limiting a launch blocker.

### The prompt-caching finding — counter-intuitive, and worth stating

Caching cuts the 2,000-token prefix to a cache-read rate. For `claude-haiku-4-5`: 2,000 × $0.10/1e6 + 30 × $1.00/1e6 + $0.0006 = **$0.00083/query against $0.00263 uncached, a ~68% saving.**

**But caching is a write premium before it is a discount, with a short default time-to-live.** At Pilot volume, 8,000 searches/month ≈ 11/hour ≈ one every 5.5 minutes — approximately the default TTL. Most queries would pay a cache *write* and few would land a read.

> **Prompt caching makes the pilot more expensive, not cheaper. It pays only from roughly Early Marketplace volume upward, where traffic is continuous enough to keep the prefix warm — and it is defeated entirely by a taxonomy change, which `R-010` guarantees.**

### Cost ceiling and rate limiting — preconditions of approval

Items 1–4 are non-negotiable. The endpoint must not ship without them.

1. **An application-enforced hard monthly spend ceiling that flips the kill switch.** Not a vendor-console alert — a control in Superola's own code. On breach the endpoint returns the structured form, and the product stays fully functional.
2. **Per-IP and per-session token buckets**, strictest on anonymous traffic.
3. **Per-query input and output caps** — maximum query length and a `max_tokens` on the response — so no single pathological input costs 100× the modelled figure.
4. **A short timeout with the deterministic form as fallback.**
5. **A normalised-query result cache with a governed TTL.** The head of a category-search query distribution is extremely concentrated. Note what this is: a deterministic lookup table mapping normalised text to a constraint set. **That is the non-model alternative arriving through the back door, and it should be read as evidence.**
6. **An established session, preferably a verified customer, before the entry point is reachable.** The cheapest control of all — **and it directly conflicts with the public-acquisition hypothesis.** Name the conflict rather than paper over it: the mapper is most expensive precisely on the traffic the acquisition strategy exists to attract.
7. **Bot mitigation through the single enforcement point required by `D-09`/`DB-13`** — never a separate rule that can silently contradict the approved crawler policy.
8. **Separately attributable measurement** — calls, tokens, cost, rejection rate, fallback rate, latency, locale, prompt version.

## 7. Evaluation harness and prompt management

`D-13` calls this *"real work with no user-visible output"*. It is, and it is larger than the mapper.

| Component | Developer-days | Note |
|---|---|---|
| De-identified query-corpus extraction | 2–4 | **Blocked on a lawful-basis decision assigned to nobody** — `Q-033` |
| Labelling ~8 classes × 2 languages × 25–50 items = **400–800 items** | 5–10 **person**-days, roughly half not developer time | Needs a **bilingual, domain-literate** labeller. Needs double-labelling on a sample, or the "gold" is one person's opinion |
| Harness runner and per-class scoring | 5–8 | Exact constraint-set match, per-field precision and recall, and **rejection accuracy — the single most important metric**, because a mapper that approximates instead of rejecting violates the §2 boundary and silently corrupts eligibility |
| Adversarial suite | 2–3 | Injection attempting to emit a constraint the form cannot produce, a non-existent category identifier, or the taxonomy itself. **Blast radius is small because Discovery validates every emitted constraint against governed data — a genuine architectural win of the P02 boundary** |
| Prompt and version management as deployable artifacts | 3–5 | Prompts versioned in the repository; prompt version stamped on every call and every measurement event; harness runnable against a version in the pipeline; rollback without an application redeploy |
| Release-blocking mechanics | 2–3 plus recurring compute | Per-class pass bars, regression tolerance, a required pipeline check, **plus a scheduled out-of-band run** |
| **Total** | **19–33 developer-days engineering plus 5–10 person-days labelling** | **≈ 5–8 developer-weeks before a customer sees anything** |

The eight labelling classes: exact match · surface-form variance · compositional multi-constraint · incomplete intent · **unsupported constraint** (date-as-filter, availability guarantee, price guarantee — things the form *cannot* produce and the mapper must therefore **reject**) · out-of-taxonomy · adversarial and injection · nonsense.

**Three findings on this work:**

1. **Most of it is not a model cost.** The labelled bilingual query set is required by `search-architecture-requirements.md` §11 trigger 2 *before any relevance claim can be measured at all*, on every path. **It should be funded first and independently of this decision.** Genuinely model-specific: the adversarial suite, prompt versioning, provider-drift monitoring, and the release gate — roughly 7–11 developer-days.
2. **The harder regression case is one with no release.** A provider can change a model under you with no code change on your side, and a pipeline-only gate never fires. **The scheduled out-of-band run against a pinned model identifier is mandatory, and it is a permanent recurring cost with no equivalent on the deterministic path.**
3. **Re-labelling recurs.** `R-010` guarantees taxonomy change, and every change potentially invalidates gold labels.

## 8. AI outside the search critical path — verdict on each

Criteria applied: is it on a critical path? does a human still review the output (if yes, the saving is smaller than it looks)? what is the privacy classification?

**Standing constraint throughout:** request free text and conversation message text are **customer-private, assume-contains-contact-data** (`security-privacy-architecture.md` §12) and **must never be exported to analytics, a search read path, a notification body, or a model provider.**

| Use | Verdict | Reasoning |
|---|---|---|
| **Taxonomy mapping assistance during a future legacy import (P05)** | **CONDITIONAL YES — the only genuine win on this list** | Not on a critical path; out-of-band and batch. `DB-03` states taxonomy mapping is *"per distinct legacy string, not per record"* — hundreds to low thousands of strings, not 43,000 records. **Drafting is most of the work and human review is fast, which is not true of any other item here.** Gated behind `G-09`; only for distinct *category strings* after a personal-data check; never raw records. `A-022` means legacy strings may carry natural persons' data |
| **Operator-queue triage assistance** | **NO for V1** | Every operator case is decided by a human by definition, so AI can only reorder. `security-privacy-architecture.md` §10 already rejects automated abuse detection *"at zero volume with no measured abuse"*, and triage has the identical shape — buying ordering for a queue one person can read end to end. Cases touch request and conversation text, so it also crosses the privacy gate outright. Reconsider when queue depth exceeds one operator-day and the backlog-age stop rule is breached |
| **Content-moderation pre-screening** | **CONDITIONAL YES, narrowly — and it contains a trap** | **The trap: if pre-screening sits between upload and publication it is on the publication path, and a model outage becomes a publication outage.** Must be async, must not gate publication latency, must degrade to "queue for human review". Yes for provider-uploaded media and provider narrative only. **NO for request or conversation text.** Use a purpose-built moderation classifier, not a general model — cheaper, faster, with a published error profile. Note: **if `D-06`'s link-out alternative is chosen for performers, there may be no hosted media to moderate at all** |
| **Provider profile-completion suggestions** | **NO — the AI version is worse than the free version** | It generates provider-authored public content that Superola publishes. `R-011` warns thin or duplicate public pages *"convert a discovery strategy into search-quality and maintenance debt"*, and `ai-discoverability.md` requires people-first differentiated content with no mass thin pages. **An AI that fills profiles produces exactly that, at scale, on the surface carrying the acquisition hypothesis.** It also creates a truthfulness problem: machine-suggested claims a provider clicks through become claims Superola publishes. **The free alternative is strictly better — prompt the provider about which governed fields are missing. That is a completeness check V1 already has** |
| **Spanish/English translation of governed labels** | **MARGINAL** | Not on a critical path; build-time and governance-time only. Human review mandatory — **a mistranslated category label is a permanent discovery defect that silently misroutes an entire category's demand in the second locale.** Volume is tens to low hundreds of labels; a human does that in hours. **The only item here with zero privacy exposure.** Use as a drafting aid; do not build a pipeline |

### 8.1 AI-assisted request intake / form prefill — a bounded future experiment for P04

**Added in P03.1. It reverses nothing above and nothing in §10.** The recommendation against a natural-language intent mapper on the **search** path stands unchanged, and no AI infrastructure is proposed for the V1 critical path. **What this section corrects is a wording problem: P03 read as though AI has no useful product role in Superola at all, and that is a stronger claim than the evidence supports.**

**The shape.** A customer writes free text at the point of *submitting a request*:

> *"I need a mariachi for a quinceañera in Houston next Saturday for 150 people, around $1,500."*

The experiment would: extract **known structured request fields** — category, `Place`, event date, guest count, budget band — **show them to the user**, let the user **confirm or correct** every one, and then hand off to the ordinary deterministic marketplace flow. **The model proposes; the form disposes.**

**What it explicitly is not.** Not authoritative ranking. Not eligibility. Not a replacement for the structured form, which `DB-08` requires to *"exist and be sufficient alone"*. **Nothing may depend on the model's output**, and a model outage degrades the flow to typing, not to failure.

**Why this is a materially different proposition from the §1–§7 mapper**, and the difference is structural rather than a matter of degree:

| | Search-path intent mapper (**recommended against**) | Assisted intake (**bounded experiment**) |
|---|---|---|
| Attaches to | **searches** — 8,000 / 80,000 / **600,000** per month | **submitted requests** — 300 / 3,000 / **20,000** per month |
| Reachable by | **anonymous public traffic** — the acquisition hypothesis itself | an **authenticated, rate-limited, intentional** action |
| Unbounded-cost exposure | **USD $3,875–$340,848 for one un-rate-limited month** | bounded by RFQ volume and an application-enforced ceiling |
| Correction path | none — the user sees results, not an interpretation | **the user sees the extracted fields and fixes them** |
| Failure mode | silently wrong results | visibly wrong fields the user corrects |

**At Growth there are 30× fewer submitted requests than searches.** That is a difference in kind, not a discount.

**Parametric cost only — no figure here is committed and none appears in any total in `cost-model.md`.** At USD $0.01 per submitted request: USD $3 / USD $30 / **USD $200** per month. At USD $0.05: USD $15 / USD $150 / **USD $1,000**. Comparison in `cost-alternatives.md` §9.

**Preconditions, all of which are already required elsewhere and none of which this experiment may waive:**

1. **A hard application-enforced cost ceiling**, not a vendor console alert — §6's distinction, unchanged.
2. **Request free text is customer-private, assume-contains-contact-data** (`security-privacy-architecture.md` §12). **It may not leave Superola without a resolved lawful basis and a verified zero-retention term.** `Q-033` is unresolved and blocks this exactly as it blocks the mapper.
3. **Optional and skippable.** The structured form remains the primary path and must be sufficient alone.
4. **The guided structured entry flow ships first** (`ai-evaluation.md` §3(b)). It is a prerequisite, not an alternative — **you cannot measure whether prefill helps against a baseline that does not exist.**

**Who decides: P04.** This is a UX question — does assisted intake measurably reduce request-composition abandonment (`R-022`) against the guided form? **If P04 does not find an interaction problem worth testing, the experiment does not happen, and P03.1 does not prejudge that.**

### Net finding

One plausible yes behind a gate that may never open, on a baseline where the work may not exist. One narrow conditional yes that should use a purpose-built classifier rather than a general model. One marginal. Two nos. **And one bounded, optional, capped experiment handed to P04 rather than to P03 — because it is a question about an interaction, not about a model.**

**Measured against "is it genuinely cheaper than not doing it", AI is not a meaningful cost saver anywhere in Superola V1. The largest AI-adjacent saving available is not doing something at all — `D-06`'s link-out alternative, which removes the media-moderation cost by removing the media.**

**One clarification P03.1 adds, because the sentence above is about *cost* and was being read as a verdict on *value*: "not a cost saver" is not the same as "no useful product role."** §8.1's assisted intake would not be funded to save money — **it would be funded to reduce request-composition abandonment**, which is a product outcome, not a cost line. **P03 should not be summarised as "AI is useless to Superola."** It should be summarised as: **no AI belongs in the V1 critical path, no AI is worth spending on to save money, and the first place worth *testing* is assisted intake — not ranking.**

## 9. Future agent readiness — nothing further is needed in V1

**Stated plainly: NOTHING FURTHER IS NEEDED IN V1.**

P02 already carries the entire structural preparation, and every piece was independently justified by a Phase-1 need — which is exactly `system-architecture.md` §6's admissibility test, and exactly why none of it is protocol inflation.

| Preparation | Where it already lives | Independent Phase-1 justification |
|---|---|---|
| Stable public identifiers | `ADR-012`; `domain-model.md` §1.4 governed `Place` identity; `DB-11` | Stable public URLs, retained redirect history, correction path |
| Per-field publication policy | `ADR-012` allowlist-derived versioned projection | `R-027`: a denylist projection leaks the next private field added |
| Freshness and provenance on public projections | `PublishedSnapshot` re-versioned synchronously | Public page correctness and moderation-removal propagation |
| One application layer as the single write path | `ADR-011`, `ADR-001` | `R-026`: authorization at the delivery edge is silently absent in a second channel |

**No V1 deliverable is needed for MCP, A2A, A2UI, UCP, ACP, `llms.txt`, any protocol adapter, any public versioned contract, or any data feed.** `R-012` and the anti-inflation rule bind: *no V1 deliverable may exist whose only consumer is an emerging protocol.*

**The trigger — all three parts together:** a **named, approved distribution channel or partner** with (a) **confirmed access** — actual admitted access, not a published specification; (b) **measurable economics** — a demand estimate and a cost per qualified request; and (c) **target-market availability**.

The closest current candidate, OpenAI's Local Services Get Quote conversion specification, satisfies **none** of the three today. `ai-discoverability.md` classifies it `DESIGN-FOR`, implementation `FUTURE`.

**Two ordering constraints when the trigger fires:**

- **`Q-027` before `Q-015`.** The deletion path for surfaces Superola does not control must exist before any crawler or agent channel is approved. An agent channel that caches Superola content is a third-party surface.
- **Read-only first, through the existing seams** — the same application layer and the same disclosure decision (`ADR-010`), never a parallel path.

## 10. Recommendation

> ## `PROPOSED — DAVID APPROVAL REQUIRED`: **DO NOT SPEND on a natural-language intent mapper.**
>
> Not "not yet, and here is a cheap model". **Do not spend.** No provider is recommended, because provider selection is not the decision in front of Superola.

The reasoning, compressed:

1. **The evidence that would justify the spend does not exist, cannot exist before V1 launches, and is additionally blocked by an unassigned privacy decision** (§1).
2. **The two cheaper alternatives are already required by P02 and cost 13–25 incremental developer-days combined.** They are not alternatives to weigh against a model — they are prerequisites for measuring whether a model would help (§3).
3. **The output vocabulary is closed and deliberately tiny, making this a classification problem, and the mapper's ceiling is the form's expressiveness.** The one thing a model genuinely does better is what the guided flow removes (§4).
4. **Variable cost scales with traffic on the highest-volume, lowest-intent action in the product, and the acquisition hypothesis is anonymous public traffic.** One un-rate-limited month costs $3,875 to $340,848, with zero marketplace effect (§6).
5. **The harness is 5–8 developer-weeks with no user-visible output** — and most of it is owed on every path, so it should be funded independently of this decision (§7).

### The spend order recommended instead

| # | Spend | Why | Cost |
|---|---|---|---|
| **0** | Build the §11 instrumentation V1 already requires — zero-result and abandoned-query logs with the constraint set attempted, plus supply counts per `Market` | **The only thing that can ever open `D-13`'s gate.** Also already required for the `D-03` triggers, so it is not new spend | Already in scope |
| **1** | Assign the de-identified query-corpus decision — owner, lawful basis, retention | **Currently assigned to nobody, and it blocks everything downstream.** `Q-033` | Owner plus counsel time |
| **2** | Governed synonym and alias table with accent and plural normalisation | Already required as a data model by `D-12`; named as the cheap alternative by `ADR-006`, `D-03`, `D-13` | 5–10 developer-days plus 2–5 content days |
| **3** | Guided structured entry flow with type-ahead | Baseline already required by `DB-08` — *"must exist and be sufficient alone"* | 8–15 developer-days plus P04 design |
| **4** | Build the labelled bilingual query set | Required by §11 trigger 2 before **any** relevance claim, on any path | 5–8 developer-days plus 5–10 labelling days |
| **5** | **Read the corpus.** Classify each zero-result as interpretive or coverage | The decision point. **If coverage dominates — which the owner's own evidence of broken legacy category and location data (`R-010`, `DB-03`) makes more likely than not — the money belongs in supply acquisition and the answer to `D-13` is permanently no** | Analysis time |
| **6** | *Only if* interpretive failures survive steps 2 and 3 above an approved share: evaluate the **non-model classifier** | Closed output vocabulary; no privacy gate; no rate-limit surface; no provider drift | — |
| **7** | *Only if* the classifier is insufficient: a **small-model** mapper, with every control in §6 shipped first | Last option, not first | — |

**The sentence to carry:** *Superola should spend on measuring whether an interpretation problem exists, not on solving one it has not observed.*

## 11. Reconsideration trigger

All of the following, together, as stated in §1's gate table. The operative bar is `DB-08`'s, which is the strictest of the three statements in the repository: a recorded corpus of real zero-result and abandoned queries whose failure is demonstrably **interpretive** rather than **coverage**-driven, **exceeding an approved share of search sessions**, measured **after** the governed synonym table and the guided structured entry flow have shipped and their residual failure has been measured — plus separate owner approval and separate budget.

## 12. Contradictions and omissions found in P02

| ID | Finding | Severity | Disposition |
|---|---|---|---|
| `AI-C1` | **The corpus that opens `D-13`'s gate is itself gated by `D-13`'s own privacy rule, and no document assigns the required assessment.** The prerequisite is not merely unsatisfied — it is not yet satisfiable | HIGH | New question `Q-033` |
| `AI-C2` | **The mapper's value ceiling is bounded by the form's expressiveness, and P02 made that expressiveness deliberately minimal.** `DB-08` is correct to bind it; no document states the consequence, which is the strongest argument against ever funding the mapper | HIGH — omission | Recorded in §2 |
| `AI-C3` | **`R-010` taxonomy churn is a model-mapper cost nobody named.** `ADR-006` rejects a separate search store partly because every taxonomy change is a full reindex; the mapper carries the isomorphic cost — prompt-cache invalidation and gold-label invalidation | MEDIUM-HIGH — omission | Recorded in §4 and §6 |
| `AI-C4` | **The public-acquisition hypothesis carries TWO unbounded variable-cost lines, not one.** `D-06` names served bytes per anonymous public view as *"the one cost that grows with acquisition success rather than with revenue-bearing requests"*; `D-13` says the same of per-query mapper cost. **Each is written as if it were the sole exception, and no document models them together** | MEDIUM-HIGH — omission | New risk `R-033`; `cost-model.md` must model them jointly |
| `AI-C5` | **`D-13` cannot be answered in P03 at all.** The gate has a time component no document states | MEDIUM | Recorded in §1 |
| `AI-C6` | Trigger strength differs across documents. `integration-architecture.md` states it as the corpus showing interpretive failure; `DB-08` additionally requires *"exceeding an approved share of search sessions"*. **The operative bar is `DB-08`'s** | LOW — wording | Recorded in §11 |

## 13. Unresolved questions handed forward

1. **Who owns the de-identified query corpus, under what lawful basis, with what retention?** Blocks `D-13`'s prerequisite. `Q-033`.
2. **What numerically is "an approved share of search sessions", and who sets it?** `DB-08` requires the share; no owner and no value exist.
3. **Is the launch production locale Spanish or English?** `DB-04`'s *locale does not follow country* materially changes both the synonym-table content cost and the evaluation burden. Under a Spanish launch locale this is a Spanish-primary problem, and the published Spanish evidence covers flagship models rather than the tier that would ship.
4. **Does the cost ceiling live in the application or in the vendor console?** Only an application-enforced ceiling is a control; a console alert is a notification.
5. **Does `D-06`'s link-out alternative for performer media remove the media-moderation question entirely?** If chosen, §8's moderation item largely disappears. The two decisions should be taken together.

## 14. Confidence and what could not be verified

| Claim | Confidence |
|---|---|
| The gate is closed; the corpus does not exist; `D-13` is unanswerable in P03 | **HIGH** — directly from the repository; `SRC-006` NOT RECEIVED |
| Both cheap alternatives are already required by P02 and near-free incrementally | **HIGH** — `D-12`, `DB-08`, `ADR-006` state it explicitly |
| The closed output vocabulary makes this a classification problem | **HIGH** — follows from the rejection rule |
| Rate limiting is a launch blocker | **HIGH** — arithmetic in §6 on verified prices |
| Nothing further is needed for agent readiness in V1 | **HIGH** — `R-012` and the four already-justified preparations |
| Prices, structured-output guarantees, retention terms | **MEDIUM-HIGH** — verified against official pages on 2026-08-11; **these change frequently and must be re-verified before any decision** |
| Developer-day estimates | **MEDIUM** — informed ranges, not measurements. **Not usable for a commitment** |
| What share of the problem each alternative solves | **LOW** — nobody can know without the corpus, **which is the entire point of the gate** |

**Explicitly `NOT VERIFIED`:** latency for any provider on this task shape · Groq, Together, Mistral and Bedrock retention, no-training and zero-retention terms — **material, because the cheapest per-token options are exactly the ones whose privacy terms are unverified** · whether hosted open-weights endpoints offer schema-enforced structured output equivalent to Anthropic's or OpenAI's · Amazon Bedrock per-token pricing · Google's exact paid-tier retention window in days · published per-language Spanish figures for Google, and for **any** provider's nano/lite/8B tier — **the tier that would actually ship** · Anthropic and OpenAI zero-retention eligibility criteria beyond "sales approval".

## Sources

All accessed **2026-08-11**.

[Anthropic pricing](https://platform.claude.com/docs/en/about-claude/pricing) · [Anthropic structured outputs](https://platform.claude.com/docs/en/build-with-claude/structured-outputs) · [Anthropic API and data retention](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention) · [Anthropic multilingual support](https://platform.claude.com/docs/en/build-with-claude/multilingual-support) · [OpenAI pricing](https://developers.openai.com/api/docs/pricing) · [OpenAI structured outputs](https://developers.openai.com/api/docs/guides/structured-outputs) · [OpenAI data controls](https://developers.openai.com/api/docs/guides/your-data) · [GPT-5 system card](https://cdn.openai.com/gpt-5-system-card.pdf) · [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing) · [Gemini structured output](https://ai.google.dev/gemini-api/docs/structured-output) · [Gemini API terms](https://ai.google.dev/gemini-api/terms) · [Groq models](https://console.groq.com/docs/models) · [Together AI pricing](https://www.together.ai/pricing) · [Mistral API pricing](https://mistral.ai/pricing/api)

Repository inputs: `docs/02-architecture/p03-decision-inputs.md` (`D-13`) · `decision-branches.md` (`DB-08`, `DB-04`, `DB-05`) · `search-architecture-requirements.md` §2, §6, §8, §9, §11, §12 · `adr/ADR-006`, `ADR-010`, `ADR-011`, `ADR-012` · `integration-architecture.md` · `internationalization-architecture.md` §3 · `security-privacy-architecture.md` §10, §12 · `docs/07-research/ai-discoverability.md` · `docs/05-roadmap/risks.md` (`R-010`, `R-011`, `R-012`, `R-019`, `R-026`, `R-027`) · `docs/00-context/assumptions.md` (`A-022`) · `SRC-013`.
