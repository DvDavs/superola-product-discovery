# Superola Owner Review v1 — Core Presentation Source

> **Status:** `PROPOSED — OWNER VALIDATION REQUIRED`
>
> **Format:** English owner-facing slide copy; Spanish delivery notes live in `superola-owner-review-speaker-notes-v1.md`.
>
> **Use:** 30–45 minute owner conversation. This is a slide-by-slide source, not a rendered deck or approved implementation brief.

## Slide 1

**Title**

Superola: From Listings to a Measurable Marketplace Exchange

**Owner-facing English text**

Current understanding, recommended first release, and the decisions we should make together.

David · Owner review

**Recommended visual**

Minimal cover. One continuous line connects a customer need on the left to a provider response on the right through a centered Superola mark. No feature collage.

**David speaker notes in Spanish**

Abrí aclarando que esto no es una propuesta cerrada. Es mi lectura actual, basada en lo que pude investigar sin acceso interno, y quiero usarla para confirmar qué entendí bien y qué necesita cambiar.

**Evidence / source notes**

- `docs/01-product/product-vision.md`
- `plans/P06-mvp-roadmap.md`

**Decision or takeaway**

This meeting is for alignment and decisions, not for presenting a finished product as already decided.

---

## Slide 2

**Title**

Superola can become the exchange—not only the directory

**Owner-facing English text**

**Customer:** find a provider worth contacting.

**Provider:** receive a relevant opportunity worth answering.

**Superola:** make the exchange trustworthy, observable, and repeatable.

My current interpretation is a two-sided event-services marketplace. I need your confirmation.

**Recommended visual**

A simple three-part loop: Customer need → Superola exchange → Provider response → customer decision. Keep “trustworthy · observable · repeatable” below the loop as one anchor line.

**David speaker notes in Spanish**

Lo que entendí es que el valor no está en publicar fichas por publicar. Está en conseguir que una necesidad real llegue a un proveedor pertinente, que el proveedor pueda responder honestamente y que Superola aprenda del resultado. Si esa lectura no representa el negocio, hay que corregirla antes de construir.

**Evidence / source notes**

- `docs/00-context/product-context.md`
- `docs/01-product/actors.md`
- `docs/01-product/product-vision.md`

**Decision or takeaway**

Confirm or correct the marketplace interpretation before accepting the proposed V1.

---

## Slide 3

**Title**

I investigated the public product—and kept its limits visible

**Owner-facing English text**

I reviewed:

- the public customer and provider surfaces;
- marketplace flows, UX, architecture, technology, and cost options;
- roadmap, measurement, and migration dependencies.

I deliberately did **not** assume:

- what exists behind login or in the database;
- that public records are active providers;
- that a booking label proves a booking system;
- that legacy data is ready to migrate.

**Recommended visual**

An “observed / unknown” split with a narrow public window into a larger shaded internal system. Use three observed examples and three unknowns; avoid repository labels on the canvas.

**David speaker notes in Spanish**

Investigación seria también significa saber dónde termina la evidencia. Pude revisar lo público con bastante profundidad y modelar opciones, pero no vi la base de datos, los procesos internos ni la operación privada. Prefiero decir “no lo sé todavía” antes que construir una promesa sobre una suposición.

**Evidence / source notes**

- `docs/00-context/source-register.md`
- `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md` §§1.3, 18
- `docs/05-roadmap/migration-dependency.md`

**Decision or takeaway**

Public evidence supports design lessons; internal claims and migration decisions still require authorized access.

---

## Slide 4

**Title**

The current platform gives us useful signals—not final answers

**Owner-facing English text**

**43,361 public listing URLs**

Different from the owner-reported **~43,000 registrations**. The relationship is unknown.

**53-page ownership sample**

47 of 53 sampled pages referenced the same owner identifier.

Concentration signal only — internal ownership remains unknown.

**Location and taxonomy examples**

Missing coordinates, repeated centroids, one materially wrong location, and duplicate/misspelled/off-domain terms.

**Booking-labelled surface**

Publicly shows contact intake—not a demonstrated booking or payment lifecycle.

**Recommended visual**

Four evidence callouts around a central statement: “What we can learn.” Make the URL/registration distinction visually unmistakable with two different units and an ≠ sign.

**David speaker notes in Spanish**

No quiero convertir esto en una crítica destructiva del sistema actual. Son señales para decidir qué validar primero. La cifra pública es de URLs, no de proveedores; el dato de ownership viene de una muestra; y lo que se ve como reserva es, públicamente, un formulario de contacto. Lo interno puede cambiar la lectura y por eso hace falta P05.

**Evidence / source notes**

- `docs/00-context/interview-evidence.md`
- `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md` §§3.2, 4.4, 8.1, 12.2, 17.2–17.4
- `docs/00-context/product-context.md`

**Decision or takeaway**

Treat the public footprint as an audit asset and a source of design lessons—not as proof of usable supply.

---

## Slide 5

**Title**

My current V1 recommendation: prove the request-to-response loop first

**Owner-facing English text**

**Branch L — Marketplace Leads / RFQ**

1. A customer discovers an eligible provider.
2. The customer deliberately sends one structured request.
3. The provider clarifies, declines, offers, or does not respond.
4. Both sides can continue asynchronously.
5. The customer reports whether they plan to proceed.

No automatic broadcast. No booking or payment claim. No guaranteed date availability.

**Recommended visual**

A single horizontal journey with five large steps and one highlighted boundary after “reported decision.” Use a dotted continuation labeled “transaction layer, if later justified.”

**David speaker notes in Spanish**

Esta es mi recomendación, no una decisión tomada. Es la porción más chica que completa un intercambio medible para las dos partes. El proveedor evalúa factibilidad real; aceptar solicitudes no quiere decir que una fecha esté disponible. La V1 termina con un resultado reportado, no con una transacción verificada.

**Evidence / source notes**

- `docs/01-product/product-vision.md`
- `docs/05-roadmap/mvp-definition.md`
- `docs/02-architecture/decision-branches.md`

**Decision or takeaway**

Branch L is the pragmatic baseline because it proves marketplace behavior before adding transaction operations.

---

## Slide 6

**Title**

The customer journey stays open until the moment of intent

**Owner-facing English text**

**Proposed customer flow**

Need → category + location → honest results → provider profile → guided request → review recipient → verify and send → response → conversation → reported outcome

Search, profiles, and request drafting stay open. Verification happens when the customer sends.

Zero results explain whether the gap is category, geography, or another eligibility condition.

**Recommended visual**

A clean journey line with three emphasis moments: “honest results,” “review one recipient,” and “verify on send.” Show zero results as a useful side path, not a dead end.

**David speaker notes in Spanish**

Quiero reducir fricción sin perder atribución. El cliente puede explorar y empezar el pedido sin crear una cuenta. Cuando decide enviarlo, revisa a quién va y verifica su acceso. Si no hay resultados, Superola explica qué restricción falló; no manda el pedido por debajo de la mesa a proveedores no elegidos.

**Evidence / source notes**

- `docs/04-ux/customer-journey.md`
- `docs/04-ux/discovery-and-results.md`
- `docs/04-ux/request-intake.md`

**Decision or takeaway**

The customer should understand what will happen before any provider receives information.

---

## Slide 7

**Title**

Provider quality starts before the first lead arrives

**Owner-facing English text**

**Proposed provider flow**

Create account → describe the business → define an offering → declare service area → build profile → preview → review → publish → receive and answer requests

The provider can:

- save and resume onboarding;
- see what blocks publication;
- pause new request intake without claiming every date is unavailable;
- clarify, decline, or offer without being punished for honesty.

**Recommended visual**

A provider path that changes from “draft” to “eligible” only after preview/review/publication. Put the request inbox after the eligibility gate, not alongside onboarding.

**David speaker notes in Spanish**

La calidad de supply no se arregla después con ranking. Se construye desde el onboarding: categorías gobernadas, zona de servicio clara, checklist de publicación y revisión. El proveedor puede pausar solicitudes sin que eso se interprete como un calendario. Y tiene que poder decir que no o pedir aclaración; una respuesta honesta también es valor.

**Evidence / source notes**

- `docs/04-ux/provider-onboarding.md`
- `docs/04-ux/provider-workspace.md`
- `docs/04-ux/provider-profile.md`

**Decision or takeaway**

Curated, governable supply is a prerequisite for trustworthy demand generation.

---

## Slide 8

**Title**

Alternatives reuse effort—but never reuse consent

**Owner-facing English text**

**Proposed alternative-provider flow**

If Provider A is not the right fit:

See alternatives → choose Provider B → reuse compatible event details → review the new recipient and copied information → send a **new** request

Each request remains single-recipient and explicit.

Future option: the customer may explicitly authorize several visible recipients. Automatic fan-out is not V1.

**Recommended visual**

A branching flow from Provider A to an alternatives list, then a visible confirmation checkpoint before Provider B receives a new request. Make the copied draft visually distinct from a sent request.

**David speaker notes in Spanish**

Acá hay un equilibrio importante. No quiero hacer que el cliente reescriba todo, pero tampoco quiero que una solicitud se convierta en spam. Se copia sólo la información compatible a un borrador nuevo; el cliente ve el nuevo destinatario, revisa los datos y vuelve a confirmar. Un borrador copiado NO es una solicitud enviada.

**Evidence / source notes**

- `docs/05-roadmap/mvp-definition.md` §§Alternative-provider experiment, Multi-recipient
- `docs/04-ux/request-intake.md` §Alternative-provider reuse
- `docs/04-ux/customer-journey.md` §Explicit non-capabilities

**Decision or takeaway**

Reuse can improve conversion without sacrificing customer control or provider trust.

---

## Slide 9

**Title**

Relevance comes before ranking—and before sponsorship

**Owner-facing English text**

**Proposed relevance principle**

**1 · Eligibility**

Category · service area · publication · trust and quality rules

**2 · Ordering and placement**

Eligible candidates may later consider profile quality, price, recent activity, response behavior, and clearly labeled sponsorship.

Paid placement can change visibility. It can never make an ineligible provider eligible.

**Recommended visual**

A two-stage funnel. Stage one filters for eligibility; stage two orders the remaining providers. Show sponsored placement entering only in stage two with a clear label.

**David speaker notes in Spanish**

Primero se decide quién merece aparecer para esa necesidad; después se ordena. Eso protege la confianza y también el negocio: vender visibilidad no puede significar vender permiso para saltarse calidad o cobertura. Los factores de ordenamiento son candidatos, no una fórmula prometida; todavía no existe data nueva para aprender pesos reales.

**Evidence / source notes**

- `docs/02-architecture/adr/ADR-006-discovery-owns-eligibility-ordering-and-placement.md`
- `docs/02-architecture/adr/ADR-008-monetization-is-a-reserved-policy-seam.md`
- `docs/05-roadmap/measurement-plan.md`

**Decision or takeaway**

Organic relevance and sponsored inventory must remain independently measurable and visibly distinct.

---

## Slide 10

**Title**

Monetization should follow proof of provider value

**Owner-facing English text**

Owner ideas:

- provider subscription;
- sponsored category × geography inventory.

My recommendation:

1. prove supply quality, demand, request volume, and provider response;
2. test a small fixed-fee, manual, time-bounded sponsored placement;
3. consider subscriptions or more complex budget/CPC systems only when value and scale justify them.

Even a manual sponsorship test needs a named owner, inventory rotation, invoicing, reporting, claims handling, and fairness review.

**Recommended visual**

A three-step staircase: prove the exchange → run a bounded sponsorship test → expand monetization. Keep “no ad-tech first” as a small side annotation, not the headline.

**David speaker notes in Spanish**

Las ideas de suscripción y patrocinio tienen sentido como hipótesis. Mi recomendación es no construir un sistema publicitario antes de saber si hay supply útil, búsquedas reales y respuestas. El primer experimento puede ser manual, con tarifa fija, duración clara y medición separada de lo orgánico. Después decidimos si la complejidad paga su costo.

**Evidence / source notes**

- `docs/01-product/monetization.md`
- `docs/05-roadmap/phased-roadmap.md` Stage 6
- `docs/05-roadmap/measurement-plan.md`
- Public audit §11: no current public marketplace listing monetization observed; historical state unknown.

**Decision or takeaway**

Charge after Superola can demonstrate value—not before providers have experienced it.

---

## Slide 11

**Title**

Legacy data is a decision tree, not a migration promise

**Owner-facing English text**

Owner approval → controlled access and safeguards → internal audit → classify records → owner decision + legal/privacy review where needed → controlled implementation

Possible record outcomes:

migrate · normalize · claim · re-consent · review · deduplicate · suppress · discard/archive

A fresh, curated pilot can proceed without using legacy records.

**Recommended visual**

A left-to-right gated pipeline. Under “classify records,” fan into possible outcomes without percentages. Show “fresh curated pilot” as a parallel path that does not cross the legacy gate.

**David speaker notes in Spanish**

No voy a prometer “migrar los 43 mil proveedores” porque hoy no sabemos cuántos proveedores hay, quién controla los registros, qué consentimiento existe ni qué tan útil es la información. Con permiso y controles, P05 clasifica. Recién después elegimos migrar, reclamar, normalizar, suprimir o descartar. Si conviene, el piloto puede arrancar con supply nueva y curada.

**Evidence / source notes**

- `docs/05-roadmap/migration-dependency.md`
- `docs/06-migration/legacy-data-strategy.md`
- `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md`

**Decision or takeaway**

Access creates evidence; evidence determines migration. Public scale alone does not determine the answer.

---

## Slide 12

**Title**

The roadmap advances when evidence is ready—not when a date looks reassuring

**Owner-facing English text**

Align → Foundation → Curated supply → Discovery → Request-to-response loop → Pilot → Monetization test → Optimize / expand → Transaction layer if evidence supports it

Roughly six months was discussed. A responsible delivery view still depends on branch, pilot scope, team, access, and operating capacity.

**Recommended visual**

A staged path with evidence gates between major phases. Use no calendar dates. Make monetization optional and the transaction layer a separate final branch rather than an inevitable destination.

**David speaker notes in Spanish**

Puedo trabajar con una conversación de seis meses como horizonte, pero no voy a disfrazar incertidumbre con fechas inventadas. Primero necesitamos elegir la rama, el mercado piloto y el equipo. Cada etapa produce evidencia para la siguiente. Monetización es un experimento opcional; transacciones se abren sólo si la prioridad o los resultados lo justifican.

**Evidence / source notes**

- `docs/05-roadmap/phased-roadmap.md`
- `docs/05-roadmap/six-month-roadmap.md`
- `docs/05-roadmap/dependencies.md`

**Decision or takeaway**

The roadmap is conditional and evidence-gated; it is not yet a committed schedule.

---

## Slide 13

**Title**

Technology is manageable; marketplace operations may cost more

**Owner-facing English text**

**Technical/service bill — monthly scenario comparison**

Pilot **~USD $76/month** · Early **~USD $193/month** · Growth **~USD $561/month**

Marketplace operations may become the larger TCO driver as volume grows.

Illustrative operating sensitivity is available in Appendix A5—shown separately; not a committed total.

Technical prices verified **2026-08-11**; reverify before purchase.

**Recommended visual**

A three-stage technical-cost strip with one clear operating-TCO callout below it. Keep the verification date beside the technical figures and point to Appendix A5 for the separate illustrative sensitivity.

**David speaker notes in Spanish**

La factura técnica es relativamente manejable bajo los escenarios comparativos actuales. El costo que puede crecer más es la operación humana: moderar, resolver duplicados, atender soporte, mejorar supply y manejar excepciones. La segunda línea NO se suma como si fuera una factura; usa USD 75 por hora técnica y USD 40 por hora operativa con cargas todavía no medidas. Sirve para comparar decisiones, no para prometer presupuesto.

**Evidence / source notes**

- `docs/03-technology/cost-model.md` §1.1, single arithmetic source of truth
- `docs/03-technology/cost-alternatives.md`
- Prices verified 2026-08-11; re-verification required before purchase.
- Excludes payments/booking, legacy migration, marketing, legal advice, and other unapproved scope.

**Decision or takeaway**

Optimize for total operating burden, not only the lowest vendor invoice.

---

## Slide 14

**Title**

Booking and payments are absolutely possible—and materially different

**Owner-facing English text**

**Branch T adds:**

structured availability · booking states · deposit/payment · cancellation · refund · payout · reconciliation · dispute · fraud · guarantee · support · transaction-derived reviews

Discovery, profiles, requests, offers, and conversation still matter.

If booking/payment is required in V1, we should re-plan scope, compliance, cost, and operations before building—not bolt payments onto the end.

**Recommended visual**

A delta diagram: Branch L as the stable base, with a second layer labeled Transaction Operations expanding above and around it. Avoid red warning styling; use a neutral “different operating model” frame.

**David speaker notes in Spanish**

No estoy diciendo que pagos sea imposible ni que haya que tenerle miedo. Estoy diciendo que cambia el producto. Aparecen disponibilidad comprometida, dinero de terceros, cancelaciones, refunds, payouts, conciliación, fraude, disputas y soporte. Si eso es imprescindible para usted, fantástico: se redefine la primera versión y se vuelve a modelar antes de empezar.

**Evidence / source notes**

- `docs/05-roadmap/mvp-definition.md` §Branch T
- `docs/05-roadmap/phased-roadmap.md` §Branch T activation rule
- `docs/02-architecture/decision-branches.md`
- Current P03 cost figures exclude payment and booking technology.

**Decision or takeaway**

The owner can choose Branch T, but that choice changes the first-release plan before Stage 1.

---

## Slide 15

**Title**

Eight decisions will turn this recommendation into a real plan

**Owner-facing English text**

**Decide in this meeting**

1. Leads/RFQ or booking/payment first?
2. What does “available” promise?
3. Is single-recipient the baseline?
4. Which pilot geography and categories?

**Confirm or assign a next action**

5. What is the legacy expectation—and may we audit it?
6. When should phone/contact details be disclosed?
7. Which monetization priority should we test first?
8. What would make us stop, adjust, or expand the pilot?

Also needed: the promised feature/monetization document and existing UI/UX materials.

**Recommended visual**

Eight numbered prompts in two calm columns, grouped as Product boundary, Pilot, Trust/data, and Business proof. The materials request sits in a footer band.

**David speaker notes in Spanish**

Estas son las decisiones que realmente mueven alcance, costo y orden de trabajo. No necesito que el owner elija base de datos ni framework; necesito que defina el resultado de negocio y los límites. Las respuestas se registran una por una. Si algo queda abierto, sigue abierto; no lo convierto en aprobación por silencio.

**Evidence / source notes**

- `docs/05-roadmap/owner-decision-matrix.md`
- `docs/01-product/open-questions.md`
- `docs/01-product/owner-reconciliation-matrix.md`
- `docs/00-context/source-register.md`

**Decision or takeaway**

The next plan should be driven by explicit owner choices, not silent assumptions.

---

## Slide 16

**Title**

The next step is a bounded decision—not a leap of faith

**Owner-facing English text**

**Now**

Confirm the product branch, pilot envelope, contact policy, and success criteria.

**With authorized access**

Audit ownership, consent, activity, data quality, internal capabilities, analytics, and migration options.

**Then**

Reconcile the evidence, approve a release boundary, and build the smallest measurable pilot.

My role: turn these decisions into a measurable system—and then build it.

**Recommended visual**

A three-step closing path—Decide → Validate → Pilot—with David's working role shown as a continuous foundation underneath, not as a “why hire me” card.

**David speaker notes in Spanish**

Cierro proponiendo una decisión acotada. Primero alineamos la rama y el piloto. Después, con acceso autorizado, valido lo que hoy no puedo saber. Recién entonces cerramos el alcance y construimos. Mi aporte no es sólo escribir código: es reducir riesgo, hacer visibles los costos y desconocidos, y convertir decisiones de negocio en un producto medible.

**Evidence / source notes**

- `docs/05-roadmap/migration-dependency.md`
- `docs/05-roadmap/measurement-plan.md`
- `docs/05-roadmap/phased-roadmap.md`

**Decision or takeaway**

Agree on the owner decisions and the access process; then authorize the smallest evidence-producing pilot.

---

## Core-deck production notes

- Use English as visible slide language. Do not place evidence labels, internal IDs, ADR numbers, gate names, or phase names on the main canvas.
- Keep titles as the spoken takeaway. Do not replace them with generic section labels.
- Use a restrained, credible marketplace aesthetic: human exchange, structured flows, and clear evidence boundaries. Avoid startup hype, stock-photo handshakes, dashboard-card grids, or a legacy “before/after” shame treatment.
- Keep a persistent but quiet footer: `Current recommendation · Owner validation required`.
- The core deck contains 16 slides and is designed for a 30–45 minute conversation with pauses after slides 5, 14, and 15.
- **Recommended main route:** 1 → 2 → 4 → 5 → 6 → 7 → 10 → 11 → 12 → 14 → 15 → 16.
- **OPTIONAL / DISCUSSION MODULES:** Slide 3 (investigation and evidence boundary), Slide 8 (alternative providers), Slide 9 (ranking and sponsorship), and Slide 13 (cost and TCO). Open them when the conversation calls for that depth; do not renumber or move them to the appendix.
- Appendix material lives in `superola-owner-review-appendix-v1.md` and should be used only when the owner asks for depth.
