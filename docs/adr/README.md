# ADRs — ZAIKA

Decisiones de arquitectura registradas (ADR = Architecture Decision Record). Cada ADR es un contrato de implementación; cambiarlo requiere un nuevo ADR que lo reemplace.

| # | ADR | Estado | Resumen |
|---|---|---|---|
| 001 | [Next.js full-stack](ADR-001-nextjs-fullstack.md) | Aceptado | Next.js 15 App Router + TS + Tailwind + Drizzle; un solo codebase |
| 002 | [Autenticación](ADR-002-auth-identity.md) | Aceptado | Auth.js v5: Google + magic link; email = identidad única; interstitial WebView |
| 003 | [Base de datos + RLS](ADR-003-database-rls.md) | Aceptado | Postgres (Neon default / Supabase alt) con RLS; Drizzle ORM |
| 004 | [Pagos](ADR-004-pagos.md) | Aceptado (plan B) | Código manual Pro para Navidad 2026; spike pasarela Bolivia ene 2027 |
| 005 | [Storage](ADR-005-storage.md) | Aceptado | Cloudflare R2 (o Supabase Storage); miniaturas server-side; EXIF strip |
| 006 | [Email y Push](ADR-006-email-push.md) | Aceptado | Resend + React Email; web-push VAPID; email primario en iOS |
| 007 | [PWA](ADR-007-pwa.md) | Aceptado | Manifest + Serwist/Workbox; push handler; offline mínimo |
| 008 | [Entornos y jobs](ADR-008-entornos-deploy-jobs.md) | Aceptado | Vercel + Neon branches; cron diario pistas; drizzle-kit; GH Actions |

## Contexto común a todos los ADRs

- **PRD contrato:** `docs/prds/2026-10-15_0201-zaika.prd.md` (v2).
- **Restricción dura:** 1 desarrollador, ventana hasta Navidad 2026 (~6 semanas desde 2026-10-15). El corte "MVP Mínimo Navidad" del PRD sección 47 gobierna prioridades.
- **Privacidad:** el resultado del sorteo es el dato más sensible del sistema; toda decisión (DB, deploy, cache, logs) se juzga primero por no filtrarlo (RN-06/RN-14).
