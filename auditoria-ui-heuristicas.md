# Auditoría UI — Heurísticas de Nielsen (dashboard actual)

Documento de apoyo, no un cuarto entregable: el brief pide que el diagnóstico (`01-diagnostico.md`)
sea corto y priorizado, no exhaustivo. Esta auditoría es la evidencia detallada detrás de esa
priorización — un repaso sistemático de las 10 heurísticas de Nielsen sobre la captura real del
dashboard, para no dejar hallazgos solo a "lo que salta a la vista."

## 1. Visibilidad del estado del sistema
**Bien:** el timestamp ("29 de julio de 2026 a las 3:37:46 p.m. GMT-6") y la nota "esta es una
estimación... basada en los precios de tus divisas al [hora]" son honestos sobre qué tan
fresco es el dato — raro ver esa transparencia en un dashboard financiero.
**Falta:** no hay ninguna señal de dinero en tránsito (pagos enviados pero no confirmados,
cobros pendientes) — exactamente el hallazgo del propio estudio de EFEX (79% sin certeza sobre
su dinero en tránsito). El balance es un corte estático, no un estado de flujo.

## 2. Correspondencia entre el sistema y el mundo real
**Bien:** usa lenguaje bancario estándar (Compra/Venta, CLABE) — correcto para un usuario que ya
opera cuentas en divisas.
**Ambiguo:** la tarjeta "EFEX Plus USD" con "2.5% anual" y "Beneficio total: +80.28 USD" mezcla un
producto de rendimiento con una cuenta operativa de divisa, sin distinguir visualmente que es un
producto distinto (¿es la misma cuenta que uso para pagos, o una cuenta de ahorro aparte?).

## 3. Control y libertad del usuario
**Falta:** las cuentas se navegan por carrusel (`< >`) sin forma de reordenar, fijar o esconder
una cuenta inactiva (COP en 0.00 ocupa el mismo carril que las cuentas activas).
**Falta:** ni el aviso de MexPago ni la promo de referidos tienen forma de cerrarse o posponerse
— son permanentes hasta que el usuario deje de verlos por costumbre, no por elección.

## 4. Consistencia y estándares
**Inconsistente:** "Enviar Pago" aparece dos veces en la misma pantalla — como botón primario
arriba a la derecha, y otra vez como tile inferior junto a "Crear link de cobro" — mismo destino,
dos contextos visuales distintos, sin razón aparente para el usuario.
**Inconsistente:** "Programar Rate Lock" tiene el mismo peso visual y misma lista que "Transferir
entre mis cuentas" — pero son acciones de naturaleza distinta (un compromiso a futuro vs. un
movimiento inmediato entre cuentas propias). Ya señalado en el diagnóstico #2, esta es la
evidencia heurística específica detrás de ese punto.

## 5. Prevención de errores
**Bien, parcialmente:** el aviso de MexPago previene un error real y costoso (transferencias a
la cuenta STP anterior se devuelven en 2 días hábiles) — buen ejemplo de prevención proactiva.
**Limitado:** es texto estático, no hay una verificación activa (ej. un check al momento de pagar
que confirme que el beneficiario ya tiene la cuenta CLABE actualizada).

## 6. Reconocimiento antes que memoria
**Falta:** el balance combinado no muestra qué proporción viene de cada divisa — el usuario tiene
que recorrer el carrusel y sumar mentalmente para entender la composición de los 6,501,048.67 MXN
mostrados.

## 7. Flexibilidad y eficiencia de uso
**Falta:** no hay atajos, beneficiarios frecuentes ni variación por nivel de uso — el cliente en su
primera operación y el que ya lleva 50 ven exactamente el mismo flujo para todo. Esta es la
evidencia heurística más directa detrás del diagnóstico #1 (el dashboard no distingue quién eres).

## 8. Diseño estético y minimalista
**Problema de jerarquía:** el aviso operativo (MexPago) y la caja de referidos —ambos de baja
urgencia real para la mayoría de los clientes en un día cualquiera— ocupan el tercio superior de
la pantalla, empujando el balance y las cuentas hacia abajo. Evidencia directa detrás del
diagnóstico #3.
**Ruido:** la tarjeta COP con "0.00 COP" despliega compra/venta completos para una cuenta sin
uso — mismo nivel de detalle que las cuentas activas.

## 9. Ayudar a reconocer y recuperarse de errores
No evaluable directamente — la captura no muestra ningún estado de error o validación fallida.
Queda como pendiente de revisión si se audita el flujo de pago completo, fuera del alcance de
esta auditoría de la pantalla principal.

## 10. Ayuda y documentación
**Bien, puntual:** el link "Términos y condiciones" bajo EFEX Plus da acceso contextual a la
letra chica de ese producto específico.
**Falta:** ninguna señal del especialista asignado (mencionado en el brief como diferenciador de
EFEX) en esta pantalla — si un cliente tiene un especialista dedicado, el dashboard no lo refleja
en absoluto; el cliente no tiene una vía visible de ayuda humana desde su vista principal.

---

## Sintesis — qué agrega esta auditoría a lo ya escrito

Confirma con evidencia heurística específica los 3 problemas priorizados en `01-diagnostico.md`
(#7 y #1 → diagnóstico #1; #4 y #8 → diagnóstico #2 y #3) y agrega dos hallazgos nuevos, menores,
no incluidos en la priorización original por ser de menor impacto relativo:
- **Heurística 1**: ausencia total de estado de "dinero en tránsito" — vale la pena anotarlo como
  posible cuarto punto si hubiera más tiempo, ya que ataca directamente el hallazgo insignia del
  propio estudio de EFEX (79% sin certeza).
- **Heurística 10**: el dashboard no refleja la relación con el especialista asignado en absoluto
  — ya corregido en la Variante B del diseño final (`02-diseno-final.md`), que muestra el nombre
  del especialista cuando aplica.

No se encontraron heurísticas violadas de forma grave que cambien la priorización ya hecha — el
propósito de este documento es dar rigor a las decisiones tomadas, no reabrirlas.
