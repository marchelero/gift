# ADR-002: Autenticación — Auth.js (NextAuth v5): Google OAuth + Magic Link, email = identidad única

- **Estado:** Aceptado
- **Fecha:** 2026-10-15
- **Contexto:** PRD sección 33 exige un esquema único para organizadores y participantes, sin contraseñas, con Google como método principal y magic link como fallback. El email es la clave única de identidad. WhatsApp abre links en WebView donde Google OAuth falla (`disallowed_useragent`); se requiere interstitial previo. Fricción máxima del participante casual: < 90 s.

## Decisión

**Auth.js v5 (NextAuth)** integrado en Next.js con dos providers:

1. **Google OAuth** — provider principal.
2. **Magic link por email** — provider tipo "Email" con token de un solo uso (expiración 15 min), enviado vía servicio de email transaccional (ADR-006). Sin tabla de contraseñas.

**Identidad única por email:** en el callback de signin se normaliza el email (lowercase, trim) y se upsert en `Usuario`. Un solo registro de usuario por email sea cual sea el método o el año. Sesión JWT (o database session — decisión de implementación menor) con email como claim estable.

**Detección de WebView:** middleware de Next.js inspecciona `User-Agent` en rutas de invitación/landing; si detecta WebView conocido (WhatsApp, Instagram, Messenger, Facebook), redirige a un interstitial que fuerza `intent://` / URL scheme del navegador del sistema **antes** de renderizar cualquier botón de login. Fallback: si la detección falla, el magic link sigue siendo viable (se abre desde la app de correo).

## Alternativas consideradas

| Opción | Por qué no |
|---|---|
| Clerk / Auth0 / Supabase Auth | Excelentes pero añaden costo por MAU o acople a su BD de usuarios; para un MVP con identidad-email-simple y budget ajustado, Auth.js + Postgres propio es suficiente y portable. |
| Passport.js custom | Más control, mucho más código de sesión/CSRF/refresh que Auth.js ya resuelve. |
| Contraseñas + email verification | Explícitamente rechazado por el PRD (fricción, soporte de "olvidé contraseña"). |

## Consecuencias

- **Positivas:** cero fricción de contraseña; identidad estable por email (RN de reutilización entre años); Auth.js es maduro en App Router.
- **Negativas / riesgos:** magic link requiere email transaccional confiable (entrega = login); rate-limit por email obligatorio (5/hora, sección 40 PRD); dependencia de configuración OAuth de Google Cloud (redirect URIs por entorno — ADR-008).
- **Restricciones derivadas:** el magic link nunca se registra en logs en texto plano; tokens con hash en DB; la sesión no distingue rol organizador/participante (el rol es por intercambio, no global).
