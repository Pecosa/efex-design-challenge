# Extensiones opcionales — más allá de los 3 entregables

Este documento no es parte de los 3 entregables requeridos por el brief (ya completos en
`01-diagnostico.md`, `02-diseno-final.md`, `03-decisiones-explicadas.md`) — son las dos
extensiones que `03-decisiones-explicadas.md` había marcado como pendientes si se retomaba el
ejercicio con más tiempo.

---

## Extensión 1 — Flujo de Forward Rate Lock (más allá del punto de entrada)

En `02-diseno-final.md`, la Variante B solo diseñó el disparador en el dashboard: una tarjeta
que dice "Tienes un pago a proveedor programado en 45 días. ¿Aseguramos la tasa de hoy?" con un
botón "Activar Rate Lock →". Este es el flujo completo detrás de ese botón, usando el mismo
ejemplo (María, tesorera de manufactura, pago a proveedor de $18,500 USD en 45 días). El
nombre "Rate Lock" se mantiene en inglés en toda la interfaz, igual que en el brief y en la
pantalla actual ("Programar Rate Lock") — no se traduce a "Bloquear Tasa" para no crear un
nombre de producto distinto al que EFEX ya usa en marketing/ayuda.

```
PASO 1 — Confirmar el pago a cubrir
┌─────────────────────────────────────────────┐
│ Rate Lock para un pago futuro                │
├─────────────────────────────────────────────┤
│ Pago a: Proveedor Industrial SA (ya          │
│ registrado como beneficiario)                │
│ Monto: USD $18,500        [editar]           │
│ Fecha del pago: 12 sep 2026 (45 días)  [editar]│
│                                               │
│              [ Continuar → ]                 │
└─────────────────────────────────────────────┘
```
**Por qué así:** el formulario llega precargado con un pago que la plataforma ya conoce —no es
una captura en blanco— porque esa es la diferenciación real que sostuvo la investigación de
competidores (ningún competidor ata el rate lock a un pago ya agendado). El usuario puede
ajustar monto/fecha si el pago real cambió, pero el punto de partida no es cero.

```
PASO 2 — Ver los términos antes de comprometerse
┌─────────────────────────────────────────────┐
│ Términos de tu Rate Lock                     │
├─────────────────────────────────────────────┤
│ Tasa de hoy (29 jul 2026):     17.46 MXN/USD │
│ Tasa que se bloquea:           17.42 MXN/USD │
│ Vigente hasta:                  12 sep 2026  │
│                                               │
│ Costo de asegurar: depósito de 5%             │
│ ($925 USD), se descuenta del pago final       │
│                                               │
│ Si el pago cambia de fecha o se cancela,      │
│ el depósito se conserva 30 días para          │
│ reprogramar o se devuelve menos una           │
│ comisión de $50 USD.                          │
│                                               │
│         [ Atrás ]      [ Confirmar → ]        │
└─────────────────────────────────────────────┘
```
**Por qué así:** muestra tasa de hoy, tasa bloqueada y vigencia lado a lado, antes de pedir
cualquier compromiso — el patrón de transparencia de Airwallex, aplicado al hallazgo del 79%
de EFEX sobre incertidumbre de tasa. El depósito del 5% sigue el modelo real de OFX (5–10%)
encontrado en la investigación de competidores — no se inventó un mecanismo sin precedente,
solo se contextualizó. La política de cancelación se explica en una línea, no se esconde para
después de confirmar.

```
PASO 3 — Confirmación
┌─────────────────────────────────────────────┐
│ ✓ Rate Lock activado: 17.42 MXN/USD          │
│   para tu pago de $18,500 USD el 12 sep 2026 │
│   Depósito de $925 USD descontado de tu       │
│   cuenta MXN.                                 │
│                                               │
│           [ Ver mis Rate Lock ]              │
└─────────────────────────────────────────────┘
```

```
PASO 4 — Dónde vive después (nuevo estado en el dashboard, Variante B)
┌─────────────────────────────────────────────┐
│ TUS RATE LOCK                                │
│ USD→MXN · 17.42 · $18,500 USD                 │
│ Se paga en 32 días (12 sep 2026)              │
└─────────────────────────────────────────────┘
```
**Por qué así:** el compromiso no desaparece después de confirmarlo — queda visible como un
elemento propio del dashboard (no enterrado en un historial), y cuando llega la fecha, el flujo
normal de "Enviar Pago" para ese beneficiario ya trae la tasa bloqueada aplicada en vez de la
tasa de mercado de ese día. Esto cierra el ciclo: el rate lock no es un producto de tesorería
aparte, es una extensión del flujo de pago que el cliente ya usa.

**Qué se dejó fuera incluso de este flujo extendido:** qué pasa si el cliente quiere bloquear
una tasa para un pago que *no* está agendado todavía (compra especulativa de cobertura) —
diseñado aquí solo para el caso contextual, que es el diferenciador real; el caso genérico
"quiero cubrir un monto sin fecha de pago fija" quedaría como una segunda iteración.

---

## Extensión 2 — Señal de "dinero en tránsito"

Hallazgo de la auditoría de heurísticas (heurística 1): el dashboard actual no muestra ningún
estado de dinero en camino — ni pagos enviados pendientes de confirmación, ni cobros
esperados. Esto ataca directamente el hallazgo insignia del propio estudio de mercado de EFEX
(79% de empresas sin certeza sobre su dinero en tránsito, tasa aplicada, o monto final).

**Dónde vive:** se agrega como un elemento común a las 3 variantes (no es específico de una
persona — el diagnóstico ya señalaba que este problema afecta a todos por igual), entre la
zona de estado y las cuentas por divisa:

```
┌─────────────────────────────────────────────┐
│ ESTADO (tarjeta específica de la variante)   │
├─────────────────────────────────────────────┤
│ DINERO EN TRÁNSITO                            │
│ • $8,200 USD enviados a Proveedor SA —       │
│   llega en 1 día hábil                        │
│ • $45,000 MXN por cobrar de Cliente Bogotá —  │
│   pendiente de confirmación bancaria,         │
│   esperado en 2 días                          │
├─────────────────────────────────────────────┤
│ CUENTAS                                       │
│ ...                                           │
```

**Por qué así:** convierte la nota de "esta es una estimación de tu saldo" (que ya existe en
el dashboard actual, honesta pero pasiva) en información activa y accionable — el usuario ve no
solo que el balance es una estimación, sino exactamente qué la mueve y cuándo se resuelve.

**Relación con lo que ya existía:** la Variante C (Persona 3) ya tenía un componente parecido
("Último lote: 18 pagos · 16 completados, 2 pendientes de confirmación bancaria") pero era
específico de pagos masivos. Esta extensión generaliza esa idea a cualquier operación en
tránsito, para que la Persona 1 y la Persona 2 —que hoy no tienen ninguna visibilidad de este
tipo— también se beneficien, no solo el cliente de pagos por lote.

**Qué se dejó fuera:** de dónde exactamente saca la plataforma el estado "pendiente de
confirmación bancaria" en tiempo real (integración con el banco corresponsal/riel de pago) es
una pregunta de ingeniería, no de diseño — se asume que ese dato ya existe internamente en
EFEX (deben saberlo para poder cumplir sus propios tiempos de entrega prometidos), pero no se
verificó.
