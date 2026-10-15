# ADR-008: Entornos, deploy y jobs — Vercel (prod/staging) + cron para pistas goteadas; migraciones drizzle-kit; CI GitHub Actions

- **Estado:** Aceptado
- **Fecha:** 2026-10-15
- **Contexto:** PRD RNF-08 exige al menos tres entornos (dev, staging, prod) separados, con migraciones versionadas y seeds que no toquen prod. El motor de entrega goteada de pistas (sección 23) necesita ejecutarse en horario local (America/La_Paz) sin depender de que un usuario abra la app. 1 dev ⇒ tooling mínima y automatizada.

## Decisión

- **Deploy: Vercel** para la app Next.js en **staging** (protegido por auth/preview) y **prod** (dominio propio). Cada PR genera un **preview deployment** con su propia variable de entorno (DB de staging o Neon branch). Alternativa aceptada: Node standalone en un VPS/Fly.io si se necesita control de cron y costos fijos — no es el default.
- **Base de datos por entorno:** Neon **branches** — `dev` (local/datos de prueba), `staging`, `main` (prod). Cada branch su propio `DATABASE_URL`. Seeds solo en dev/staging.
- **Migraciones:** **drizzle-kit migrate** aplicadas en CI hacia staging antes de prod; en prod, migración como paso manual/ gating con confirmación (evitar migración automática sorpresa en la temporada). Patrón: expandir→migrar datos→contraer (no romper compat hacia atrás en un deploy).
- **Jobs (entrega goteada de pistas, emails programados, recordatorios):**
  - Default en Vercel: **Vercel Cron** (o cron del proveedor) invocando una route handler protegida (`/api/cron/pistas`) con secret; corre 1×/día cerca de las 09:00 America/La_Paz y calcula qué pistas vencen.
  - El handler es **idempotente** (re-ejecutar no duplica entregas) y **transaccional** por pista (marcar entregada + encolar notificación en la misma transición de estado).
  - Si se migra a VPS: cron del sistema o worker programado; misma idempotencia.
- **CI: GitHub Actions** — lint + typecheck + tests (unit/integration; E2E Playwright opcional en CI de staging) en cada PR; deploy a staging en merge a main; deploy a prod manual/gated.
- **Feature flags simples:** tabla o variables de entorno para activar Pro/pasarela/publicidad sin redeploy (sección 47 PRD: componentes diferidos).

## Alternativas consideradas

| Opción | Por qué no (default) |
|---|---|
| Kubernetes / ECS | Ops enorme para 1 dev y MVP; overkill. |
| Supabase Edge Functions / Cron del proveedor | Viable si todo vive en Supabase; acopla jobs al proveedor. Se usa cron del proveedor de DB solo si no hay Vercel Cron. |
| Sidekiq/Temporal para jobs | Necesario a escala real; el volumen de año 1 no lo justifica — un cron + tabla de pendientes basta. |
| Migraciones 100% automáticas en prod | Riesgo alto en diciembre; gating manual reduce la probabilidad de romper la temporada. |

## Consecuencias

- **Positivas:** entornos aislados baratos; previews por PR aceleran QA; cron diario garantiza pistas sin depender de usuarios; drizzle-kit mantiene esquema y tipos sincronizados.
- **Negativas / riesgos:** Vercel Cron con free tier tiene límites de frecuencia → si se necesita horarios más finos, migrar jobs a un worker (Fly/Railway) sin tocar el resto; migraciones manuales en prod pueden atrasar hotfixes → runbook corto de rollback.
- **Restricciones derivadas:** secretos (DB, VAPID, Resend, OAuth) solo en variables de entorno por entorno, nunca en repo; el endpoint cron exige header secret y no está en la capa pública; seeds jamás se ejecutan contra `DATABASE_URL` de prod.
