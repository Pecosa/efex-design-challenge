# Decisiones explicadas — Dashboard EFEX

Este documento se puede leer de forma independiente, sin haber visto una presentación en vivo.
Cada sección retoma brevemente el problema antes de explicar la decisión, para que no sea
necesario volver a `01-diagnostico.md` para entenderla.

## Por qué creemos que el dashboard actual es así (lectura arqueológica)

Antes de entrar a las decisiones, vale la pena leer la captura actual no como "mal diseño" sino
como el registro fósil de cómo se construyó el producto — entender esto importa porque cambia
qué tipo de arreglo hace falta (¿de diseño, o de quién es dueño de esta pantalla?).

- **La pantalla única para todos** encaja con un equipo de ingeniería seed-stage (EFEX levantó
  ~$8M) que puso su presupuesto en la parte difícil y regulada — cuentas multi-divisa,
  transferencias transfronterizas, cumplimiento CNBV — y trató la pantalla de inicio como una
  capa delgada. Personalizar requiere infraestructura de datos de uso y un dueño explícito de
  "la pantalla de inicio" como producto; eso normalmente llega después de encontrar
  product-market fit, no en la etapa semilla.
- **La promoción de referidos flotante** se lee como el pedido de un equipo de growth,
  insertado sin coordinarse con quien sea que controla el layout general — barato de
  implementar, típico de la etapa seed enfocada en bajar CAC, y sin ninguna señal de que
  alguien haya considerado con qué más compite ese espacio.
- **El aviso de MexPago** es evidencia de que EFEX todavía no tiene un sistema de
  notificaciones dentro de la app: un equipo de operaciones/cumplimiento con una fecha límite
  real (transferencias mal dirigidas se devuelven) solo tenía una palanca disponible — gritar
  desde arriba de la única pantalla que todos ven. Eso es lo que haces cuando no existe un
  inbox ni un sistema de notificaciones más dirigido.
- **Rate Lock como botón con el mismo peso que "Transferir"** es la firma de un equipo de
  producto lanzando contra una fecha, que conectó el punto de entrada a la lista de acciones ya
  existente en vez de rediseñar la jerarquía alrededor de la función nueva.
- **Pagos Masivos completamente ausente** es el hallazgo más revelador: la función backend
  claramente existe (el propio brief dice que "se acaba de lanzar"), pero nunca llegó a la
  pantalla de inicio. Es evidencia fuerte de una separación organizacional — un equipo de
  producto lanza la funcionalidad, pero nadie es dueño de actualizar la pantalla principal para
  reflejarla. El dueño de la pantalla de inicio va por detrás de los equipos de feature.
- **La tarjeta de COP en 0.00 con compra/venta completos** es un componente genérico
  reutilizado sin una rama para el caso vacío — evidencia de que "soportar más divisas" se
  priorizó sobre "pulir la lista de cuentas."

**Conclusión de esta lectura:** no es falta de buen gusto de diseño — es el registro natural de
un startup que priorizó correctamente el núcleo regulado sobre la puerta de entrada, y donde
varios equipos (operaciones, growth, features) escriben sobre la misma pantalla sin que nadie
concilie las prioridades entre ellos. Esto importa para las decisiones que siguen: arreglar
solo la superficie visual no alcanza si nadie queda como dueño explícito de esta pantalla
después del rediseño — por eso la Decisión 1 (dashboard adaptativo por señal) también implica,
implícitamente, que alguien en EFEX tiene que ser dueño de esa lógica de personalización hacia
adelante, no solo de la versión inicial.

## Decisión 1 — El dashboard cambia según quién eres y en qué momento estás, en vez de ser una sola pantalla

**Qué cambió:** en vez de un layout fijo, el dashboard se arma a partir de tres señales que
EFEX ya tiene: número de operaciones del cliente, si hay un pago/cobro futuro agendado, y si
existe un patrón recurrente de pagos por lote. Esto produce 3 estados distintos (Variantes
A/B/C en `02-diseno-final.md`), no 3 productos distintos — mismo layout base, distinta zona de
estado y distinto peso de acciones.

**Por qué:** era el problema #1 del diagnóstico — la queja explícita del brief de que la
pantalla es idéntica para el exportador en su semana más intensa y para el cliente que no ha
operado en un mes. Es la causa raíz: mientras el dashboard no distinga clientes, tampoco puede
resolver de forma inteligente los otros dos problemas.

**Qué esperaría mejorar en producción:** que más clientes en la Variante A (pre-5-operaciones)
lleguen a su 3ª/4ª/5ª operación — es medible directamente contra el umbral de retención que da
el propio brief. También esperaría mayor tasa de clic en Rate Lock y Pagos Masivos al aparecer
contextualizados (Variantes B y C) frente a la tasa de clic actual de esos botones genéricos.

**Qué NO se hizo (a propósito):** no se diseñó un motor de personalización continuo ni
basado en modelos — son 3 disparadores discretos y explicables, no un sistema de ML. Es una
decisión de alcance: con más tiempo y acceso a datos reales de uso, valdría la pena validar si
estos 3 estados cubren la mayoría de los casos reales o si se necesitan más.

## Decisión 2 — Forward Rate Lock se ata a un pago real y agendado; Pagos Masivos se vuelve la acción principal en su día

**Qué cambió:** Rate Lock ya no es un botón con el mismo peso que "Transferir entre mis
cuentas" — en la Variante B aparece disparado por un pago futuro específico ya conocido por la
plataforma, mostrando la tasa de hoy antes de pedir cualquier compromiso. Pagos Masivos, que
hoy no existe en ningún lugar de la pantalla, pasa a ser la acción destacada en la Variante C,
disparado por el día de la semana en que el cliente históricamente paga por lote.

*(Nota de naming: el botón se llama "Rate Lock," en inglés, no "Bloquear Tasa" — el propio
brief y la pantalla actual usan "Rate Lock" como nombre de producto en inglés dentro de una
interfaz en español ("Programar Rate Lock"). Una versión anterior de este rediseño lo tradujo
por completo, lo cual habría creado una inconsistencia de nombre entre el producto y cualquier
material de marketing/ayuda que use "Rate Lock" — se corrigió para mantener el mismo nombre en
todos lados.)*

*(Nota de jerarquía visual, solo visible en `05-mockups.html` — un wireframe de texto no lo
captura: la primera versión del mockup usaba el color de acento (amarillo) dos veces para la
misma acción — una vez como CTA de la tarjeta de estado, otra vez repetida en la fila de
acciones — lo que diluía la señal de "esto es lo importante." Se corrigió dejando el acento en
un solo lugar (la tarjeta de estado) y bajando su duplicado en acciones a estilo secundario.
También se le dio a la tarjeta de estado un fondo con tinte y un borde de acento a la izquierda,
porque su posición arriba de la pantalla no bastaba para ganarle la atención a la cifra de
balance de 30px que aparece justo debajo — jerarquía por posición sola no es suficiente cuando
compite con un número grande.)*

*(Nota de layout — 4 versiones probadas de dónde/cómo viven las acciones: (1) sección propia
debajo de cuentas — cambio de layout no pedido por el diagnóstico; (2) a la derecha del balance
— más fiel al original, pero competía por espacio en pantallas angostas; (3) toolbar
persistente arriba (patrón bancario Revolut/PayPal) — siempre visible, pero muy compacto para
íconos grandes; (4) **versión final**: tarjetas grandes de ícono (ícono + etiqueta de 2 líneas)
en su propia sección debajo del balance — el usuario prefirió el tamaño táctil sobre ser
persistente. Ver `02-diseno-final.md` y `05-mockups.html`.)*

*(Nota de alcance: "Crear link de cobro" — la acción de recibir un pago, presente en la
pantalla actual como tile inferior — se había caído silenciosamente de las 3 variantes en un
borrador anterior, sin decisión ni nota. Se restauró en las 3 variantes, siempre en la posición
de menor peso, porque no es parte de los 3 problemas diagnosticados y no había ninguna razón
para quitarla.)*

**Por qué:** era el problema #2 del diagnóstico — las dos funciones que el negocio más quiere
mostrar eran las más invisibles.

**Corrección hecha durante el proceso (vale la pena dejarla explícita, es evidencia de
rigor):** la primera versión de este documento argumentaba que "ningún competidor ha resuelto
el rate lock a futuro," basado en una revisión inicial de Wise. Una segunda pasada de
investigación (a petición del usuario) encontró que esto era falso — OFX y Convera ya operan
contratos forward maduros (12–24 meses, autoservicio), y Revolut Business integra forwards a
12 meses directo en su dashboard multi-divisa cotidiano. La diferenciación real, que sí se
sostuvo tras la corrección, es más angosta: **ningún competidor investigado ata la oferta de
rate lock a un pago específico ya agendado en la plataforma** — todos lo tratan como un
producto de tesorería aparte. Esa es la apuesta de diseño de la Variante B, no "ser el primero
en tener forwards."

Una tercera pasada (investigación de bancos, también a petición del usuario) afinó todavía más
el argumento de negocio: la comparación honesta contra bancos no es "EFEX tiene pagos masivos y
forwards, los bancos no" — Banorte ya ofrece pagos masivos en autoservicio. La diferenciación
real vs. bancos es que **el hedging de FX en EFEX es autoservicio y contextual, mientras que en
los bancos (Santander lo dice explícitamente) sigue requiriendo una llamada a un asesor
especializado.** Esta es la afirmación que valdría la pena usar en comunicación de producto,
en vez de la cifra genérica de "73% ve a los bancos como ineficientes."

**Qué esperaría mejorar en producción:** mayor tasa de adopción de ambas funciones, medible
directamente porque hoy su adopción probablemente es cercana a cero por pura invisibilidad.

**Qué NO se hizo:** no se diseñó el flujo completo posterior al clic (la pantalla de
Rate Lock o el flujo paso a paso de Pagos Masivos) — solo el punto de entrada
contextualizado en el dashboard. Con más tiempo, el siguiente paso lógico sería bocetar ese
flujo, empezando por Rate Lock, ya que es el que menos patrón tiene para copiar.

## Decisión 3 — El espacio superior deja de competir con el contenido que sí importa

**Qué cambió:** el aviso operativo (migración a MexPago) y la promoción de referidos bajan a
una franja secundaria, debajo de las acciones principales. Las cuentas con saldo 0.00 (ej. COP
sin uso) se colapsan en un renglón compacto en vez de ocupar el mismo espacio que las cuentas
activas. El balance combinado siempre lleva un dato de contexto (comparación con la semana
pasada), nunca es solo una cifra aislada.

**Por qué:** era el problema #3 del diagnóstico, confirmado con evidencia heurística específica
en `auditoria-ui-heuristicas.md` (heurísticas 4 y 8 — inconsistencia de jerarquía y ruido
visual).

**Qué esperaría mejorar en producción:** más clics en las acciones primarias relativas a los
elementos secundarios — hoy compiten por la misma atención inicial.

**Qué NO se hizo:** no se eliminó la promoción de referidos por completo, solo se demovió — es
un juicio deliberado, no un descuido: el negocio probablemente sigue queriendo ese canal de
crecimiento, y quitarlo sin discutirlo con el equipo de growth sería una decisión que no me
corresponde tomar unilateralmente en este ejercicio.

## Decisión 4 — El especialista asignado se vuelve visible en el dashboard

**Qué cambió:** la Variante B muestra el nombre del especialista EFEX asignado al cliente,
cuando existe esa relación.

**Por qué:** no venía del diagnóstico original sino de la auditoría de heurísticas
(heurística 10) — el brief menciona la relación con un especialista como uno de los dos
diferenciadores centrales de EFEX, pero la pantalla actual no la refleja en absoluto.

**Qué esperaría mejorar en producción:** menos confusión de clientes que sí tienen un
especialista pero se sienten forzados a operar 100% solos por cómo se ve la pantalla.

**Qué NO se hizo:** no se diseñó cómo cambia la experiencia completa para un cliente
100% self-serve vs. uno con especialista más allá de esta mención — sería el siguiente eje de
personalización a explorar si se retoma este trabajo.

## Lo que se dejó fuera a propósito (resumen)

- **Ningún flujo posterior al punto de entrada** para Rate Lock o Pagos Masivos — solo se
  diseñó el disparo contextual en el dashboard principal, no la pantalla de captura de fecha/
  monto ni la de confirmación. *(Actualización: el flujo de Rate Lock sí se bocetó después,
  como extensión opcional — ver `04-extensiones-opcionales.md`. El de Pagos Masivos sigue sin
  diseñarse.)*
- **Ninguna interfaz visual de alta fidelidad** (colores, tipografía, espaciado exacto) — todo
  quedó en wireframe de texto, por elección explícita del usuario sobre el formato del
  entregable. *(Actualización: el usuario pidió después ver las 3 variantes como mockups
  visuales — ver `05-mockups.html`, un HTML interactivo con switcher A/B/C que recrea la
  identidad visual real de EFEX tomada de la captura original.)*
- **Ninguna señal de "dinero en tránsito"** en el dashboard, pese a ser el hallazgo insignia
  del propio estudio de EFEX (79% sin certeza) y estar documentado en la auditoría de
  heurísticas — era el candidato más fuerte a un 4° elemento de diseño. *(Actualización: sí se
  diseñó después, como extensión opcional — ver `04-extensiones-opcionales.md`.)*
- **Ninguna validación con datos reales de uso de EFEX** — las 3 personas y sus disparadores
  (umbral de operaciones, cadencia de lotes) están construidos a partir de pistas del brief y
  de investigación de mercado externa, no de datos reales de clientes EFEX, a los que no tuve
  acceso. Antes de construir esto en producción, los umbrales exactos (¿es "5 operaciones" el
  número correcto? ¿qué tan seguido cae realmente un pago por lote?) necesitan validarse contra
  datos reales, no solo contra la pista del brief.
- **Ninguna revisión de accesibilidad** (contraste, tamaños, lectores de pantalla) — fuera de
  alcance dado el tiempo disponible.
- **Las tarjetas de cuenta se simplificaron a divisa + monto**, dejando fuera tres cosas que sí
  tiene la pantalla actual: la cuenta EFEX Plus USD (producto con rendimiento del 2.5% anual,
  distinto de una cuenta operativa normal, sin ninguna distinción visual hoy), las tasas de
  compra/venta que hoy se muestran directamente en cada tarjeta de MXN/COP/EUR, y el enlace
  "Ver detalle" por cuenta. Ninguno de los 3 problemas diagnosticados trata sobre estos
  elementos, así que quedaron fuera de alcance a propósito — pero una versión de producción
  necesita decidir qué hacer con ellos, no solo omitirlos.

## Sobre el uso de IA (requerido por las reglas del brief)

Todo este ejercicio se hizo con asistencia de IA (Claude, vía Claude Code) de forma continua,
no como consulta puntual: investigación de la empresa, el mercado y competidores (vía
WebSearch/WebFetch), verificación cruzada de esa investigación contra fuentes primarias
(incluyendo dos correcciones reales de afirmaciones iniciales que resultaron ser
incorrectas o insuficientemente sustentadas — documentadas arriba y en `memory.md`), redacción
de los tres documentos del entregable, y la auditoría de heurísticas. Las decisiones de diseño
y priorización se tomaron de forma colaborativa: el usuario dirigió el alcance y formato en
cada paso (elección de wireframe sobre mockup visual, qué investigaciones de seguimiento
perseguir). Ningún dato real de clientes de EFEX estuvo disponible ni se usó — las personas son
construidas, no observadas.

## Nota sobre el límite de tiempo

El brief pide un máximo de 3–4 horas de trabajo y ser transparente sobre dónde se paró. Este
ejercicio se hizo en dos sesiones (29 y 31 de julio de 2026) con asistencia de IA extensiva,
incluyendo tres rondas de investigación de competidores y una auditoría de heurísticas
completa — más profundidad de la que una persona sola normalmente cubriría en un bloque de 4
horas de trabajo manual. Si este mismo ejercicio se hiciera bajo el límite estricto de 4 horas
sin asistencia de IA de este nivel, el alcance realista habría sido: el diagnóstico, **una**
variante de dashboard (no tres) y una versión más breve de este documento de decisiones — el
diseño de 3 variantes y las 3 rondas de benchmarking competitivo exceden lo que 4 horas
enfocadas de trabajo humano solo típicamente producen. Se deja esto por escrito para no dar la
impresión de que el resultado completo cabe dentro del límite de tiempo tal como está
planteado en el brief.
