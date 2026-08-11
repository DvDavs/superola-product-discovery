# Superola Discovery Preview v0.1

> **Status / Estado:** `PROPOSED — WORKING DRAFT`
> **Purpose / Propósito:** Owner discussion; not an approved product, roadmap, technology, price, or delivery commitment. / Conversación con el owner; no es un producto, roadmap, tecnología, precio ni compromiso de entrega aprobado.

---

## 1. Qué es Superola y dónde está hoy / What Superola is and where it stands today

### Español

- Superola es un **marketplace de dos lados** entre personas que organizan eventos y proveedores de servicios: músicos, DJs, salones, transporte, pasteles, maquillaje y otras categorías. No es solamente un directorio.
- El owner reporta que Superola acumuló aproximadamente **43 mil registros de proveedores** con atención limitada; el dato y su origen/actividad todavía no están auditados.
- El owner también reporta ubicación poco confiable y categorías saturadas; la magnitud y causas aún requieren auditoría.
- La oportunidad no es “migrar 43 mil filas”; es convertir oferta real y vigente en un marketplace útil.

### English

- Superola is a **two-sided marketplace** between people organizing events and service providers: musicians, DJs, venues, transportation, cakes, makeup, and other categories. It is not only a directory.
- The owner reports that Superola accumulated approximately **43,000 provider registrations** with limited attention; the count and its acquisition/activity characteristics remain unaudited.
- The owner also reports unreliable location and saturated categories; their extent and causes still require audit.
- The opportunity is not “migrate 43,000 rows”; it is to turn real, current supply into a useful marketplace.

*Sources / Fuentes: `docs/00-context/product-context.md`, `SRC-001`, `A-001`, `R-001`, `R-010`.*

---

## 2. Oportunidad del marketplace / Marketplace opportunity

### Español

- Cliente: encontrar un proveedor adecuado, obtener una respuesta útil y decidir con menos incertidumbre.
- Proveedor: recibir oportunidades relevantes que pueda cotizar o rechazar, y reconocer valor atribuible.
- Superola: crear intercambios repetibles y medibles sin asumir desde el inicio toda la operación de una transacción.

### English

- Customer: find a suitable provider, obtain a useful response, and decide with less uncertainty.
- Provider: receive relevant opportunities they can quote or decline, and recognize attributable value.
- Superola: create repeatable, measurable exchanges without taking on the full transaction operation at launch.

*Source / Fuente: `docs/01-product/actors.md`.*

---

## 3. Ciclo del marketplace / Customer + provider loop

### Español

**Necesidad → Búsqueda → Perfil → RFQ directo → Cotización o rechazo → Conversación → Intención reportada**

- Cada RFQ tiene exactamente un proveedor elegido; contactar a otro exige una acción y confirmación nuevas. No hay broadcast automático en V1.
- Una cotización no es una reserva y la intención de continuar no es un pago.
- Respuesta, no-respuesta, rechazo y resultado desconocido también son aprendizaje.

### English

**Need → Search → Profile → Direct RFQ → Quote or decline → Conversation → Reported intent**

- Each RFQ has exactly one selected provider; contacting another requires a new deliberate action and confirmation. There is no automatic broadcast in V1.
- A quote is not a booking and intent to proceed is not payment.
- Response, no response, decline, and unknown outcome are also learning signals.

*Sources / Fuentes: `docs/01-product/product-vision.md`, `docs/01-product/user-journeys.md`.*

---

## 4. Qué aprendimos de marketplaces maduros / What mature marketplaces teach us

### Español

- Discovery directa y matching general son trabajos distintos; no deben entrar juntos por costumbre.
- Perfil mínimo publicable + enriquecimiento progresivo funciona mejor que pedir todo al inicio.
- Disponibilidad, contacto y “verificado” necesitan definiciones honestas y auditables.
- Ranking orgánico y patrocinio deben permanecer separados y claramente identificados.
- Booking, pagos, protección, disputas y reseñas forman un conjunto operativo acoplado.

### English

- Direct discovery and general matching are different jobs; they should not ship together by habit.
- A minimum publishable profile plus progressive enrichment is stronger than asking for everything upfront.
- Availability, contact, and “verified” need honest, auditable definitions.
- Organic ranking and sponsorship must remain separate and clearly disclosed.
- Booking, payments, protection, disputes, and reviews are a coupled operating cluster.

*Sources / Fuentes: `docs/01-product/competitive-benchmark.md` (`GS-01`–`GS-09`, `TB-01`–`TB-09`; observations are not Superola requirements).*

---

## 5. V1 propuesta / Proposed V1 launch slice

### Español

**Marketplace Leads — `PROPOSED — OWNER VALIDATION REQUIRED`**

- Marketplace navegable públicamente por personas, con búsqueda determinística y perfiles confiables; indexación/crawlers sujetos a política aprobada.
- Onboarding fresco del proveedor; claim solamente para una cohorte importada y auditada.
- RFQ directo, verificación del cliente, cotizar/aclarar/rechazar y conversación asíncrona de texto.
- Estados de vacío, no acepta RFQ, no respuesta, spam/reporte y desactivación.
- Onboarding gratuito y medición de valor; sin automatización de planes/entitlements en V1.0.

### English

**Marketplace Leads — `PROPOSED — OWNER VALIDATION REQUIRED`**

- Marketplace publicly browseable by people, with deterministic search and credible profiles; indexing/crawlers depend on an approved policy.
- Fresh-provider onboarding; claim only for an audited imported cohort.
- Direct RFQ, customer verification, quote/clarify/decline, and asynchronous text conversation.
- Empty, not-accepting-RFQ, no-response, spam/report, and deactivation states.
- Free onboarding and value measurement; no plan/entitlement automation in V1.0.

*Source / Fuente: `docs/05-roadmap/mvp-scope.md`.*

---

## 6. Qué dejamos después / What we deliberately defer

### Español

- Broadcast/matching automático a múltiples proveedores.
- Booking, pagos, depósitos, payouts, reembolsos, disputas y garantías.
- Disponibilidad universal en tiempo real y sincronización de calendarios.
- Inventario patrocinado hasta tener liquidez, reglas, disclosure y medición creíbles.
- Apps móviles, acciones de agentes externos y adaptadores de protocolos sin evidencia de canal.

### English

- Automatic multi-provider broadcast/matching.
- Booking, payments, deposits, payouts, refunds, disputes, and guarantees.
- Universal real-time availability and calendar synchronization.
- Sponsored inventory until liquidity, rules, disclosure, and measurement are credible.
- Mobile apps, external-agent actions, and protocol adapters without channel evidence.

*Sources / Fuentes: `docs/05-roadmap/mvp-scope.md`, `docs/01-product/monetization.md`.*

---

## 7. Descubrimiento humano + IA / Human + AI discovery

### Español

**Fundación V1 si se aprueba distribución pública/indexable:** contenido visible útil para personas, URLs canónicas, taxonomía/geografía gobernadas, frescura y políticas de publicación. Un programa SEO de páginas geográficas no viene incluido automáticamente.

**Experimento separado:** intención en lenguaje natural → restricciones estructuradas → búsqueda determinística; fuera del camino crítico, con presupuesto y regla de stop propios.

**Futuro:** acciones de agentes externos y adaptadores de protocolos, solamente con un canal/partner y economía medible.

### English

**V1 foundation if public/indexable distribution is approved:** useful visible content for people, canonical URLs, governed taxonomy/geography, freshness, and publication policies. An SEO location-page program is not automatically included.

**Separate experiment:** natural-language intent → structured constraints → deterministic search; off the critical path, with its own budget and stop rule.

**Future:** external-agent actions and protocol adapters, only with a named channel/partner and measurable economics.

*Source / Fuente: `docs/07-research/ai-discoverability.md`.*

---

## 8. Decisiones necesarias / Decisions needed from owner

### Español

1. ¿Qué resultado define éxito para cliente y proveedor?
2. ¿RFQ directo o también matching general, y dónde termina V1?
3. ¿Qué categorías, mercados e idiomas lanzan primero?
4. ¿Qué oferta legacy es confiable y legalmente utilizable?
5. ¿Qué valor es gratis, qué sería pagado y cuándo es justo cobrar?
6. ¿Cuándo se comparten datos de contacto?
7. ¿Qué materiales/plazos son compromisos y cuáles son ideas abiertas?

### English

1. What outcome defines success for customer and provider?
2. Direct RFQ or general matching too, and where does V1 end?
3. Which categories, markets, and languages launch first?
4. Which legacy supply is trustworthy and legally usable?
5. What value is free, what may become paid, and when is charging fair?
6. When may contact details be shared?
7. Which materials/timelines are commitments versus open ideas?

*Source / Fuente: `docs/01-product/open-questions.md`.*

---

## 9. Qué sigue / What happens next

### Español

1. Sus respuestas se registran una por una y se comparan con esta propuesta: la confirman, la amplían, la contradicen o la reemplazan. Lo que quede sin resolver queda anotado como pregunta abierta, nunca como supuesto silencioso.
2. Pedimos el documento de funcionalidades y monetización, y los materiales de UI/UX que ya existen, para reconciliarlos con esto antes de diseñar.
3. Con eso definido, empieza el trabajo de arquitectura y de diseño de experiencia.
4. Alcance comprometido, plazos, costos y tecnología llegan después, con evidencia. Nada de eso se decide en este documento.

### English

1. Your answers are recorded one by one and compared with this proposal: they confirm it, extend it, contradict it, or replace it. Whatever stays unresolved is recorded as an open question, never as a silent assumption.
2. We request the feature and monetization document and the existing UI/UX materials, to reconcile them with this before designing.
3. Once that is settled, architecture and experience design work begins.
4. Committed scope, timelines, costs, and technology come later, with evidence. None of that is decided in this document.

*Sources / Fuentes: `docs/01-product/owner-reconciliation-matrix.md`, `plans/P01.1-owner-reconciliation-prep.md`.*
