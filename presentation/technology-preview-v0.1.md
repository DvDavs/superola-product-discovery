# Vista previa de tecnología Superola v0.1 / Superola Technology Preview v0.1

> **Estado / Status:** `PROPUESTA — APROBACIÓN DE DAVID REQUERIDA` / `PROPOSED — DAVID APPROVAL REQUIRED`
>
> **Propósito / Purpose:** Explicar qué tecnología usaría Superola, cuánto costaría y qué la encarecería. En lenguaje de negocio. / Explain what technology Superola would use, what it would cost, and what would make it expensive. In business language.
>
> **Importante / Important:** Ninguna tecnología está comprada ni contratada. Todos los precios se verificaron el 11 de agosto de 2026 y hay que volver a verificarlos antes de contratar. / No technology is bought or contracted. All prices were verified on 11 August 2026 and must be re-verified before purchase.
>
> **Uso / How to use:** Para 1–2 slides use **solo** la sección 1 y la tabla de la sección 3. El resto es respaldo. / For 1–2 slides use **only** section 1 and the section 3 table. The rest is backing material.

---

## 1. El número que importa / The number that matters

### La factura de tecnología / The technology bill

| Etapa / Stage | **Tecnología / Technology** |
|---|---|
| **Piloto / Pilot** | **USD $76 / mes · month** |
| **Marketplace temprano / Early Marketplace** | **USD $193 / mes · month** |
| **Crecimiento / Growth** | **USD $561 / mes · month** |

### Español

**Esa es la factura: lo que se le paga a proveedores de tecnología.** En el piloto cuesta menos que un plan de celular.

**El trabajo humano de operar el marketplace se estima aparte y no se suma aquí** (sección 3.1). No es un pronóstico ni un compromiso, y depende de decisiones que todavía no se toman.

Aun así, la comparación sirve para una cosa concreta y es la conclusión que importa: **el trabajo humano pesa mucho más que la factura de tecnología en todas las etapas.** Por eso **una decisión que ahorra USD $30 al mes en la factura pero agrega una hora de trabajo humano al mes es una pérdida.** Todo el diseño técnico se optimizó con ese criterio, no por el precio del servidor.

### English

**That is the invoice: what gets paid to technology vendors.** At pilot stage it is less than a phone plan.

**The human work of operating the marketplace is estimated separately and is not added here** (section 3.1). It is not a forecast and not a commitment, and it depends on decisions not yet taken.

The comparison still earns its place, and this is the conclusion that matters: **human work outweighs the technology bill at every stage.** That is why **a decision that saves USD $30 a month on the invoice but adds one hour of monthly human work is a loss.** The whole technical design was optimised on that basis, not on the price of a server.

> **Todas las cifras en dólares estadounidenses (USD). / All figures in United States dollars (USD).**
>
> Explicación completa y no técnica de a dónde va el dinero: `presentation/cost-explainer-v0.1.md`. / Full non-technical explanation of where the money goes: `presentation/cost-explainer-v0.1.md`.

---

## 2. La forma técnica recomendada / The recommended technical shape

### Español

- **Un solo sistema, no varios.** Superola es una sola aplicación con partes bien separadas por dentro. Partirla en varios servicios costaría más y no resolvería ningún problema que Superola tenga hoy.
- **Una sola base de datos.** Toda la información del marketplace vive en un solo lugar, con respaldo automático y capacidad de restaurar a un momento anterior en el tiempo. Además guardamos una copia cifrada **fuera del proveedor**, porque si se pierde la cuenta del proveedor, sus respaldos se pierden con ella.
- **Las páginas públicas se arman en el servidor** — punto de partida, **todavía no decidido**. Hace las páginas públicas rápidas, baratas y legibles para buscadores. **Pero cómo se construyen las pantallas internas se decide en la etapa de diseño de producto (P04), no ahora**, y esa etapa puede concluir que algunas pantallas necesitan más trabajo en el navegador. Lo que sí es firme en cualquier caso: las páginas públicas siguen siendo legibles sin depender del navegador.
- **Las fotos se guardan en un servicio que no cobra por servirlas.** Ese fue el ahorro individual más grande de todo el análisis.
- **Los videos y el audio no se alojan: se enlazan** desde donde el proveedor ya los tiene publicados.

### English

- **One system, not several.** Superola is a single application with well-separated parts inside. Splitting it into several services would cost more and would solve no problem Superola has today.
- **One database.** All marketplace information lives in one place, with automatic backups and the ability to restore to an earlier point in time. We also keep an encrypted copy **outside the provider**, because if the provider account is lost, its backups are lost with it.
- **Public pages are built on the server** — a starting point, **not yet decided**. It makes public pages fast, cheap, and readable by search engines. **But how the internal screens are built is decided in the product design stage (P04), not now**, and that stage may conclude that some screens need more work in the browser. What is firm either way: public pages stay readable without depending on the visitor's browser.
- **Photos live in a service that does not charge to serve them.** That was the single largest saving in the whole analysis.
- **Video and audio are not hosted — they are linked** from wherever the provider already publishes them.

---

## 3. Bandas de costo aproximadas / Approximate cost bands

Esta es la tabla para el slide. / This is the table for the slide.

| Etapa / Stage | Proveedores publicados / Published providers | **Tecnología / Technology** |
|---|---|---|
| **Piloto / Pilot** | 150 | **USD $76 / mes · month** |
| **Marketplace temprano / Early Marketplace** | 1,500 | **USD $193 / mes · month** |
| **Crecimiento / Growth** | 10,000 | **USD $561 / mes · month** |

> **Español.** Los **volúmenes** de cada etapa son **supuestos, no datos.** Superola no tiene todavía información de tráfico. Los ~43,000 registros del sitio actual son un dato reportado y **no auditado**, y no se usó para dimensionar nada. Las bandas sirven para comparar decisiones entre sí, **no para presupuestar**.
>
> **English.** The **volumes** behind each stage are **assumptions, not data.** Superola has no traffic evidence yet. The ~43,000 records from the current site are owner-reported and **unaudited**, and were not used to size anything. The bands exist to compare decisions against each other, **not to budget**.
>
> *Precios verificados en fuentes oficiales el 2026-08-11; deben re-verificarse antes de contratar. / Prices verified against official sources on 2026-08-11; re-verify before purchase.*

### 3.1 El costo humano — ilustrativo, no comprometido / The human cost — illustrative, not committed

| Etapa / Stage | Horas / mes · Hours / month | Costo ilustrativo / Illustrative cost |
|---|---|---|
| **Piloto / Pilot** | ~12 h | ~USD $578 |
| **Marketplace temprano / Early Marketplace** | ~60 h | ~USD $2,592 |
| **Crecimiento / Growth** | ~350 h | ~USD $14,298 |

> **Español — esta tabla NO se suma a la de arriba.** Es un **ejercicio de sensibilidad**, no un pronóstico, no un presupuesto y **no un costo mensual comprometido de Superola**. Depende de dos supuestos que se declaran porque toda la cifra depende de ellos: **USD $75/hora** para trabajo técnico de infraestructura y **USD $40/hora** para operación del marketplace, más volúmenes de trabajo sin base medida. **Si esos números cambian, la tabla cambia en proporción directa.** Si usted o David hacen ese trabajo, el costo en efectivo es cero y el costo real es tiempo no disponible para otra cosa.
>
> **English — this table is NOT added to the one above.** It is a **sensitivity exercise**, not a forecast, not a budget, and **not a committed monthly Superola operating cost**. It rests on two assumptions, stated because the whole figure depends on them: **USD $75/hour** for technical infrastructure work and **USD $40/hour** for marketplace operations, plus workload volumes with no measured basis. **If those numbers change, the table moves in direct proportion.** If you or David do that work, the cash cost is zero and the real cost is time not spent on something else.

**No incluye / Not included:** pagos y reservas (decisión suya, pendiente) · migración del sitio actual (pendiente de permiso legal) · marketing, adquisición de proveedores, contenido y asesoría legal. / Payments and booking (your decision, pending) · migration of the current site (pending legal permission) · marketing, provider acquisition, content, and legal counsel.

---

## 4. Qué encarece esto, en orden / What makes this expensive, in order

| | Español | English |
|---|---|---|
| **1** | **El trabajo humano de operar el marketplace.** Revisar categorías, resolver duplicados, atender reportes, dar seguimiento a proveedores inactivos, y soporte a clientes y proveedores. Es 88–96% del costo total en todas las etapas. | **The human work of operating the marketplace.** Reviewing categories, resolving duplicates, handling reports, following up on inactive providers, and supporting customers and providers. It is 88–96% of total cost at every stage. |
| **2** | **Los bytes que se sirven a visitantes anónimos.** Este costo crece con el **éxito** de la estrategia de atraer tráfico público — no con las solicitudes que generan valor. Por eso las fotos van a un servicio sin cargo por servirlas, y por eso el video se enlaza en vez de alojarse. | **The bytes served to anonymous visitors.** This cost grows with the **success** of the public-traffic strategy — not with the requests that create value. That is why photos go to a service with no charge to serve them, and why video is linked rather than hosted. |
| **3** | **Un segundo idioma en operación.** Estructuralmente casi no cuesta: el sistema ya está hecho para varios idiomas. Operativamente cuesta mucho y para siempre: todo el contenido se escribe y mantiene dos veces, y **cada cola de trabajo humano se duplica**. No es una decisión técnica, es una decisión de personal. | **A second operating language.** Structurally it costs almost nothing — the system is already built for it. Operationally it costs a lot, permanently: all content is written and maintained twice, and **every human work queue doubles**. This is not a technical decision, it is a staffing decision. |
| **4** | **Migrar los registros del sitio actual.** Hoy ese costo es **cero** porque no está aprobado. Si se aprueba, la revisión manual pasa a ser el costo dominante y aparece la cola de trabajo más cara del producto: resolver quién es el dueño legítimo de un registro. | **Migrating records from the current site.** Today that cost is **zero** because it is not approved. If approved, manual review becomes the dominant cost and the most expensive work queue in the product appears: deciding who legitimately owns a record. |
| **5** | **Pagos y reservas.** El grupo de costos más grande de todo el programa, y no está incluido en ninguna cifra de arriba. | **Payments and booking.** The largest cost cluster in the entire programme, and it is not included in any figure above. |

---

## 5. Lo que deliberadamente NO agregamos / What we deliberately did NOT add

### Español

Cada una de estas cosas es normal en un marketplace grande. **Ninguna resuelve un problema que Superola tenga hoy**, y cada una tiene una condición escrita que la activaría:

- **Un motor de búsqueda aparte.** La base de datos hace la búsqueda, incluso en español con acentos y errores de tipeo. Un motor aparte se agrega solo cuando una medición lo justifique.
- **Un mapa dibujado en pantalla — todavía no se descarta, se decide en P04.** Lo que sí es firme: **la ubicación exacta de un proveedor es información privada y nunca se muestra en público.** Eso descarta el pin en la dirección del proveedor, **no descarta un mapa**: mostrar una ciudad, una zona aproximada o el área de cobertura no expone nada privado. Hoy no hay ningún requisito de V1 que necesite un mapa, y las cifras de arriba asumen que no se dibuja ninguno. **Si el diseño de producto concluye que hace falta, se vuelve a costear — no está prohibido por la arquitectura.**
- **Infraestructura de mensajería en tiempo real.** Las conversaciones de Superola son asincrónicas. Nadie espera respuesta en un segundo.
- **Inteligencia artificial en la búsqueda.** Recomendamos **no gastar** en esto todavía, y la razón es concreta: no sabemos aún si las búsquedas sin resultado fallan porque el sistema no entendió, o porque no hay proveedores. **Si es lo segundo, el dinero va a conseguir proveedores, no a un modelo.** **Esto no quiere decir que la IA no sirva para Superola.** Hay una idea acotada que P04 puede evaluar: **asistencia para llenar la solicitud.** El cliente escribe *"necesito un mariachi para un quinceañero en Houston el sábado, 150 personas, como $1,500"*, el sistema propone los campos ya estructurados y **el cliente los confirma o corrige.** Es opcional, con tope de gasto, no decide resultados y no reemplaza el formulario.
- **Herramientas de monitoreo de pago.** Las versiones gratuitas alcanzan y sobran en el piloto.

### English

Each of these is normal in a large marketplace. **None solves a problem Superola has today**, and each has a written condition that would trigger it:

- **A separate search engine.** The database performs the search, including Spanish with accents and typos. A separate engine is added only when a measurement justifies it.
- **A map drawn on screen — not ruled out; decided in P04.** What is firm: **a provider's exact location is private and is never shown publicly.** That rules out a pin at the provider's address; **it does not rule out a map** — showing a city, an approximate area, or coverage exposes nothing private. Today no V1 requirement needs a map, and the figures above assume none is drawn. **If product design concludes one is needed, it gets re-costed — the architecture does not forbid it.**
- **Real-time messaging infrastructure.** Superola's conversations are asynchronous. Nobody expects a reply within one second.
- **Artificial intelligence in search.** We recommend **not spending** on this yet, for a concrete reason: we do not yet know whether searches with no results fail because the system misunderstood, or because no providers exist. **If it is the second, the money belongs in acquiring providers, not in a model.** **That does not mean AI has no role in Superola.** There is one bounded idea P04 can evaluate: **assistance filling in the request.** The customer writes *"I need a mariachi for a quinceañera in Houston next Saturday, 150 people, around $1,500"*, the system proposes the structured fields, and **the customer confirms or corrects them.** Optional, spend-capped, decides no results, and replaces no form.
- **Paid monitoring tools.** Free tiers are more than sufficient at the pilot stage.

---

## 6. Cómo escala / How it scales

### Español

El diseño crece en tres pasos, y **cada paso tiene una condición medible que lo dispara** — no una fecha ni una corazonada:

1. **Piloto.** Una aplicación, una base de datos. ~USD $76 al mes.
2. **Marketplace temprano.** Se agrega una segunda copia de la aplicación — **por disponibilidad durante las actualizaciones, no por carga** — y una base de datos más grande. Se dispara cuando el uso del servidor se sostiene alto por una semana, o cuando la cola de trabajo humano se atrasa más de lo aceptable.
3. **Crecimiento.** Equipos más grandes y una base de datos de respaldo. Se dispara cuando la base de datos ya no cabe en memoria.

**Un dato que sorprende:** incluso en la etapa de crecimiento, todo el marketplace atiende menos de diez visitas por segundo en su momento pico. La tecnología recomendada tiene mucho margen. **El límite real no es técnico — es cuánta gente hace falta para operar el marketplace.**

### English

The design grows in three steps, and **each step has a measurable condition that triggers it** — not a date and not a hunch:

1. **Pilot.** One application, one database. ~USD $76 per month.
2. **Early Marketplace.** A second copy of the application is added — **for availability during updates, not for load** — plus a larger database. Triggered when server usage stays high for a week, or when the human work queue falls further behind than acceptable.
3. **Growth.** Larger machines and a standby database. Triggered when the database no longer fits in memory.

**One surprising fact:** even at the growth stage, the entire marketplace serves fewer than ten visits per second at peak. The recommended technology has ample headroom. **The real limit is not technical — it is how many people it takes to operate the marketplace.**

---

## 7. Lo que hace falta de usted / What we need from you

### Español

Nada de esta sección es técnico. Son decisiones suyas que cambian el costo de forma material:

1. **¿Dónde termina el primer lanzamiento?** Si incluye pagos y reservas, aparece el grupo de costos más grande del programa, que no está en ninguna cifra de arriba.
2. **¿Un país y un idioma, o dos?** Estructuralmente da igual. Operativamente, dos idiomas **duplican cada cola de trabajo humano** — y ese es el costo dominante.
3. **¿Tenemos permiso legal para migrar o contactar los registros del sitio actual?** Hoy ese costo es cero porque asumimos que no. Si la respuesta cambia, cambia la cifra más grande de la tabla.

**Y tres decisiones técnicas que dejamos abiertas a propósito**, porque cerrarlas ahora sería adivinar:

- **Cómo se construyen las pantallas.** Lo decide el diseño de producto, no la tecnología.
- **Cómo se manejan cuentas y contraseñas.** Verificamos un dato clave el 12 de agosto de 2026 y **el resultado cambió el análisis** — dos proveedores sí permiten llevarse las contraseñas. Estamos re-evaluando la recomendación con honestidad en vez de defenderla. **Esta decisión está en pausa.**
- **Cuál proveedor de geolocalización.** Depende de dos puntos legales que tiene que resolver un abogado, no un técnico.

### English

Nothing in this section is technical. These are your decisions, and each materially changes the cost:

1. **Where does the first launch end?** If it includes payments and booking, the largest cost cluster in the programme appears, and it is in none of the figures above.
2. **One country and one language, or two?** Structurally it makes no difference. Operationally, two languages **double every human work queue** — and that is the dominant cost.
3. **Do we have legal permission to migrate or contact the current site's records?** Today that cost is zero because we assume not. If the answer changes, the largest figure in the table changes.

**And three technical decisions we deliberately left open**, because closing them now would be guessing:

- **How the screens are built.** Product design decides that, not technology.
- **How accounts and passwords are handled.** We verified a key fact on 12 August 2026 and **the result changed the analysis** — two vendors do let you take the passwords with you. We are honestly re-running the recommendation rather than defending it. **This decision is on hold.**
- **Which geolocation vendor.** It turns on two legal points a lawyer has to settle, not an engineer.

---

*Fuentes internas / Internal sources: `docs/03-technology/technology-evaluation.md`, `cost-model.md`, `cost-alternatives.md`, `p03-decision-reconciliation.md`, `infrastructure-options.md`, `technology-radar.md`, `ai-evaluation.md`, `build-vs-buy.md`, `adr/ADR-013`–`ADR-020`. Explicación de costos para no técnicos / Non-technical cost explainer: `presentation/cost-explainer-v0.1.md`.*

*Todas las cifras en USD. Precios verificados en fuentes oficiales el 2026-08-11; datos de proveedores de autenticación el 2026-08-12. Deben re-verificarse antes de contratar. Ninguna tecnología está adoptada. / All figures in USD. Prices verified against official sources on 2026-08-11; authentication vendor facts on 2026-08-12. They must be re-verified before purchase. No technology is adopted.*
