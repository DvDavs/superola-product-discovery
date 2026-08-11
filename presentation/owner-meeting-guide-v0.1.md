# Owner Meeting Guide v0.1 / Guía de reunión con el owner v0.1

> **Status / Estado:** `PROPOSED — WORKING DRAFT`
> **Purpose / Propósito:** Live conversation support. Everything about V1 in this guide is a proposal, not an approved product, roadmap, technology, price, or delivery commitment. / Apoyo para la conversación en vivo. Todo lo que aquí se dice sobre la V1 es una propuesta, no un producto, roadmap, tecnología, precio ni compromiso de entrega aprobado.

**How to use this guide (David).** Spanish lines are the ones to speak; English lines are the same question for the written record. Each CORE question has a `→ Decides:` note naming what it unblocks — that note is for you, not for the meeting. Ask CORE in order. Stop for the answer; do not fill silence with the proposal. Record answers against `docs/01-product/owner-reconciliation-matrix.md` using the capture template at the end.

---

## Meeting flow / Secuencia de la reunión

Target: 45–60 minutes. CORE questions need at least half of it. Everything before them is context, not persuasion.

### 1. Opening / Apertura — ~5 min

Show that the business was understood before any solution was proposed.

- ES: "Antes de proponer nada, quiero confirmar que entendimos bien el negocio. Todo lo que traemos es una propuesta abierta a discusión, y si algo está mal, prefiero saberlo hoy."
- EN: "Before proposing anything, I want to confirm we understood the business correctly. Everything we bring is a proposal open to discussion, and if something is wrong I would rather hear it today."

### 2. Current interpretation / Interpretación actual — ~8 min

Walk the marketplace loop from `presentation/discovery-preview-v0.1.md`, sections 1–3.

- ES: "Necesidad → búsqueda → perfil → solicitud a un proveedor elegido → cotización, aclaración o rechazo → conversación → el cliente nos dice si avanza o no."
- EN: "Need → search → profile → request to one chosen provider → quote, clarification, or decline → conversation → the customer tells us whether they are proceeding."

Say the count honestly: the ~43,000 registrations are owner-reported and unaudited, and the opportunity is turning real current supply into a working marketplace, not migrating rows.

### 3. Proposed V1 / V1 propuesta — ~7 min

Preview section 5. Name it as a hypothesis every time.

- ES: "La V1 que proponemos se llama Marketplace Leads. Es la porción más chica que todavía completa un ciclo medible para las dos partes. Es una propuesta que necesita su validación."
- EN: "The V1 we propose is Marketplace Leads. It is the smallest slice that still completes a measurable loop for both sides. It is a proposal that requires your validation."

### 4. Deliberate deferrals / Diferimientos deliberados — ~5 min

Preview section 6. The point is that these are staged, not discarded.

- ES: "Reservas, pagos, payouts, disputas, envío automático a varios proveedores, disponibilidad en tiempo real, patrocinio y app móvil no están descartados. Están fuera del camino crítico inicial porque cada uno arrastra operación, cumplimiento y riesgo que conviene ganar, no asumir."
- EN: "Booking, payments, payouts, disputes, automatic multi-provider sending, real-time availability, sponsorship, and a mobile app are not discarded. They are off the initial critical path because each drags operations, compliance, and risk that should be earned rather than assumed."

### 5. Owner decisions / Decisiones del owner — ~20 min

Ask the seven CORE questions. This is the part that cannot be cut.

### 6. Materials / Materiales — ~5 min

Request and register the pending materials.

### 7. Closing / Cierre — ~3 min

Explain what happens with the answers.

- ES: "Con estas respuestas definimos la arquitectura y el diseño de experiencia. Lo que quede abierto queda registrado como pregunta abierta, no como supuesto silencioso."
- EN: "These answers drive the architecture and UX design work. Whatever stays unresolved is recorded as an open question, not as a silent assumption."

---

## CORE — 7 questions maximum / 7 preguntas máximo

Each of these changes scope, cost, timeline, or architecture. Ask all seven even if time is short.

### C1 — Success / Éxito

- ES: "¿Qué tiene que conseguir un cliente, y qué tiene que conseguir un proveedor, para que usted diga que el primer lanzamiento de Superola funcionó?"
- EN: "What must a customer get, and what must a provider get, for you to say the first Superola release worked?"

`→ Decides: OR-001. Gate G-01. If this answer is transactional, the whole proposed boundary is wrong.`

### C2 — Where V1 stops / Dónde termina la V1

- ES: "Proponemos que la primera versión termine cuando el cliente nos dice si va a avanzar con un proveedor, sin reservas ni pagos en la plataforma. ¿Está de acuerdo, o para usted la primera versión tiene que llegar hasta la reserva o el pago?"
- EN: "We propose the first version ends when the customer tells us whether they are proceeding with a provider, with no booking or payment on the platform. Do you agree, or must the first version reach booking or payment?"

`→ Decides: OR-005, OR-014. Gates G-02. Disagreement here is the single most expensive answer in the meeting — capture it precisely.`

### C3 — Direct request or matching / Solicitud directa o matching

- ES: "En lo que proponemos, el cliente elige un proveedor y le envía una solicitud; si quiere contactar a otro, es una acción nueva y deliberada. La alternativa es que Superola mande una sola solicitud a varios proveedores. ¿Cuál de las dos necesita el negocio en el primer lanzamiento?"
- EN: "In what we propose, the customer picks one provider and sends a request; contacting another is a new deliberate action. The alternative is Superola sending one request to several providers. Which does the business need in the first release?"

`→ Decides: OR-006. Gate G-03. Do not accept silence as agreement — require an explicit choice.`

### C4 — First market / Primer mercado

- ES: "¿Con qué categorías, en qué ciudades o países, y en qué idioma tenemos que salir primero? Si todavía no está decidido, ¿cuáles son los dos escenarios más probables?"
- EN: "Which categories, in which cities or countries, and in which language must we launch first? If it is not decided yet, what are the two most likely scenarios?"

`→ Decides: OR-002, OR-003, OR-004. Gates G-04, G-05. Two named scenarios are an acceptable answer; an open range is not.`

### C5 — Legacy providers / Proveedores actuales

- ES: "De los proveedores registrados hoy, ¿cuáles cree que siguen siendo reales y útiles, y tenemos permiso para migrarlos, contactarlos o pedirles que reclamen su perfil?"
- EN: "Of the providers registered today, which do you believe are still real and useful, and do we have permission to migrate them, contact them, or ask them to claim their profile?"

`→ Decides: OR-008, OR-009. Gate G-09. Ask about belief and permission, not about counts — the counts need an audit, not an opinion.`

### C6 — When providers pay / Cuándo pagan los proveedores

- ES: "¿Qué valor debería ser siempre gratis para un proveedor, qué debería requerir suscripción más adelante, y qué tiene que haber vivido un proveedor para que cobrarle sea justo?"
- EN: "What value should always be free for a provider, what should require a subscription later, and what must a provider have experienced before charging them is fair?"

`→ Decides: OR-012. Gate G-08. Direction only — no price is being agreed in this meeting.`

### C7 — Commitments versus ideas / Compromisos versus ideas

- ES: "Del documento de funcionalidades y monetización, del trabajo de UI/UX que ya existe, y de la conversación sobre seis meses: ¿qué es un compromiso firme del negocio y qué es una idea que podemos discutir o dejar para después?"
- EN: "Of the feature and monetization document, the existing UI/UX work, and the six-month discussion: what is a firm business commitment, and what is an idea we can challenge or stage?"

`→ Decides: OR-015, OR-016, OR-017. Gate G-10. Ask item by item; a single global answer is not usable.`

---

## IF TIME — maximum 5 / Si hay tiempo — máximo 5

### T1 — Contact disclosure / Datos de contacto

- ES: "¿Cuándo deberían poder intercambiar teléfono o email el cliente y el proveedor, y qué quiere que quede registrado dentro de Superola?"
- EN: "When should a customer and provider be able to exchange phone or email, and what do you want kept inside Superola?"

`→ OR-011.`

### T2 — What "available" promises / Qué promete "disponible"

- ES: "Cuando un cliente ve que un proveedor está disponible, ¿qué le estamos prometiendo: una señal orientativa, una invitación a preguntar, o una fecha realmente reservable?"
- EN: "When a customer sees a provider as available, what are we promising: a helpful indication, an invitation to ask, or a genuinely reservable date?"

`→ OR-010. Gate G-06 — promote to CORE if C4 exposes categories with hard date scarcity.`

### T3 — Sponsored placement / Espacios patrocinados

- ES: "¿Qué tendría que ser cierto en una ciudad y categoría para que usted se sienta cómodo vendiendo posiciones destacadas ahí?"
- EN: "What would have to be true in a city and category before you would feel comfortable selling promoted positions there?"

`→ OR-013.`

### T4 — Trust and verification / Confianza y verificación

- ES: "¿Qué señales de confianza importan al lanzar, y qué prueba haría honesto decir que un proveedor está verificado?"
- EN: "Which trust signals matter at launch, and what proof would make calling a provider verified honest?"

`→ Q-014.`

### T5 — Operating capacity / Capacidad de operación

- ES: "¿Cuánta moderación, reactivación de proveedores y soporte a clientes puede sostener Superola realmente durante el lanzamiento, y en qué idiomas?"
- EN: "How much moderation, provider reactivation, and customer support can Superola realistically operate during launch, and in which languages?"

`→ Operating envelope in docs/05-roadmap/mvp-scope.md.`

---

## Materials request / Pedido de materiales

- ES: "Para avanzar necesitamos tres cosas: el documento actual de funcionalidades y monetización, los archivos o enlaces de UI/UX que ya existen, y saber si podemos tener acceso autorizado al sitio actual. Los datos de la base legacy los pedimos después, con controles de privacidad acordados."
- EN: "To move forward we need three things: the current feature and monetization document, the existing UI/UX files or links, and whether authorized access to the current site is possible. Legacy database data comes later, under agreed privacy controls."

Register version and date for everything received. Full list and handling rules: `docs/01-product/owner-reconciliation-matrix.md`, owner material checklist.

Do not request credentials or production data exports during the meeting or through informal channels.

---

## Capture template / Plantilla de registro

Fill during or immediately after the meeting. One block per answered topic.

```
Topic ID:        OR-0NN
Question asked:  C1..C7 / T1..T5
Answer (verbatim or close summary):
Source:          OWNER_INTERVIEW — meeting, <date>, <speaker>
Relationship:    CONFIRMS | EXTENDS | CONTRADICTS | REPLACES | CLARIFIES | STILL OPEN
Follow-up:       <specific next evidence action + owner>
```

Then transfer each block into the reconciliation ledger in `docs/01-product/owner-reconciliation-matrix.md`. An answer that is not written down with a relationship value has not been reconciled, and must not change any canonical document.

## Do not ask / No preguntar

These belong to David and the technical team, not to the owner:

- Which database, framework, hosting, language, or vendor to use.
- How search, ranking, notifications, or messaging should be implemented.
- Schema, API, or infrastructure shape.
- Whether the system should be a monolith or distributed.

If the owner raises them, record the preference as context and return the conversation to the business outcome.
