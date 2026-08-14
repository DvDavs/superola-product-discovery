# Superola Owner Review v1 — David Speaker Notes

> **Status:** `PROPOSED — OWNER VALIDATION REQUIRED`
>
> These Spanish notes support the 16 core slides in `superola-owner-review-v1.md`. They are not owner-approved requirements, a script to read word-for-word, or a substitute for capturing the owner's answers.

## Slide 1 — Superola: From Listings to a Measurable Marketplace Exchange

**Objetivo al hablar**

Abrí con calma: “Quiero mostrarle lo que entendí, qué pude comprobar, qué recomiendo hoy y qué necesito confirmar con usted.”

No vendas certeza artificial. Decí explícitamente que la recomendación puede cambiar si la prioridad del negocio o la evidencia interna cambia.

**Puente**

“Antes de hablar de funcionalidades, quiero confirmar cuál creo que es el trabajo real de Superola.”

[Sources]
- `docs/01-product/product-vision.md`
- `plans/P06-mvp-roadmap.md`

## Slide 2 — Superola can become the exchange—not only the directory

**Explicación sugerida**

“Mi lectura es que Superola tiene dos clientes del sistema. La persona necesita encontrar a alguien pertinente y avanzar con confianza. El proveedor necesita oportunidades relevantes que pueda evaluar y responder. Superola crea valor cuando ese intercambio se vuelve confiable, visible y repetible.”

Preguntá: “¿Esto describe el negocio que usted quiere construir, o hay otro resultado principal que estoy dejando afuera?”

No presentes “marketplace” como algo que el owner ya aprobó. Es la interpretación de David, apoyada por una directiva interna y por el trabajo de discovery.

**Puente**

“Con esa interpretación, revisé lo que una persona puede ver hoy y también modelé lo que todavía no se puede saber.”

[Sources]
- `docs/00-context/product-context.md`
- `docs/01-product/actors.md`
- `docs/01-product/product-vision.md`

## Slide 3 — I investigated the public product—and kept its limits visible

**Explicación sugerida**

“Pude revisar navegación, perfiles, formularios, búsqueda, ubicación, taxonomía, exposición de contacto y señales técnicas públicas. También trabajé journeys, arquitectura, costos, roadmap y operación. Pero no tuve acceso a base de datos, administración, analítica ni procesos privados.”

Remarcá que una ausencia pública no prueba una ausencia interna. El punto de la slide es credibilidad por disciplina, no impresionar con volumen de investigación.

**Puente**

“Con ese límite claro, hay cuatro señales públicas que sí cambian qué conviene validar primero.”

[Sources]
- `docs/00-context/source-register.md`
- `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md`
- `docs/05-roadmap/migration-dependency.md`

## Slide 4 — The current platform gives us useful signals—not final answers

**Explicación sugerida**

“Encontré 43,361 URLs públicas de listings por dos métodos reproducibles. Usted reportó aproximadamente 43 mil registros. Los números se parecen, pero las unidades son distintas y no sabemos la relación.”

“En una muestra estratificada de 53 páginas, 47 referenciaron el mismo identificador de owner. Técnicamente fue el user id 1; ese detalle es opcional y sirve sólo para explicar el método. Esto alcanza para priorizar ownership en la auditoría; NO alcanza para extrapolar una distribución al sistema completo.”

“En una muestra de 33 listings hubo ubicaciones faltantes, centroides repetidos y un caso aproximadamente 600 km fuera. También hay duplicados, errores y términos fuera del dominio en taxonomía. La superficie ‘Solicitud de Reserva’ muestra públicamente un formulario de contacto, no un ciclo visible de reserva o pago.”

Usá la frase: “Lo valioso no es demostrar que todo está roto; es saber qué preguntas no podemos dejar para el final.”

**Puente**

“Esas señales me llevan a una primera versión que produzca nueva evidencia confiable.”

[Sources]
- `docs/00-context/interview-evidence.md`
- `docs/00-context/product-context.md`
- `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md` §§3.2, 4.4, 8.1, 12.2, 17.2–17.4

## Slide 5 — My current V1 recommendation: prove the request-to-response loop first

**Explicación sugerida**

“Mi recomendación actual es Branch L: Marketplace Leads, también llamado RFQ. En adelante lo explico simplemente como una solicitud. El cliente elige un proveedor, envía una solicitud estructurada, el proveedor aclara, rechaza, cotiza o no responde, y la conversación puede continuar. Después preguntamos al cliente si piensa avanzar.”

Aclará tres límites:

1. una solicitud va a un destinatario elegido;
2. una cotización no crea obligación contractual;
3. el outcome es autodeclarado, no una transacción verificada.

Preguntá temprano: “¿Para usted la primera versión puede terminar ahí, o necesariamente tiene que incluir reserva o pago?”

**Puente**

“Si aceptamos ese límite como hipótesis, así se ve para cada lado del marketplace.”

[Sources]
- `docs/01-product/product-vision.md`
- `docs/05-roadmap/mvp-definition.md`
- `docs/02-architecture/decision-branches.md`

## Slide 6 — The customer journey stays open until the moment of intent

**Explicación sugerida**

“No obligaría a crear cuenta para buscar, ver perfiles o empezar el pedido. La verificación aparece cuando existe la intención real de enviarlo. Antes del envío, el cliente ve el proveedor elegido y el contenido.”

Explicá que zero results no es un fracaso genérico: hay que diferenciar falta de categoría, cobertura geográfica u otra regla. Relajar una condición requiere una acción visible del cliente.

No prometas que los resultados ya estarán correctamente rankeados; la slide describe el flujo y los principios.

**Puente**

“Para que ese journey sea confiable, el lado del proveedor tiene que estar gobernado desde el inicio.”

[Sources]
- `docs/04-ux/customer-journey.md`
- `docs/04-ux/discovery-and-results.md`
- `docs/04-ux/request-intake.md`

## Slide 7 — Provider quality starts before the first lead arrives

**Explicación sugerida**

“El proveedor define un offering concreto, categoría, atributos, zona de servicio y perfil. Puede guardar y continuar. Antes de publicar ve una vista previa y un checklist; un perfil incompleto no aparece por accidente.”

“Accepting requests” significa que recibe nuevas solicitudes, no que toda fecha está disponible. Puede pausar intake. Y puede aclarar, rechazar u ofrecer: castigar el rechazo honesto produciría peores respuestas.

Preguntá qué señales de confianza cree el owner que deben ser obligatorias para publicar.

**Puente**

“La siguiente pregunta es qué pasa cuando el primer proveedor no encaja.”

[Sources]
- `docs/04-ux/provider-onboarding.md`
- `docs/04-ux/provider-workspace.md`
- `docs/04-ux/provider-profile.md`

## Slide 8 — Alternatives reuse effort—but never reuse consent

**Explicación sugerida**

“Si el cliente no quiere seguir con Provider A, Superola muestra alternativas elegibles. El cliente elige Provider B. Se copian a un nuevo draft sólo los datos compatibles del evento. Antes de enviar, revisa destinatario y contenido y confirma de nuevo.”

Decí textual: “Un borrador copiado no es una solicitud enviada.”

La opción futura de varios destinatarios requiere un conjunto visible y autorizado, límites y reglas contra spam. No digas “matching automático”; esa redacción vieja fue superada por el canon actual.

**Puente**

“Mostrar alternativas sirve sólo si primero protegemos la relevancia.”

[Sources]
- `docs/05-roadmap/mvp-definition.md`
- `docs/04-ux/request-intake.md`
- `docs/04-ux/customer-journey.md`

## Slide 9 — Relevance comes before ranking—and before sponsorship

**Explicación sugerida**

“Eligibility responde quién realmente puede aparecer: categoría, área de servicio, publicación y reglas de confianza. Ordering responde en qué orden aparecen los elegibles. Patrocinio pertenece a ordering, nunca a eligibility.”

Presentá precio, calidad de perfil, actividad reciente y comportamiento de respuesta como candidatos sujetos a definición, datos suficientes y revisión de fairness. No hay fórmula final aprobada.

**Puente**

“Esa separación permite monetizar sin vender la confianza.”

[Sources]
- `docs/02-architecture/adr/ADR-006-discovery-owns-eligibility-ordering-and-placement.md`
- `docs/02-architecture/adr/ADR-008-monetization-is-a-reserved-policy-seam.md`
- `docs/05-roadmap/measurement-plan.md`

## Slide 10 — Monetization should follow proof of provider value

**Explicación sugerida**

“Usted mencionó suscripción y espacios patrocinados por categoría y geografía. Los trato como ideas reales del negocio, pero no como precios ni políticas aprobadas.”

“Primero mediría supply publicable, búsquedas, solicitudes entregadas y respuesta. Después probaría un patrocinio pequeño, manual, por tarifa fija y tiempo limitado. Eso permite aprender sin construir campañas, presupuestos y CPC antes de necesitarlos.”

Mencioná que no se observó monetización pública vigente de listings. Digital Consulting sí es una línea pública real y conviene decidir más adelante cómo se relaciona con el marketplace.

**Puente**

“Antes de pensar que la base actual resuelve el cold start, necesitamos auditar qué representa.”

[Sources]
- `docs/01-product/monetization.md`
- `docs/05-roadmap/phased-roadmap.md`
- `docs/05-roadmap/measurement-plan.md`
- `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md` §§11, 14.2

## Slide 11 — Legacy data is a decision tree, not a migration promise

**Explicación sugerida**

“El orden correcto es permiso, acceso controlado, auditoría interna, clasificación, decisión del owner con revisión legal o de privacidad cuando corresponda, y recién después implementación. Posibles resultados: migrar, normalizar, pedir claim, re-consentir, revisar, deduplicar, suprimir o archivar.”

No pidas credenciales ni exportaciones informales en la reunión. Pedí acordar el proceso de acceso y los controles.

Explicá que supply fresca permite empezar un piloto sin contaminarlo con registros no auditados, pero tampoco resuelve automáticamente las obligaciones del sistema legacy.

**Puente**

“Separar esas dos pistas permite avanzar sin inventar una fecha de migración.”

[Sources]
- `docs/05-roadmap/migration-dependency.md`
- `docs/06-migration/legacy-data-strategy.md`
- `docs/00-context/sources/live-platform-audit-superola-2026-08-13.md`

## Slide 12 — The roadmap advances when evidence is ready—not when a date looks reassuring

**Explicación sugerida**

“El roadmap empieza con decisiones, después construye base operativa y supply curada, luego discovery, el loop RFQ y un piloto. Monetización es un experimento opcional. Optimización y expansión dependen de resultados. La capa transaccional es una decisión separada.”

Sobre los seis meses: “Lo tomo como horizonte de conversación, no como compromiso. Branch, mercado, equipo, acceso y capacidad operativa cambian el alcance.”

No asignes semanas ni fechas en vivo si no se acordaron inputs.

**Puente**

“El motivo para trabajar por etapas también aparece en los costos.”

[Sources]
- `docs/05-roadmap/phased-roadmap.md`
- `docs/05-roadmap/six-month-roadmap.md`
- `docs/05-roadmap/dependencies.md`

## Slide 13 — Technology is manageable; marketplace operations may cost more

**Explicación sugerida**

“La factura técnica modelada es aproximadamente USD 76, 193 y 561 por mes en Pilot, Early y Growth. Son bandas comparativas con volúmenes supuestos, no presupuestos.”

No abras la sensibilidad humana salvo que el owner pregunte o se muestre Appendix A5. Si se abre: usa aproximadamente USD 578, 2,592 y 14,298, con supuestos de USD 75/h técnica, USD 40/h operativa y cargas todavía no medidas. Infraestructura y trabajo humano forman parte del TCO, pero la sensibilidad se muestra por separado y no es un total comprometido, payroll ni forecast. Si David o el owner hacen el trabajo, el efectivo puede ser cero, pero existe costo de oportunidad.

Aclará exclusiones: pagos/reservas, migración, marketing, legal y otras capacidades no aprobadas. El standby de Growth tiene una cifra todavía no verificada y debe revalidarse.

**Puente**

“Y esta es justamente una de las razones por las que pagos no es sólo agregar un botón.”

[Sources]
- `docs/03-technology/cost-model.md`
- `docs/03-technology/cost-alternatives.md`
- `presentation/cost-explainer-v0.1.md`

## Slide 14 — Booking and payments are absolutely possible—and materially different

**Explicación sugerida**

“Branch T mantiene discovery, perfiles, requests, ofertas y conversación. Encima agrega disponibilidad estructurada, booking, cobro, payout, cancelaciones, refunds, conciliación, fraude, disputas, garantías, soporte y reviews derivadas de transacciones.”

No uses lenguaje de miedo. Usá: “Es totalmente posible; es otra primera versión.”

Preguntá: “¿Cuál de esas capacidades es imprescindible desde el primer lanzamiento y por qué?” Si el owner elige Branch T, registrá el resultado y explicá que hay que replanificar TCO, compliance y operación antes de Stage 1.

**Puente**

“Esa es la decisión más grande, pero no es la única que necesitamos cerrar.”

[Sources]
- `docs/05-roadmap/mvp-definition.md`
- `docs/05-roadmap/phased-roadmap.md`
- `docs/02-architecture/decision-branches.md`
- `plans/P03-technology-evaluation.md`

## Slide 15 — Eight decisions will turn this recommendation into a real plan

**Explicación sugerida**

No leas ocho preguntas de corrido. Agrupalas y usá “stop, adjust, expand” en inglés:

- límite: Branch L/T, disponibilidad, single-recipient;
- piloto: geografías y categorías;
- confianza/datos: legacy y disclosure de contacto;
- negocio: monetización y criterios de éxito.

Pedí también el documento prometido de funcionalidades/monetización y los materiales UI/UX existentes. No pidas que el owner decida tecnologías.

Decí: “Si una respuesta queda abierta, la dejamos abierta. No tomo silencio como aprobación.”

**Puente**

“Con esas respuestas y un proceso de acceso, el siguiente paso es concreto y reversible.”

[Sources]
- `docs/05-roadmap/owner-decision-matrix.md`
- `docs/01-product/open-questions.md`
- `docs/01-product/owner-reconciliation-matrix.md`
- `docs/00-context/source-register.md`

## Slide 16 — The next step is a bounded decision—not a leap of faith

**Explicación sugerida**

“Propongo cerrar el branch, el envelope del piloto, la política de contacto y la definición de éxito. Después acordamos acceso autorizado para auditar ownership, consentimiento, actividad, calidad, capacidades internas, analítica y opciones de migración. Con eso reconciliado, aprobamos un límite y construimos el piloto más chico que produzca evidencia útil.”

Tu valor aparece por el método, no por una slide de autopromoción. Desarrollalo sólo verbalmente: “Puedo investigar, desafiar supuestos, diseñar producto y sistema, modelar operación y costo, definir medición, usar IA para acelerar la ejecución y después implementar. Pero el humano dirige: las decisiones del negocio siguen siendo suyas.”

Cierre concreto: solicitá acordar fecha/forma de la sesión de decisiones y responsable del acceso. No cierres con “gracias” genérico.

[Sources]
- `docs/05-roadmap/migration-dependency.md`
- `docs/05-roadmap/measurement-plan.md`
- `docs/05-roadmap/phased-roadmap.md`

## Delivery guardrails

- Detenete para escuchar después de las slides 5, 14 y 15.
- No digas “43,361 proveedores”; decí “43,361 URLs públicas de listings”.
- No extrapoles la muestra de ownership al corpus.
- No digas que no existe booking interno; decí que la superficie pública no lo demuestra.
- No llames “disponible” a una fecha si sólo significa accepting requests.
- No presentes costo técnico y sensibilidad humana como un total.
- No prometas migración, seis meses, ranking final, monetización o transacciones.
- Registrá cada respuesta con fuente, relación y follow-up en la matriz de reconciliación.
