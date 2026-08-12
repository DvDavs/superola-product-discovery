# Explicación de costos Superola v0.1 / Superola Cost Explainer v0.1

> **Estado / Status:** `PROPUESTA — APROBACIÓN DE DAVID REQUERIDA` / `PROPOSED — DAVID APPROVAL REQUIRED`
>
> **Moneda / Currency:** **Todas las cifras están en dólares estadounidenses (USD).** / **All figures are in United States dollars (USD).**
>
> **Propósito / Purpose:** Explicar en lenguaje de negocio por qué la tecnología cuesta lo que cuesta. Se entiende sin leer ningún documento técnico. / Explain in business language why the technology costs what it costs. It stands alone without reading any technical document.
>
> **Importante / Important:** Ninguna tecnología está comprada ni contratada. **Escenario ilustrativo de planeación — no es un pronóstico.** / No technology is bought or contracted. **Illustrative planning scenario — not a forecast.**

---

## 1. ¿Cuánto cuesta la tecnología? / What does the technology cost?

| Etapa / Stage | Proveedores publicados / Published providers | **Tecnología / Technology** |
|---|---|---|
| **Piloto / Pilot** | 150 | **USD $76 / mes · month** |
| **Marketplace temprano / Early Marketplace** | 1,500 | **USD $193 / mes · month** |
| **Crecimiento / Growth** | 10,000 | **USD $561 / mes · month** |

### Español

Eso es todo lo que se le paga a proveedores de tecnología: servidores, base de datos, almacenamiento de fotos, correo, mapas, respaldos, monitoreo. **En el piloto cuesta menos que un plan de celular.**

Estas cifras **no incluyen personas**. El trabajo humano de operar el marketplace se explica aparte, en la sección 4, y **no es un compromiso ni un pronóstico**.

Los volúmenes de cada etapa son **supuestos**, no datos. Superola todavía no tiene información de tráfico. Los ~43,000 registros del sitio actual son un dato reportado y **no auditado**, y no se usó para dimensionar nada. **Estas bandas sirven para comparar decisiones entre sí, no para presupuestar.**

### English

That is everything paid to technology vendors: servers, database, photo storage, email, maps, backups, monitoring. **At pilot stage it costs less than a phone plan.**

These figures **do not include people**. The human work of operating the marketplace is explained separately in section 4, and **it is neither a commitment nor a forecast**.

The volumes behind each stage are **assumptions**, not data. Superola has no traffic evidence yet. The ~43,000 records from the current site are owner-reported and **unaudited**, and were not used to size anything. **These bands exist to compare decisions against each other, not to budget.**

---

## 2. ¿A dónde va el dinero? / Where does the money go?

### Piloto — USD $76 / Pilot — USD $76

| | Español | English | USD / mes · month |
|---|---|---|---|
| **46%** | La aplicación corriendo | The application running | $35 |
| **33%** | Un ambiente de pruebas separado | A separate testing environment | $25 |
| **20%** | La base de datos | The database | $15 |
| **1%** | Correo, mapas, respaldo externo | Email, maps, external backup | $1 |
| | **Total** | **Total** | **$76** |

### Crecimiento — USD $561 / Growth — USD $561

| | Español | English | USD / mes · month |
|---|---|---|---|
| **44%** | Base de datos, con una copia de respaldo en caliente | Database, with a hot standby copy | $244 |
| **22%** | La aplicación corriendo | The application running | $125 |
| **9%** | Monitoreo y registro de errores | Monitoring and error tracking | $51 |
| **7%** | Ambiente de pruebas | Testing environment | $40 |
| **7%** | Correo (250,000 mensajes al mes) | Email (250,000 messages a month) | $40 |
| **8%** | Red de entrega y automatización | Delivery network and automation | $45 |
| **2%** | Mapas y geolocalización | Maps and geolocation | $9 |
| **1%** | **Fotos: almacenar y servir 2.5 TB al mes** | **Photos: storing and serving 2.5 TB a month** | **$4** |
| **<1%** | Respaldo cifrado fuera del proveedor | Encrypted backup held outside the vendor | $3 |
| | **Total** | **Total** | **$561** |

### Español

**Dos cosas llaman la atención en esa tabla, y las dos son a propósito.**

La primera: **casi la mitad de la factura en crecimiento es la base de datos**, y la mitad de eso es una segunda copia que existe solo para que el sistema siga funcionando si la primera falla. Es un seguro, no capacidad.

La segunda: **servir 2.5 terabytes de fotos al mes cuesta USD $4.** No es una oferta ni un descuento — es la consecuencia de una decisión de diseño que se explica en la sección 3.

### English

**Two things stand out in that table, and both are deliberate.**

First: **almost half the Growth bill is the database**, and half of that is a second copy that exists only so the system keeps running if the first one fails. That is insurance, not capacity.

Second: **serving 2.5 terabytes of photos a month costs USD $4.** That is not a deal or a discount — it is the consequence of a design decision, explained in section 3.

---

## 3. ¿Por qué no usar el servidor más barato? / Why not use the cheapest server?

### Español

Es la pregunta correcta, y la respuesta tiene números.

Un servidor sin administrar costaría unos **USD $26 al mes** en lugar de **USD $76**. Se ahorran USD $50. **Pero alguien tiene que hacer el trabajo que el servicio administrado hace solo:** parchar el sistema operativo, actualizar y respaldar la base de datos, configurar y verificar respaldos, mantener certificados, montar monitoreo, y atender caídas sin que exista recuperación automática.

Ese trabajo son **entre 6.5 y 15 horas más al mes**, dependiendo de la etapa.

**El punto de equilibrio:** administrar el servidor uno mismo conviene **solo si la hora de quien lo hace vale menos de unos USD $8.**

Y hay algo que no depende de ninguna tarifa: **ese ahorro se paga con el recurso más escaso del proyecto — el tiempo de David**, que compite directamente contra construir el producto.

### English

That is the right question, and the answer has numbers.

An unmanaged server would cost about **USD $26 a month** instead of **USD $76**. You save USD $50. **But somebody has to do the work the managed service does by itself:** patching the operating system, upgrading and backing up the database, configuring and verifying backups, maintaining certificates, standing up monitoring, and handling outages with no automatic recovery.

That work is **6.5 to 15 additional hours a month**, depending on the stage.

**The break-even:** self-managing the server is worth it **only if the hour of whoever does it is worth less than about USD $8.**

And one thing does not depend on any rate: **that saving is paid in the project's scarcest resource — David's time**, which competes directly against building the product.

---

## 4. ¿Qué decisiones ahorran más dinero? / Which decisions save the most money?

### Español

| | Decisión | Qué ahorra |
|---|---|---|
| **1** | **Las fotos van a un servicio que no cobra por servirlas.** | En crecimiento, servir 2.5 TB al mes cuesta **USD $4** en lugar de **USD $375** con un proveedor que cobra por tráfico. **Fue el ahorro individual más grande de todo el análisis.** |
| **2** | **Superola tiene su propia lista de ciudades y zonas.** Cuando un cliente busca por ubicación, no se le pregunta a ningún proveedor externo. Solo se geolocalizan las direcciones de los proveedores. | **USD $9 al mes** en lugar de **USD $634** — y hasta **USD $3,000** si cada búsqueda consultara a un proveedor externo. **Es una decisión de diseño, no de negociación de precio.** |
| **3** | **El video y el audio se enlazan, no se alojan.** | Evita unos **USD $429 al mes** en crecimiento, evita que una persona tenga que ver o escuchar cada archivo completo, y evita un problema de derechos musicales en las categorías de músicos. |
| **4** | **La búsqueda la hace la base de datos.** No se compra un motor de búsqueda aparte. | **USD $0** en lugar de **USD $295 al mes** en crecimiento — y evita tener dos fuentes de verdad que se pueden desincronizar. |
| **5** | **No hay inteligencia artificial en el camino crítico.** | Evita un costo que crece con cada búsqueda anónima: entre **USD $600 y USD $6,000 al mes** en crecimiento, dependiendo del modelo. |

**Un patrón que vale la pena notar:** los ahorros más grandes no vienen de negociar precios. **Vienen de decidir que un costo no exista.**

### English

| | Decision | What it saves |
|---|---|---|
| **1** | **Photos go to a service that does not charge to serve them.** | At Growth, serving 2.5 TB a month costs **USD $4** instead of **USD $375** with a vendor that bills for traffic. **It was the single largest saving in the whole analysis.** |
| **2** | **Superola owns its own list of cities and areas.** When a customer searches by location, no external vendor is asked. Only provider addresses are geolocated. | **USD $9 a month** instead of **USD $634** — and up to **USD $3,000** if every search called an external vendor. **This is a design decision, not a price negotiation.** |
| **3** | **Video and audio are linked, not hosted.** | Avoids about **USD $429 a month** at Growth, avoids a person having to watch or listen to each file end to end, and avoids a music-rights problem in the musician categories. |
| **4** | **The database performs the search.** No separate search engine is bought. | **USD $0** instead of **USD $295 a month** at Growth — and avoids having two sources of truth that can drift apart. |
| **5** | **No artificial intelligence in the critical path.** | Avoids a cost that grows with every anonymous search: between **USD $600 and USD $6,000 a month** at Growth, depending on the model. |

**A pattern worth noticing:** the largest savings did not come from negotiating prices. **They came from deciding that a cost should not exist.**

---

## 5. El costo humano, explicado aparte / The human cost, explained separately

### Español

**Esta sección se presenta por separado a propósito, y no debe sumarse a la sección 1.**

Operar un marketplace requiere trabajo humano: revisar categorías, resolver duplicados, atender reportes, dar seguimiento a proveedores inactivos, responder soporte, y ejecutar solicitudes de datos personales.

Un ejercicio de sensibilidad — **no un pronóstico, no un presupuesto, no un compromiso** — estimó ese trabajo así:

| Etapa | Horas / mes | Costo ilustrativo |
|---|---|---|
| Piloto | ~12 h | ~USD $578 |
| Marketplace temprano | ~60 h | ~USD $2,592 |
| Crecimiento | ~350 h | ~USD $14,298 |

**Los supuestos están a la vista, porque toda la cifra depende de ellos:** se asumió **USD $75/hora** para trabajo técnico de infraestructura y **USD $40/hora** para operación del marketplace. **Si esos números cambian, la columna cambia en proporción directa.** Y los volúmenes de trabajo son supuestos sin base medida.

**Si usted o David hacen ese trabajo, el costo en efectivo es cero** y el costo real es el tiempo no disponible para otra cosa.

**Para qué sirve entonces esta tabla.** Para ordenar decisiones: **una decisión que ahorra USD $30 en la factura y agrega una hora de trabajo al mes es una pérdida.** Todo el diseño técnico se optimizó con ese criterio. **No sirve para decir "Superola cuesta USD $14,859 al mes" — eso no es una afirmación que este análisis sostenga.**

### English

**This section is presented separately on purpose, and must not be added to section 1.**

Operating a marketplace takes human work: reviewing categories, resolving duplicates, handling reports, following up on inactive providers, answering support, and executing personal-data requests.

A sensitivity exercise — **not a forecast, not a budget, not a commitment** — estimated that work as:

| Stage | Hours / month | Illustrative cost |
|---|---|---|
| Pilot | ~12 h | ~USD $578 |
| Early Marketplace | ~60 h | ~USD $2,592 |
| Growth | ~350 h | ~USD $14,298 |

**The assumptions are stated because the whole figure depends on them:** **USD $75/hour** was assumed for technical infrastructure work and **USD $40/hour** for marketplace operations. **If those numbers change, the column moves in direct proportion.** And the workload volumes are assumptions with no measured basis.

**If you or David do that work, the cash cost is zero**, and the real cost is time not spent on something else.

**What the table is actually for.** Ordering decisions: **a decision that saves USD $30 on the invoice and adds one hour of monthly work is a loss.** The entire technical design was optimised on that basis. **It is not for saying "Superola costs USD $14,859 a month" — that is not a claim this analysis supports.**

---

## 6. ¿Qué NO está incluido? / What is NOT included?

### Español

| | No incluido | Por qué |
|---|---|---|
| **1** | **Pagos y reservas** | Es su decisión y sigue pendiente. Es el grupo de costos más grande de todo el programa: comisión por transacción, cumplimiento de datos de tarjeta, verificación de identidad para pagos, y conciliación. **Ninguna cifra de arriba lo cubre.** |
| **2** | **Migrar los registros del sitio actual** | Pendiente de permiso legal. Hoy cuesta cero porque no está aprobado. Si se aprueba, aparece la cola de trabajo más cara del producto: decidir quién es el dueño legítimo de cada registro. |
| **3** | **Marketing, conseguir proveedores, contenido y asesoría legal** | Fuera del alcance de este análisis. **En la etapa temprana probablemente superan todo lo modelado aquí.** |
| **4** | **Sueldos** más allá de las dos tarifas ilustrativas de la sección 5 | No es un análisis de personal. |
| **5** | **Un mapa dibujado en pantalla** | Todavía no se decide si hace falta. Es una decisión de diseño de producto (P04), no técnica. |

### English

| | Not included | Why |
|---|---|---|
| **1** | **Payments and booking** | Your decision, still pending. It is the largest cost cluster in the whole programme: per-transaction fees, card-data compliance, payout identity verification, and reconciliation. **No figure above covers any of it.** |
| **2** | **Migrating the current site's records** | Pending legal permission. Today it costs zero because it is not approved. If approved, the most expensive work queue in the product appears: deciding who legitimately owns each record. |
| **3** | **Marketing, acquiring providers, content, and legal counsel** | Outside this analysis. **At the early stage they plausibly exceed everything modelled here.** |
| **4** | **Salaries** beyond the two illustrative rates in section 5 | This is not a staffing analysis. |
| **5** | **A map drawn on screen** | Not yet decided whether one is needed. It is a product design decision (P04), not a technical one. |

---

## 7. ¿Qué podría subir la factura de forma importante? / What could materially increase the bill?

### Español

En orden de impacto:

1. **Incluir pagos y reservas en el primer lanzamiento.** Es el cambio más grande posible, y no está en ninguna cifra.
2. **Aprobar la migración de los registros actuales.** Cambia la cifra humana más grande de la tabla, no la de tecnología.
3. **Operar en dos idiomas.** Estructuralmente casi no cuesta — el sistema ya está hecho para eso. **Operativamente duplica cada cola de trabajo humano, para siempre.** Es una decisión de personal.
4. **Que el tráfico público crezca mucho más rápido que las solicitudes.** El diseño ya protege contra esto: las fotos no cobran por servirse. **Sin esa decisión, este sería el renglón que se sale de control.**
5. **Decidir mostrar un mapa.** Reabre un costo que hoy está en cero y que tendría que investigarse antes de comprometerse a una cifra.

### English

In order of impact:

1. **Including payments and booking in the first launch.** The largest possible change, and it is in no figure.
2. **Approving migration of the current records.** It changes the largest *human* figure, not the technology one.
3. **Operating in two languages.** Structurally almost free — the system is already built for it. **Operationally it doubles every human work queue, permanently.** That is a staffing decision.
4. **Public traffic growing much faster than requests.** The design already protects against this: photos cost nothing to serve. **Without that decision, this is the line that would run away.**
5. **Deciding to show a map.** It reopens a cost that is zero today and would need research before any figure could be committed.

---

## 8. ¿Qué decisiones siguen abiertas? / What decisions remain open?

### Español

Ninguna de estas es un problema. Son decisiones que **deliberadamente no se tomaron todavía**, porque tomarlas ahora sería adivinar.

| Decisión abierta | Quién decide | Por qué sigue abierta |
|---|---|---|
| **Cómo se construyen las pantallas** (más trabajo en el servidor o más en el navegador) | Diseño de producto (P04) | Depende de cómo sean las pantallas, y todavía no se diseñaron. **Se dejó abierto a propósito para no atar el diseño.** |
| **Cómo se manejan las cuentas y contraseñas** (construirlo o contratar un proveedor) | David | Se verificó un dato clave el 12 de agosto de 2026 y **el resultado cambió el análisis**: dos proveedores sí permiten llevarse las contraseñas. La recomendación se está re-evaluando con honestidad en vez de defenderse. |
| **Si se dibuja un mapa, y de qué tipo** | Diseño de producto (P04) | La regla que sí es firme: **la ubicación exacta de un proveedor nunca se muestra en público.** Eso no impide mostrar una zona o una ciudad. |
| **Cuál proveedor de geolocalización** | Abogado | Dos puntos de los términos legales del proveedor no están resueltos. **Una interpretación legal no se puede escribir como si fuera un hecho técnico.** |
| **En qué región se aloja el sistema** | Usted, al decidir el país de lanzamiento | Elegir región ahora decidiría el país por accidente. |
| **Si se prueba asistencia con IA para llenar solicitudes** | Diseño de producto (P04) | Idea acotada, opcional y con tope de gasto: el cliente escribe *"necesito un mariachi para un quinceañero en Houston el sábado, 150 personas, como $1,500"*, el sistema propone los campos, **y el cliente los confirma o corrige.** No decide resultados ni reemplaza el formulario. |

### English

None of these is a problem. They are decisions **deliberately not taken yet**, because taking them now would be guessing.

| Open decision | Who decides | Why it is still open |
|---|---|---|
| **How the screens are built** (more work on the server or more in the browser) | Product design (P04) | It depends on what the screens are, and they have not been designed. **Left open on purpose so the design is not tied down.** |
| **How accounts and passwords are handled** (build it or hire a vendor) | David | One key fact was verified on 12 August 2026 and **the result changed the analysis**: two vendors do let you take the passwords with you. The recommendation is being honestly re-run rather than defended. |
| **Whether a map is drawn, and what kind** | Product design (P04) | The rule that is firm: **a provider's exact location is never shown publicly.** That does not prevent showing an area or a city. |
| **Which geolocation vendor** | A lawyer | Two points in the vendor's legal terms are unresolved. **A legal interpretation must not be written down as though it were a technical fact.** |
| **Which region the system runs in** | You, when you decide the launch country | Choosing a region now would decide the country by accident. |
| **Whether to test AI assistance for filling in requests** | Product design (P04) | A bounded, optional, spend-capped idea: the customer writes *"I need a mariachi for a quinceañera in Houston next Saturday, 150 people, around $1,500"*, the system proposes the fields, **and the customer confirms or corrects them.** It does not decide results and does not replace the form. |

---

*Fuentes internas / Internal sources: `docs/03-technology/cost-model.md` (única fuente aritmética / single arithmetic source of truth) · `cost-alternatives.md` · `p03-decision-reconciliation.md` · `adr/ADR-013`–`ADR-020`.*

*Precios verificados en páginas oficiales de los proveedores el **11 de agosto de 2026**; los datos de proveedores de autenticación, el **12 de agosto de 2026**. Deben re-verificarse antes de contratar. Ninguna tecnología está adoptada. / Prices verified against official vendor pages on **11 August 2026**; authentication vendor facts on **12 August 2026**. They must be re-verified before purchase. No technology is adopted.*
