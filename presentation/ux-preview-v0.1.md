# Vista previa de experiencia Superola v0.1 / Superola UX Preview v0.1

> **Estado / Status:** `PROPUESTA — VALIDACIÓN DEL OWNER REQUERIDA` / `PROPOSED — OWNER VALIDATION REQUIRED`. Nada de esto está aprobado. Esto es **estructura**, no diseño visual: describe qué pasa y en qué orden, no cómo se ve. **No hay ninguna decisión de color, tipografía ni marca** — esas decisiones no se tomaron y no se toman en esta etapa. / Nothing here is approved. This is **structure**, not visual design: it describes what happens and in what order, not how it looks. **No decision on color, typography, or branding has been made** — those decisions were not taken and are not taken at this stage.
>
> **Propósito / Purpose:** Explicar cómo funciona Superola para el cliente y para el proveedor, en lenguaje de negocio. / Explain how Superola works for the customer and for the provider, in business language.
>
> **Importante / Important:** Esto se diseñó sobre supuestos de trabajo, no sobre respuestas suyas. La sección 4 muestra exactamente qué cambia si usted decide distinto. / This was designed on working assumptions, not on your answers. Section 4 shows exactly what changes if you decide differently.
>
> **Uso / How to use:** Para 2–4 slides use las secciones 1, 2 y 3, y la tabla de la sección 4. / For 2–4 slides use sections 1, 2, and 3, plus the section 4 table.

---

## 1. Cómo funciona para el cliente / How it works for the customer

### Español

```text
"Necesito un mariachi para el quinceañero de mi hija"
  → Elijo el servicio y el lugar
  → Veo proveedores que sí pueden servir ahí
  → Elijo UNO deliberadamente
  → Contesto solo lo que ese servicio necesita saber
  → Confirmo que el contacto que dejé es mío, y se envía
  → El proveedor responde con un precio, con una pregunta, o con un no
  → Conversamos
  → Decido
  → AQUÍ TERMINA EL PRIMER LANZAMIENTO
```

- **El cliente elige al proveedor. Superola no reparte la solicitud a todo el mundo.** Una solicitud va a un proveedor elegido a propósito. Si el cliente quiere contactar a otro, eso es otra decisión suya, hecha a mano.
- **Buscar, ver perfiles y armar la solicitud no requiere cuenta.** La cuenta y la confirmación del contacto aparecen al enviar, que es cuando el cliente ya invirtió esfuerzo y tiene más sentido pedírsela. Si abandona en ese paso, **su trabajo queda guardado y no se pierde** — pero el proveedor todavía no vio nada.
- **La fecha que pone el cliente es lo que necesita, no una promesa del sistema.** Superola nunca dice que un proveedor tenga la fecha libre. Quien contesta eso es el proveedor, dentro de la solicitud.
- **Si no hay resultados, se dice la verdad.** Se distingue "no hay ese servicio" de "no hay nadie en esa zona", se ofrece ampliar la búsqueda diciendo qué se está ampliando, y se registra que alguien buscó algo que no existe todavía. **No se convierte una búsqueda vacía en un envío masivo.**
- **Un evento puede necesitar varios servicios.** En el primer lanzamiento cada solicitud es de un servicio. Para pedir otro, el cliente parte de la solicitud anterior — la fecha, el tipo de evento y el lugar ya vienen puestos — y elige a otro proveedor.

### English

```text
"I need a mariachi for my daughter's quinceañera"
  → I choose the service and the place
  → I see providers who can actually serve there
  → I pick ONE deliberately
  → I answer only what that service needs to know
  → I confirm the contact I gave is mine, and it is sent
  → The provider answers with a price, with a question, or with a no
  → We talk
  → I decide
  → THE FIRST LAUNCH ENDS HERE
```

- **The customer picks the provider. Superola does not blast the request to everyone.** One request goes to one deliberately chosen provider. Contacting another is another decision the customer makes by hand.
- **Searching, viewing profiles, and composing the request need no account.** The account and the contact confirmation appear at send, when the customer has already invested effort and the ask has the best odds. If they abandon at that step, **their work is stored and not lost** — but the provider has seen nothing yet.
- **The date the customer enters is what they need, not a promise from the system.** Superola never states that a provider has that date free. The provider answers that inside the request.
- **When there are no results, we say so honestly.** We distinguish "this service does not exist here" from "nobody covers that area", we offer to widen the search while naming what is being widened, and we record that someone looked for something that does not exist yet. **An empty search is never turned into a mass send.**
- **One event can need several services.** In the first launch each request covers one service. To ask for another, the customer starts from the previous request — date, event type, and place already filled in — and chooses a different provider.

---

## 2. Cómo funciona para el proveedor / How it works for the provider

### Español

```text
Me registro
  → Describo mi negocio
  → Digo qué hago y dónde lo hago
  → Publico
  → Recibo solicitudes relevantes
  → Respondo con un precio, con una pregunta, o con un no
  → Conversamos
  → AQUÍ TERMINA EL PRIMER LANZAMIENTO
```

- **El proveedor ve desde la primera pantalla qué le falta para aparecer.** Una lista viva de requisitos, cada uno diciendo por qué se pide y qué destraba. Todo se guarda en cada paso. **Puede ver cómo se vería su perfil antes de completarlo todo** — lo que no puede pasar es que se publique a medias sin que él lo sepa.
- **El proveedor decide si responde y qué responde.** Un precio, una pregunta para entender mejor, o un no. **No responder también es una salida legítima** y nunca se presenta como una falta ni como un juicio: Superola no promete tiempos de respuesta, ni al cliente ni al proveedor.
- **Un no puede llevar un motivo, opcional y escrito por el proveedor.** No es un castigo ni una nota. Sirve para que Superola entienda por qué el marketplace no cerró.
- **El proveedor puede pausar la entrada de solicitudes cuando quiera**, y reanudarla igual de fácil. **Pausado no significa "esa fecha está ocupada"** y nunca se muestra así: significa que ahora mismo no quiere recibir pedidos.
- **Sólo llegan solicitudes que él puede atender:** de su categoría y de una zona que él mismo declaró servir. Cuando hay precio, siempre lleva su moneda explícita (USD como base).
- **No es un CRM.** No hay embudos, ni etiquetas, ni reportes, ni calendario. Si una pantalla no sirve para tomar una decisión del marketplace, no entra.

### English

```text
I register
  → I describe my business
  → I say what I do and where I do it
  → I publish
  → I receive relevant requests
  → I answer with a price, with a question, or with a no
  → We talk
  → THE FIRST LAUNCH ENDS HERE
```

- **From the first screen the provider sees what is still missing to appear.** A live requirements list, each item saying why it is asked and what it unlocks. Everything is saved at every step. **They can preview their public profile before completing everything** — what must not happen is a half-finished profile going public without them knowing.
- **The provider decides whether to answer and what to say.** A price, a question to understand better, or a no. **Not answering is also a legitimate outcome** and is never presented as a fault or a judgement: Superola promises no response times, to the customer or to the provider.
- **A no can carry a reason, optional and written by the provider.** It is not a penalty and not a score. It exists so Superola understands why the marketplace did not close.
- **The provider can pause incoming requests whenever they want**, and resume just as easily. **Paused does not mean "that date is taken"** and is never shown that way: it means they do not want requests right now.
- **Only requests they can serve arrive:** in their category, and in an area they themselves declared they serve. Where there is a price, it always carries its currency explicitly (USD as the base).
- **It is not a CRM.** No pipelines, no tags, no report builder, no calendar. If a screen cannot name the marketplace decision it supports, it does not ship.

---

## 3. El principio de la solicitud / The request-intake principle

Esta sección es la idea central de esta presentación. / This section is the central idea of this presentation.

### Español

**La regla: preguntar solo lo que el servicio elegido realmente necesita.**

Un flujo de solicitud de un competidor llega a pedir **veinticuatro cosas**, las mismas para todos: le pregunta a un pastelero cosas de salón, y al que busca salón le pregunta cosas de músico. Cada categoría nueva empeora ese formulario único, porque hay que agregarle preguntas que a casi nadie le sirven.

Superola hace lo contrario. **Las preguntas salen del servicio que el cliente eligió.** Una solicitud de mariachi es corta: qué evento, dónde, qué fecha, a qué hora, cuánto dura, y poco más. Una solicitud de salón pregunta otras cosas — cuánta gente, por ejemplo, que ahí sí decide si el salón sirve o no. **Agregar una categoría nueva no agrega una pantalla nueva**, porque las preguntas son datos que se configuran, no formularios hechos a mano.

La meta de diseño es que una solicitud de mariachi se arme con **cinco respuestas en el formulario — tres de ellas ya contestadas al buscar — y diez u once datos en total para poder enviarla**, contando el nombre, el contacto y la confirmación de ese contacto. Se cuentan los diez u once porque el flujo del competidor también incluye esos datos: comparar cinco contra veinticuatro sería comparar dos cosas distintas. Dos advertencias honestas:

- **Esas cifras son una meta de diseño, no un resultado medido.** Es el objetivo con el que se diseñó el flujo, no algo observado con clientes reales: Superola no tiene todavía ninguna prueba de uso. Lo que confirmaría esta meta es medir cuántas solicitudes se abandonan y en qué pregunta exacta.
- **La comparación viene de un flujo de un competidor que David observó y nos pasó.** Son las veinticuatro cosas que ese flujo pide, tal como nos llegaron. **No la volvimos a verificar por nuestra cuenta**, se registró solo al nivel de qué se pide — sin la redacción exacta y sin el orden exacto — y **ningún requisito de Superola se deriva de ella**: es contexto, no fundamento.

**Y lo más importante para su decisión: la fecha del cliente es un pedido, no una promesa.** Cuando un cliente dice "lo necesito para ese sábado", Superola registra que eso es lo que el cliente necesita y se lo transmite al proveedor. **En ningún lado dice que el proveedor tenga esa fecha libre**, porque hoy Superola no puede comprobarlo. Quien contesta si la fecha funciona es el proveedor, dentro de la solicitud, con sus palabras. **Esa es la decisión que sigue pendiente de usted:** si "disponible" tiene que pasar a significar un compromiso de fecha, eso cambia el producto — la sección 4 dice exactamente cuánto.

### English

**The rule: ask only what the chosen service actually needs.**

A competitor's request flow asks for **twenty-four things**, the same for everyone: it asks a cake baker about venues, and asks someone looking for a venue about musicians. Every new category makes that single universal form worse, because questions get added that almost nobody needs.

Superola does the opposite. **The questions come from the service the customer chose.** A mariachi request is short: what event, where, what date, what time, how long, and little else. A venue request asks different things — headcount, for instance, which there genuinely decides whether the venue works at all. **Adding a new category adds no new screen**, because the questions are configured data, not hand-built forms.

The design target is that a mariachi request is composed in **five answers — three of them already given while searching — and ten to eleven items in total before it can be sent**, counting the name, the contact, and the confirmation of that contact. The ten-to-eleven figure is the one to compare, because the competitor flow includes those items too: comparing five against twenty-four would be comparing two different things. Two honest caveats:

- **Those figures are a design target, not a measured result.** It is the goal the flow was designed against, not something observed with real customers: Superola has no usability evidence yet. What would confirm it is measuring how many requests are abandoned and at exactly which question.
- **The comparison comes from a competitor flow David observed and supplied.** It is the twenty-four things that flow asks for, as supplied. **We did not independently re-verify it**, it was recorded only at the level of what is asked — not the exact wording and not the exact order — and **no Superola requirement derives from it**: it is context, not foundation.

**And the point that matters most for your decision: the customer's date is a request, not a promise.** When a customer says "I need it that Saturday", Superola records that this is what the customer needs and passes it to the provider. **Nowhere does it state that the provider has that date free**, because today Superola cannot verify that. The provider is the one who confirms whether the date works, inside the request, in their own words. **That is the decision still pending from you:** if "available" has to mean a date commitment, that changes the product — section 4 says exactly how much.

---

## 4. Qué cambia si… / What changes if…

| Si usted decide… / If you decide… | Lo que NO cambia / What does NOT change | Lo que sí cambia / What does change | Qué haría falta primero / What it would require first |
|---|---|---|---|
| **Los pagos entran en el primer lanzamiento** / **Payments are in the first launch** | La forma de buscar, elegir proveedor, preguntar y conversar queda igual. El cliente sigue eligiendo a un proveedor a propósito. / Searching, choosing a provider, asking, and talking stay as they are. The customer still picks one provider deliberately. | **Es el cambio más grande de la lista.** Aparecen pantallas que hoy no existen: reservar, cobrar, pagarle al proveedor, devoluciones y disputas. Y cambia qué puede afirmar el producto: una cotización deja de ser solo una cotización, y "reseña" pasa a significar otra cosa. / **The largest change on this list.** Screens that do not exist today appear: booking, charging, paying the provider, refunds, and disputes. And what the product may claim changes: a quote stops being only a quote, and "review" comes to mean something else. | Una decisión suya sobre dónde termina el primer lanzamiento, y trabajo legal y fiscal por país. Todo esto está fuera de las cifras de tecnología presentadas hasta ahora (USD). / Your decision on where the first launch ends, plus legal and tax work per country. All of it sits outside the technology figures presented so far (USD). |
| **La solicitud debe ir a varios proveedores a la vez** / **The request must go to several providers at once** | Buscar, los perfiles, el borrador del cliente y la conversación quedan igual. El borrador privado del cliente ya sirve para esto. / Searching, profiles, the customer's draft, and the conversation stay as they are. The customer's private draft already serves this. | Deja de ser cierto que "el cliente eligió a este proveedor a propósito", que es hoy la promesa más fuerte del producto para el proveedor. Aparecen reglas nuevas: a cuántos como máximo, qué ve cada uno, qué pasa con los demás cuando uno contesta, y cómo se evita que se vuelva correo basura. / It stops being true that "the customer chose this provider deliberately", which is today the product's strongest promise to the provider. New rules appear: how many at most, what each one sees, what happens to the others when one answers, and how it is kept from becoming junk mail. | Decidir si el cliente lo pide explícitamente cada vez o el sistema lo hace solo — y una postura suya sobre qué le prometemos al proveedor. **Nada de lo construido se rehace.** / Deciding whether the customer asks for it explicitly each time or the system does it automatically — and a position from you on what we promise the provider. **Nothing already built gets redone.** |
| **Un calendario real con fechas garantizadas** / **A real calendar with guaranteed dates** | Buscar, perfiles, solicitudes y conversación siguen funcionando igual. / Searching, profiles, requests, and conversation keep working the same. | Hoy la fecha es contexto de la solicitud; pasaría a ser un compromiso que el producto afirma. Eso obliga a modelar calendarios y recursos, y **cada categoría los tiene distintos**: un salón tiene salones, un mariachi tiene grupos, un transporte tiene vehículos. Y obliga a mantenerlos al día: un calendario desactualizado es peor que no tener calendario, porque promete algo falso. / Today the date is request context; it would become a commitment the product asserts. That forces modelling calendars and resources, and **each category has different ones**: a venue has rooms, a mariachi has groups, a transport company has vehicles. And it forces keeping them current: a stale calendar is worse than no calendar, because it promises something false. | Su definición de qué promete "disponible" — es la pregunta abierta que más pesa sobre el diseño de experiencia — y una respuesta a quién mantiene ese calendario y con qué incentivo. / Your definition of what "available" promises — the open question that weighs most on experience design — and an answer on who maintains that calendar and with what incentive. |
| **Una app móvil** / **A mobile app** | Todo. Las mismas pantallas, las mismas reglas, la misma información. Y desde ya **todo se diseña para que funcione en un teléfono**, porque los proveedores trabajan desde el teléfono. / Everything. Same screens, same rules, same information. And everything is already designed **to work on a phone**, because providers work from their phones. | Aparece un canal más que mantener, con sus tiendas de aplicaciones, sus aprobaciones y sus versiones viejas conviviendo con la nueva. El producto no cambia; el trabajo de sostenerlo sí. / One more channel to maintain appears, with its app stores, its approvals, and its old versions living alongside the new one. The product does not change; the work of sustaining it does. | Evidencia de que el sitio en el teléfono no alcanza — por ejemplo, proveedores que no responden a tiempo por fricción del canal, no por falta de interés. / Evidence that the site on a phone is not enough — for example, providers not answering in time because of channel friction, not lack of interest. |
| **Un mapa dibujado en la pantalla** / **A map drawn on screen** · *Recomendación: no dibujar ninguno en el primer lanzamiento / Recommendation: draw none in the first version* | Nada de lo que el cliente necesita saber sobre el lugar. Se dice en palabras: la ciudad o la zona, la cobertura que el proveedor declaró, y la dirección del salón cuando el proveedor eligió publicarla — más un enlace que abre la aplicación de mapas del propio cliente, la que ya usa todos los días. Buscar, elegir proveedor y enviar la solicitud quedan igual. / Nothing the customer needs to know about location. It is said in words: the city or the area, the coverage the provider declared, and the venue address where the provider chose to publish it — plus a link that opens the customer's own map application, the one they already use every day. Searching, choosing a provider, and sending the request stay as they are. | Aparece la pantalla más cara de sostener del producto: es la más difícil de hacer usable en un teléfono, donde compite con la lista de resultados por toda la pantalla, y la más difícil de dejar accesible para quien no ve bien. Y hay un riesgo de exactitud: dibujar un punto a partir de una ciudad hace parecer preciso algo que no lo es. Una regla no cambia nunca: **la dirección exacta del proveedor es privada y no se muestra en público, con mapa o sin mapa.** / The most expensive screen in the product appears: the hardest to make usable on a phone, where it competes with the result list for the whole screen, and the hardest to keep accessible for someone who does not see well. And there is an accuracy risk: drawing a point from a city makes something look precise when it is not. One rule never changes: **a provider's exact address is private and is never shown publicly, map or no map.** | Cualquiera de tres cosas observadas, no opinadas: que el lanzamiento arranque con salones y que al evaluarlo se vea que los clientes no entienden dónde queda cada uno; que aparezca un servicio que necesite la dirección exacta del cliente, como una entrega a domicilio; o que se mida que los proveedores reciben pedidos de fuera de su zona porque los clientes leen mal la cobertura escrita. Si pasa alguna, se vuelve a costear antes de construirlo. / Any one of three observed things, not opinions: the launch starts with venues and evaluation shows customers cannot tell where each one is; a service appears that needs the customer's exact address, such as home delivery; or it is measured that providers receive requests from outside their area because customers misread the written coverage. If any occurs, it gets re-costed before it is built. |
| **IA que ayude a llenar la solicitud** / **AI helping fill in the request** | El formulario sigue existiendo y sigue mandando. La IA nunca decide qué proveedores aparecen, nunca decide si una solicitud es válida, y **si falla, el cliente escribe como siempre**. Es opcional y se puede saltar. / The form still exists and still decides. AI never decides which providers appear, never decides whether a request is valid, and **if it fails, the customer types as always**. It is optional and skippable. | El cliente podría describir su evento en una frase y el sistema propondría los campos ya llenos, **para que el cliente los confirme o los corrija**. El modelo propone; el formulario dispone. / The customer could describe their event in one sentence and the system would propose the fields already filled in, **for the customer to confirm or correct**. The model proposes; the form disposes. | Cuatro condiciones, todas: que el formulario guiado ya exista y se esté midiendo dónde se abandona; que el abandono se concentre justo ahí; una resolución de privacidad sobre qué texto del cliente sale del sistema; y un tope de gasto con interruptor de apagado. Hoy no se cumple ninguna, y por eso **no entra en el primer lanzamiento**. / Four conditions, all of them: the guided form already exists and abandonment is being measured; abandonment concentrates exactly there; a privacy resolution on what customer text leaves the system; and a spend ceiling with a kill switch. None holds today, which is why **it is not in the first launch**. |

---

*Fuentes internas / Internal sources: `docs/04-ux/README.md`, `customer-journey.md`, `request-intake.md`, `provider-onboarding.md`, `provider-workspace.md`, `map-decision.md`; `docs/02-architecture/domain-model.md`, `decision-branches.md`, `adr/ADR-003`, `ADR-005`, `ADR-006`, `ADR-010`; `docs/03-technology/ai-evaluation.md`; `docs/07-research/gigsalad-request-flow-capture.md` — la captura del flujo del competidor citada en la sección 3, observada y provista por David, no verificada de forma independiente / the competitor flow capture cited in section 3, observed and supplied by David, not independently verified. Vistas previas relacionadas / Related previews: `presentation/architecture-preview-v0.1.md`, `presentation/technology-preview-v0.1.md`.*

*Decisión del owner que sigue abierta y que condiciona esta sección 3 / Open owner decision governing section 3: `G-06` — qué promete la palabra "disponible" / what the word "available" promises. Ninguna decisión de este documento está aprobada. No se decide aquí ningún color, tipografía, marca, proveedor, precio ni plazo. / No decision in this document is approved. No color, typography, branding, vendor, price, or timeline is decided here.*
