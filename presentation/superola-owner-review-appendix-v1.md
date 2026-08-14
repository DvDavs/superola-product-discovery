# Superola Owner Review v1 — Appendix Source

> **Status:** `PROPOSED — OWNER VALIDATION REQUIRED`
>
> Use only when the owner asks for depth. English is visible slide copy; Spanish notes support David. Appendix numbering is independent from the 16-slide core deck.

## Appendix A1

**Title**

Evidence boundaries: what each source can support

**Owner-facing English text**

| Source | Supports | Does not prove |
|---|---|---|
| Owner interview | Owner goals, concerns, and hypotheses | Audited counts or accepted scope |
| Public platform | Visible pages, labels, examples, and public behavior | Database state, ownership, consent, private workflows |
| Public samples | Signals worth investigating | Corpus-wide percentages |
| David's product/technical work | Recommendations, options, costs under assumptions | Owner approval or implementation authorization |

**Recommended visual**

A four-row source-to-claim boundary diagram. Use icons sparingly and preserve the “supports / does not prove” contrast.

**David speaker notes in Spanish**

Usá esta slide si el owner pregunta de dónde sale una conclusión. La entrevista confirma lo que el owner dijo, no que el número esté auditado. Una muestra pública prioriza una pregunta; no define todo el corpus. Y mi trabajo técnico propone, no aprueba.

**Evidence / source notes**

- `docs/00-context/glossary.md`
- `docs/00-context/interview-evidence.md`
- `docs/00-context/source-register.md`
- Public audit §1.3

**Decision or takeaway**

Confidence comes from matching each claim to the kind of evidence that can actually support it.

---

## Appendix A2

**Title**

Public legacy observations to validate internally

**Owner-facing English text**

- 43,361 public listing URLs; relationship to ~43,000 owner-reported registrations unknown.
- 47 of 53 sampled pages referenced the same owner identifier; this is a concentration signal only, and internal ownership/control remains unknown.
- 11 of 33 sampled listings had no public address or coordinates.
- Repeated city centroids and one location roughly 600 km from its name.
- 118 public categories and 90 tags include duplicates, misspellings, and off-domain terms.
- Public phone disclosure can move activity outside Superola's measurement.
- “Solicitud de Reserva” publicly renders contact intake; internal booking state unknown.
- No current public paid listing/subscription product observed; history unknown.

**Recommended visual**

A vertical evidence ledger with a visible “public observation” label and a matching “internal question” column.

**David speaker notes in Spanish**

No leas esto como acusación. Cada punto se transforma en una pregunta de auditoría: qué representa la URL, quién controla el registro, qué consentimiento existe, si el dato está activo, qué capacidad privada hay y qué actividad se pierde por teléfono.

**Evidence / source notes**

- `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md`
- `docs/00-context/product-context.md`

**Decision or takeaway**

Internal access should test the highest-risk unknowns first, not attempt an immediate bulk migration.

---

## Appendix A3

**Title**

One product, one deployable system, clear internal boundaries

**Owner-facing English text**

Superola can begin as one operational unit with clear internal areas for:

Identity · Provider supply · Catalog · Discovery · Requests · Conversations · Notifications · Trust · Monetization · Legacy boundary

These are responsibility boundaries—not separate microservices.

Split only when measured scaling, legal isolation, team ownership, or transaction requirements justify it.

**Recommended visual**

One large system shape divided into labeled domains, with a single deployment arrow. Show external services outside. Do not draw ten separate servers.

**David speaker notes in Spanish**

La recomendación técnica es empezar con un solo artefacto operable, no con microservicios. Los límites internos protegen ownership de datos y responsabilidades. Si más adelante una parte escala distinto, exige aislamiento legal o un equipo separado, hay triggers concretos para dividirla.

**Evidence / source notes**

- `docs/02-architecture/domain-map.md`
- `docs/02-architecture/system-architecture.md`
- `docs/02-architecture/adr/ADR-001-single-deployable-modular-application.md`

**Decision or takeaway**

Keep today's operating model small while preserving evidence-based extension points.

---

## Appendix A4

**Title**

Technology choices favor maintainability and measured upgrades

**Owner-facing English text**

Current proposal:

- managed application platform;
- PostgreSQL as the system of record;
- database-backed search first;
- durable background work in the same store;
- low-egress media storage;
- server rendering with progressive enhancement and bounded client interactions.

Add specialized search, queues, client complexity, or distributed services only when measured evidence crosses a defined trigger.

No technology is adopted yet.

**Recommended visual**

A stable core stack with four outward arrows labeled by reconsideration triggers: search latency/relevance, backlog, interaction complexity, and organizational/legal separation.

**David speaker notes in Spanish**

Acá evito una lista de marcas. La decisión importante es empezar con infraestructura administrada, una fuente de verdad y complejidad acotada. La tecnología cambia cuando una medición concreta lo exige, no por moda. La recomendación de rendering ya recibió evidencia de UX, pero sigue propuesta y necesita aprobación técnica.

**Evidence / source notes**

- `docs/03-technology/technology-evaluation.md`
- `docs/03-technology/p03-decision-reconciliation.md`
- `docs/02-architecture/adr/ADR-020-web-rendering-strategy-provisional-until-p04.md`

**Decision or takeaway**

The stack is a reversible technical proposal, not an owner decision or a procurement commitment.

---

## Appendix A5

**Title**

Cost figures are scenario comparisons—not budgets

**Owner-facing English text**

| Stage | Assumed published providers | Technical bill | Illustrative human sensitivity |
|---|---:|---:|---:|
| Pilot | 150 | USD $75.98 | ~USD $578 |
| Early | 1,500 | USD $192.99 | ~USD $2,592 |
| Growth | 10,000 | USD $560.79 | ~USD $14,298 |

Human assumptions: USD $75/hour technical work; USD $40/hour marketplace operations. Workload volumes are unmeasured assumptions.

Excluded: booking/payment, legacy migration, marketing, content, legal advice, and unapproved capabilities.

Technical prices verified **2026-08-11**; reverify before purchase.

**Recommended visual**

A restrained table with a strong vertical divider between technical bill and human sensitivity. Keep the rates, unmeasured-workload qualifier, and verification date beside the figures. Add a footer: “Never add these into one headline.”

**David speaker notes in Spanish**

Los totales con centavos sirven para reconciliar con la fuente aritmética. En la slide principal conviene redondear. Los escenarios de proveedores y tráfico son supuestos comparativos. El standby de base de datos de Growth contiene la mayor cifra no verificada y debe revisarse antes de presupuestar.

**Evidence / source notes**

- `docs/03-technology/cost-model.md` §1.1
- `docs/03-technology/cost-alternatives.md`
- Price verification date: 2026-08-11; authentication facts: 2026-08-12.

**Decision or takeaway**

Use the model to compare choices and expose operating burden, not to promise future payroll or total monthly spend.

---

## Appendix A6

**Title**

The full owner decision set is sequenced by consequence

**Owner-facing English text**

**Release boundary**

Branch L/T · availability promise · single/multi-recipient · response/outcome semantics

**Pilot envelope**

country/city · language · categories · native-mobile expectation

**Trust and legacy**

legacy expectation · permission/lawful basis · contact disclosure · verification standard

**Business and operations**

subscription · sponsored placement · Digital Consulting/shop role · operating capacity · success/stop/expand thresholds

**Missing inputs**

feature/monetization document · existing UI/UX materials · authorized internal access process

**Recommended visual**

A decision tree grouped into four consequence bands. Highlight only the eight core interview decisions; keep the remainder subdued.

**David speaker notes in Spanish**

Esta es la versión completa para consulta. En la reunión principal uso sólo ocho preguntas. Si el owner quiere profundizar, avanzo por consecuencia: primero límite de release, luego piloto, confianza/datos y finalmente monetización/operación.

**Evidence / source notes**

- `docs/05-roadmap/owner-decision-matrix.md`
- `docs/01-product/open-questions.md`
- `docs/01-product/owner-reconciliation-matrix.md`

**Decision or takeaway**

Not every question needs an immediate answer, but every unresolved answer must stay visible.

---

## Appendix A7

**Title**

The pilot measures the whole exchange—not vanity traffic

**Owner-facing English text**

**Supply**

publishable providers · coverage · freshness · effort to publish

**Discovery**

searches · zero-result reason · eligible candidate set · profile engagement

**RFQ**

start · step abandonment · verification · submitted/delivered · clarification/decline/offer/no response

**Marketplace outcome**

response rate/time · customer-reported proceeding · unreported · support/queue load

**Alternatives experiment**

impression → click → reuse → review → explicit send → response → outcome

**Recommended visual**

A measurement spine aligned to the customer/provider loop. Keep “unreported” visible rather than forcing every request into success/failure.

**David speaker notes in Spanish**

No defino targets sin el owner. Sí defino qué se debe medir desde el primer día. No alcanza con visitas o registros: necesitamos saber si hubo candidatos elegibles, requests entregadas, respuestas útiles, outcomes reportados y cuánto esfuerzo operativo costó producirlos.

**Evidence / source notes**

- `docs/05-roadmap/measurement-plan.md`
- `docs/05-roadmap/mvp-definition.md`

**Decision or takeaway**

Pilot success requires owner-defined thresholds for stop, adjust, or expand by pilot market/category.

---

## Appendix A8

**Title**

P05 may lead to five different legacy strategies

**Owner-facing English text**

| Option | When it may fit | Primary risk |
|---|---|---|
| Mostly migrate | High lawful, usable yield | Imports stale or unowned supply at scale |
| Claim first | Reliable contact and safe control proof | Fraud, disputes, low claim rate |
| Curated partial migration | A bounded high-quality cohort exists | Manual selection cost and coexistence |
| Fresh onboarding | Controlled new supply is feasible | Gives up useful continuity if legacy has value |
| Hybrid | Cohort rules and canonical ownership are clear | Highest coexistence complexity |

No option is selected before P05.

**Recommended visual**

A five-branch decision fan with conditions above each branch and the shared “authorized audit” root below.

**David speaker notes in Spanish**

La auditoría no existe para confirmar una migración ya decidida. Puede recomendar casi todo, una parte, claim-first, supply fresca o un híbrido. Cada opción tiene una condición y un costo operativo distinto. Sin ownership, consentimiento, reachability y calidad medidos, elegir sería adivinar.

**Evidence / source notes**

- `docs/05-roadmap/migration-dependency.md` §Decision matrix
- `docs/06-migration/legacy-data-strategy.md`

**Decision or takeaway**

P05 chooses among strategies; it does not merely estimate the effort of one assumed migration path.

---

## Appendix A9

**Title**

Transactional V1 changes product, technology, and operations together

**Owner-facing English text**

| Branch L base | Branch T additions |
|---|---|
| Provider accepting requests | Structured availability and bookable commitment |
| Request + offer/decline/clarify | Booking and completion states |
| Customer-reported outcome | Transaction-derived outcome/review |
| No money movement | Deposit/payment, fees, payout, refunds |
| Marketplace support | Fraud, disputes, guarantee, reconciliation, tax/compliance support |

Current cost figures do not include the Branch T additions.

**Recommended visual**

A true delta comparison with one stable base column and one additions column. Do not portray Branch L as “bad” or Branch T as “advanced.”

**David speaker notes in Spanish**

Esta tabla muestra por qué Branch T se replantea antes de construir. No es sólo un checkout: cambia la promesa de disponibilidad, estados, datos, soporte y responsabilidad sobre dinero. Puede ser la decisión correcta si es la prioridad del negocio; simplemente exige otro plan.

**Evidence / source notes**

- `docs/05-roadmap/mvp-definition.md` §Branch T
- `docs/05-roadmap/phased-roadmap.md`
- `docs/02-architecture/decision-branches.md`
- `plans/P03-technology-evaluation.md`

**Decision or takeaway**

Choose Branch T deliberately, with its full operating model visible.

---

## Appendix A10

**Title**

Future opportunities can extend Superola after the core works

**Owner-facing English text**

**FUTURE OPPORTUNITIES — DAVID PROPOSAL**

**RSVP / digital invitations**

After a customer hires a provider, offer a guest invitation and RSVP workflow—or operate it as a stand-alone product.

**Automated provider web presence**

Generate provider cards/pages as a paid add-on or package, potentially productizing part of the existing Digital Consulting line.

Neither opportunity is part of the proposed MVP.

**Recommended visual**

Two lightweight future branches that emerge only after the core marketplace loop. Use a visibly separate “future” horizon.

**David speaker notes in Spanish**

Estas son propuestas mías basadas en capacidades que ya tengo, no requisitos del owner. RSVP puede aparecer después de una contratación o como producto separado. Las páginas automáticas pueden escalar una parte de Digital Consulting. Las menciono para mostrar opcionalidad, no para inflar V1.

**Evidence / source notes**

- `plans/P06-mvp-roadmap.md` §P07 future-business-opportunity packet
- Public audit: Digital Consulting is an observed, separate live business line.

**Decision or takeaway**

Preserve these options without allowing them to distract from proving the marketplace exchange.
