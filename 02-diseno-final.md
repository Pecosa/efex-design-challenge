# Diseño final — Dashboard EFEX (3 variantes)

En vez de una pantalla genérica, el dashboard se arma a partir de señales que EFEX ya tiene
(número de operaciones, agenda de pagos, si hay especialista asignado, historial de pagos por
lote). El layout base es el mismo para todos; lo que cambia es **qué aparece en la zona de
estado y qué acción se promueve**, siguiendo tres patrones tomados del benchmark
(`memory.md`): estado antes que controles (Mercury), asesor proactivo en vez de lista pasiva
(Ramp), y transparencia de términos de FX antes de cualquier compromiso (Airwallex).

## Cambios estructurales que aplican a las 3 variantes (arreglan el diagnóstico #3)

- El aviso operativo (migración a MexPago) y la promoción de referidos **bajan de la zona
  superior** a una franja secundaria, debajo de las acciones principales — dejan de competir
  por el primer vistazo.
- Cuentas con saldo 0.00 (ej. COP sin uso) se **colapsan** en un renglón compacto ("+1 cuenta
  sin actividad"), no ocupan el mismo espacio que las cuentas activas.
- El balance combinado deja de ser un número aislado: siempre lleva **un dato de contexto**
  (comparación con la semana pasada, o con el promedio del cliente) — nunca solo la cifra.

Estructura común, de arriba a abajo:
1. Encabezado (nombre del cliente + especialista asignado, si aplica — independiente de la
   variante: cualquier cliente, en cualquier momento, puede o no tener especialista)
2. **Zona de estado** (1–2 tarjetas, distinta por variante — aquí vive el "asesor proactivo")
3. Balance + cuentas por divisa (activas primero, inactivas colapsadas)
4. **Acciones**, como tarjetas grandes de ícono (patrón bancario: ícono arriba, etiqueta
   abajo, ver `05-mockups.html`) — su propia sección, debajo del balance.
5. Zona secundaria y silenciosa (avisos operativos, referidos)

*(Nota: la posición de acciones se probó 4 veces — sección propia, derecha del balance,
toolbar persistente arriba, y esta versión — ver el detalle de cada intento en
`03-decisiones-explicadas.md`. Se quedó aquí porque el usuario prefirió el tamaño táctil de
tarjeta grande sobre ser persistente.)*

*(Extensión opcional, fuera de los 3 entregables requeridos: `04-extensiones-opcionales.md`
agrega un elemento adicional, "Dinero en tránsito," entre la zona de estado y las cuentas.)*

---

## Variante A — Persona 1: Operador-dueño en riesgo de abandono

**Cuándo se activa:** cliente con 1–4 operaciones registradas (antes del umbral de retención).

```
┌─────────────────────────────────────────────┐
│ Hola, Juan · Sin especialista asignado       │
├─────────────────────────────────────────────┤
│ ESTADO                                       │
│ ┌───────────────────────────────────────┐   │
│ │ Llevas 2 operaciones con EFEX.         │   │
│ │ ¿Tienes otro pago pendiente esta       │   │
│ │ semana?  [Enviar Pago →]               │   │
│ └───────────────────────────────────────┘   │
├─────────────────────────────────────────────┤
│ CUENTAS                                      │
│ USD $12,400   MXN $216,300                   │
│ (+1 cuenta sin actividad)                    │
├─────────────────────────────────────────────┤
│ ACCIONES (tarjetas grandes de ícono)         │
│ [ Enviar Pago ]   [ Transferir ]             │
│ [ Crear link de cobro ]                      │
├─────────────────────────────────────────────┤
│ (aviso operativo, referidos — abajo, chico)  │
└─────────────────────────────────────────────┘
```

**Por qué:** este cliente no necesita más opciones, necesita una razón concreta para volver.
El mensaje traduce el hallazgo de retención del brief (el riesgo de abandono cae antes de la
5ª operación) en un empujón específico, no un banner motivacional genérico. Se **omiten**
Rate Lock y Pagos Masivos por completo — no son relevantes para alguien con una sola
operación puntual, y mostrarlos añadiría densidad que, según la reseña de Mercury, abruma a
usuarios nuevos sin experiencia financiera formal. "Crear link de cobro" (para recibir un pago,
no solo enviarlo) se conserva de la pantalla actual en las 3 variantes, siempre en último lugar
— no es parte de los 3 problemas diagnosticados, así que no se promueve ni se rediseña, solo se
mantiene disponible.

*(Nota: aquí Juan no tiene especialista asignado, pero eso es una coincidencia de este
ejemplo, no una regla — el brief trata self-serve vs. especialista-asignado como un eje
independiente del momento/persona; un cliente en la Variante A también podría tener
especialista, y uno en la Variante B podría no tenerlo.)*

---

## Variante B — Persona 2: Tesorero de manufactura con exposición cambiaria recurrente

**Cuándo se activa:** cliente establecido con un pago/cobro futuro ya agendado o un patrón
recurrente detectado (ej. pago a proveedor cada mes).

```
┌─────────────────────────────────────────────┐
│ Hola, María · Tu especialista: Carlos R.     │
├─────────────────────────────────────────────┤
│ ESTADO                                       │
│ ┌───────────────────────────────────────┐   │
│ │ USD/MXN hoy: 17.46 (el peso se         │   │
│ │ depreció 1.2% esta semana)             │   │
│ │ Tienes un pago a proveedor programado  │   │
│ │ en 45 días. ¿Aseguramos la tasa        │   │
│ │ de hoy?          [Activar Rate Lock →] │   │
│ └───────────────────────────────────────┘   │
├─────────────────────────────────────────────┤
│ CUENTAS                                      │
│ USD $84,200   MXN $1,240,000                 │
│ (+1 cuenta sin actividad)                    │
├─────────────────────────────────────────────┤
│ ACCIONES (tarjetas grandes de ícono)         │
│ [ Enviar Pago ]  [ Rate Lock ]               │
│ [ Pagos Masivos ]                            │
│ [ Crear link de cobro ]                      │
├─────────────────────────────────────────────┤
│ (aviso operativo, referidos — abajo, chico)  │
└─────────────────────────────────────────────┘
```

**Por qué:** este es exactamente a quién sirve Forward Rate Lock (76% de las empresas les
preocupa la volatilidad cambiaria, según el estudio de EFEX) — pero en vez de un botón
genérico con el mismo peso que "Transferir" (diagnóstico #2), aparece **ligado a un pago real
y una fecha real**, con la tasa de hoy visible antes de pedir cualquier compromiso (patrón
Airwallex). Pagos Masivos se mantiene visible pero en tercer lugar — util solo si cae en día
de nómina, como ya identificaba la persona. Se muestra el nombre del especialista asignado,
porque para este perfil el dashboard no reemplaza esa relación, la complementa.

**Corrección (2026-07-31):** el mecanismo de forward contract en sí *no* es territorio
inexplorado — OFX, Convera y Revolut Business ya ofrecen contratos a futuro genuinos
(12–24 meses, autoservicio online). Lo que sigue sin precedente claro es integrar ese
mecanismo institucional **dentro de un dashboard multi-divisa moderno para PyME**, disparado
por un pago real y agendado en vez de vivir como un producto de tesorería aparte — ver
la sección de benchmark en `memory.md` (2026-07-31) para el detalle. Revolut Business es la
referencia más cercana a lo que se busca aquí (forward + cuenta multi-divisa en un mismo
producto), aunque no hay evidencia pública de que lo conecte a un pago específico como
propone esta variante.

---

## Variante C — Persona 3: Operador logístico/agroindustrial de pagos masivos

**Cuándo se activa:** cliente con historial de pagos por lote (patrón recurrente semanal, ej.
pago a productores/transportistas cada viernes).

```
┌─────────────────────────────────────────────┐
│ Hola, AgroLog · Tu especialista: Ana G.      │
├─────────────────────────────────────────────┤
│ ESTADO                                       │
│ ┌───────────────────────────────────────┐   │
│ │ Hoy es viernes: ¿listo tu pago         │   │
│ │ semanal a productores?                 │   │
│ │             [Iniciar Pagos Masivos →]  │   │
│ │ La semana pasada ahorraste ~4h usando  │   │
│ │ pagos masivos en vez de uno por uno.   │   │
│ └───────────────────────────────────────┘   │
├─────────────────────────────────────────────┤
│ CUENTAS                                      │
│ MXN $342,000   USD $6,100                    │
├─────────────────────────────────────────────┤
│ Último lote: 18 pagos · 16 completados,      │
│ 2 pendientes de confirmación bancaria        │
├─────────────────────────────────────────────┤
│ ACCIONES (tarjetas grandes de ícono)         │
│ [ Pagos Masivos ]   [ Enviar Pago ]          │
│ [ Crear link de cobro ]                      │
├─────────────────────────────────────────────┤
│ (aviso operativo, referidos — abajo, chico)  │
└─────────────────────────────────────────────┘
```

**Por qué:** hoy Pagos Masivos no existe en ningún lugar de la pantalla (diagnóstico #2) —
aquí pasa a ser la acción principal, disparada por el día de la semana (señal que EFEX ya
tiene: el patrón recurrente del cliente). El "ahorraste ~4h" traduce a métrica el testimonio
real de un cliente EFEX de logística agrícola ("ahorra un día operativo completo a la
semana"), reforzando el patrón de asesor proactivo con evidencia, no solo con la función.
Debajo se agrega una vista de reconciliación del último lote — el patrón de Deel de "un
compromiso, complejidad escondida, luego un estado consolidado" — porque este perfil no
necesita ver el mecanismo del lote, necesita saber si todo se pagó bien. Rate Lock no aparece:
este cliente paga montos variables a muchos beneficiarios, no cubre una sola cantidad futura.

---

## Nota de implementación (qué activa cada variante)

No es una cuarta variante de UI, es la lógica detrás de las tres:
- **Conteo de operaciones** (< 5 → Variante A)
- **Pago/cobro futuro agendado o recurrencia detectada en USD/MXN** → Variante B
- **Historial de pagos por lote con cadencia semanal/mensual** → Variante C

Un mismo cliente puede pasar de A → B o C con el tiempo; no son categorías fijas de cliente,
son lecturas del momento en que está.
