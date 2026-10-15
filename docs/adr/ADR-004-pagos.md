# ADR-004: Pagos — Plan B código manual para Navidad 2026; spike de pasarela Bolivia post-corte

- **Estado:** Aceptado (plan B) · Pasarela real: Aceptado como pendiente controlado
- **Fecha:** 2026-10-15
- **Contexto:** PRD secciones 9/30/51: Stripe no opera nativamente en Bolivia. El Pro es US$ 4,99 pago único por intercambio. Con 1 dev y deadline de diciembre 2026, bloquear el MVP en la elección de pasarela es inaceptable — pero la monetización de Navidad no debe perderse.

## Decisión

1. **MVP Mínimo Navidad 2026: activación de Pro por código de un solo uso (RF-29).** El operador (propietario del proyecto) cobra externamente (link de pago manual, transferencia, o lo que esté disponible) y entrega un código. El organizador canjea el código en la app → `tier='pro'` en el intercambio. El canje queda auditado (quién, cuándo, código, intercambio). El modelo `Pago` y `CodigoActivacion` del modelo de datos soportan ambos caminos; `pasarela` es nullable.
2. **Módulo de pagos con pasarela real = spike post-lanzamiento (enero 2027).** Spike con comparativa de: **MercadoPago** (cobertura Latam/Bolivia, checkout Hosted, webhooks), **Culqi** (Perú/LatAm, API moderna), **Payku**, y opciones locales bolivianas. Criterios: soporte de Bolivia (KYC del operador, moneda, medios de pago locales como Tigo Money/Pago Móvil si aplica), fee por transacción, time-to-integration, webhooks confiables, y ability de emitir US$ 4,99 en BS al tipo del día.
3. La UI de upgrade Pro admite ambos flujos (pagar / canjear código) detección por feature flag; el tier resultante y sus efectos son idénticos (sección 30 PRD).

## Alternativas consideradas

| Opción | Por qué no (para Navidad 2026) |
|---|---|
| Stripe (via entity extranjera) | Riesgo legal/fiscal para el operador boliviano; no soporta Bolivia nativamente. Fuera del MVP. |
| Esperar a elegir pasarela | Pierde la temporada de diciembre — peor resultado posible. |
| Pro gratis para todos en Navidad | Deja de validar la propuesta de valor Pro justo en el pico de demanda; no recomendado salvo fuerza mayor. |

## Consecuencias

- **Positivas:** todo lo Pro (buzón temático, temas, álbum premium, sin publicidad) se desarrolla y prueba en Navidad sin bloquearse; el spike de pasarela se hace con producto real y users, no en el vacío.
- **Negativas / riesgos:** el cobro manual es operación humana (no escalable); fricción extra para el organizador; se debe comunicar claramente "cómo activar Pro" (landing + FAQ). Riesgo de códigos reusados/filtered → tokens aleatorios fuertes, un solo uso, revocables.
- **Restricciones derivadas:** rate-limit y lockout en intentos de canje; log de auditoría de canjes (RNF-07); nunca exponer la lista de códigos válidos en la app; el canje no revela el precio a participantes.
