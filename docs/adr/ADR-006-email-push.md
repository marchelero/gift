# ADR-006: Email y Push — Resend (transaccional) + Web Push estándar (web-push/VAPID); email como canal primario en iOS

- **Estado:** Aceptado
- **Fecha:** 2026-10-15
- **Contexto:** PRD sección 28/33: notificaciones por push (PWA) + email. iOS solo da push web si el usuario agrega la PWA a inicio (iOS 16.4+), por lo que el email debe tratarse como canal **primario y permanente**, no como respaldo. El email se usa también para magic links (ADR-002) y cierre personalizado (RF-16). Volumen año 1 bajo (~2.000 eventos, ~10–20 notifs/evento → decenas de miles de envíos).

## Decisión

- **Email transaccional: Resend** con **React Email** para plantillas (mismos tipos TS que el resto del stack). APIs de envío server-side; IDs de mensaje y eventos (delivered/bounced) se loguean sin contenido sensible. Alternativas aceptables: AWS SES (más barato a escala, más config) — se cambia tras el MVP si el costo lo justifica.
- **Push: Web Push API estándar** con librería `web-push` (VAPID) y suscripciones en tabla `SuscripcionPush`. Service worker recibe el push y muestra notificación; el payload es **siempre mínimo** (sin contenido sensible: "Tienes una pista nueva", nunca la pista ni la pregunta completa — sección 28 PRD).
- **Orquestador de notificaciones:** una capa interna `notify(user, tipo, payload)` decide canales según `PreferenciaNotificacion` + capacidad (¿tiene suscripción push? ¿es iOS sin PWA instalada → fuerza email). Idempotencia por (usuario, tipo, intercambio) para no duplicar en reintentos.

## Alternativas consideradas

| Opción | Por qué no |
|---|---|
| SendGrid / Mailgun | Viables; Resend tiene DX superior en Next/TS y plantillas React. Se puede migrar sin cambiar la capa `notify`. |
| Solo email (sin push) | Pierde el motor de retorno diario de las pistas goteadas — diferenciador del producto. |
| Push nativo / OneSignal | Adds third-party SDK weight y branding; el MVP es PWA, web push estándar basta. |
| SMS | Costo por mensaje alto en Latam; el PRD no lo pide. |

## Consecuencias

- **Positivas:** un solo framework de plantillas coherente con el stack; payloads de push mínimos respetan privacidad; opt-out granular ya modelado en PRD/DB.
- **Negativas / riesgos:** deliverability de email (magic link caído = usuario bloqueado) → monitoreo de bounces, retry, y fallback a "reenviar link"; suscripciones push expiran/desaparecen sin aviso → el email primario cubre eventos críticos (sorteo, cierre).
- **Restricciones derivadas:** nunca loguear direcciones de email en claro en logs de app (mascar); templates versionados; el email de cierre se genera server-side por destinatario (RN-09) con datos agregados y su propio resultado únicamente.
