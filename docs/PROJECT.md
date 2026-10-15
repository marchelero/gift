# Project Context

> Actualizado manualmente 2026-10-15 (etapa inicial pre-código de ZAIKA).

## Identity
- **Name**: ZAIKA (provisional — nombre de marca definitivo pendiente)
- **Type**: Producto SaaS / PWA — plataforma de intercambios de regalos con capa experiencial
- **Description**: Web/PWA para organizar amigo secreto (Navidad 2026) con sorteo privado, encuesta de pistas goteadas, preguntas anónimas y cierre emocional persistente. Free funcional + Pro por evento. Un solo dev, deadline dic 2026.

## Stack
- **Language**: TypeScript (estricto)
- **Framework**: Next.js 15 (App Router) — full-stack (ADR-001)
- **Runtime / Build**: Node.js + Vercel (ADR-008)
- **Package manager**: pnpm (recomendado) o npm
- **DB / ORM**: PostgreSQL (Neon) + Drizzle ORM + RLS (ADR-003)
- **Auth**: Auth.js v5 — Google OAuth + magic link; email = identidad (ADR-002)
- **Storage**: Cloudflare R2 (ADR-005)
- **Email/Push**: Resend + React Email; web-push VAPID (ADR-006)
- **PWA**: Manifest + Serwist/Workbox (ADR-007)
- **CSS**: Tailwind CSS

## Tooling (planeado)
- **Test runner**: Vitest (+ Playwright E2E opcional en staging)
- **Linter / Formatter**: ESLint + Prettier
- **CI**: GitHub Actions (lint, typecheck, tests)
- **Container**: (no aplica — Vercel/serverless)

## Conventions
- TypeScript estricto; nada de `any` en capa de datos sensibles
- RLS en toda tabla sensible; secrets solo en server
- Validación cliente + servidor siempre
- Documentos en `docs/` según convención del pack

## Entry Points
- (sin código aún — la etapa inicial generó solo documentación)

## Directory Layout
- `docs/prds/` — PRD v2 (contrato de producto)
- `docs/adr/` — decisiones de arquitectura
- `docs/plans/` — modelo de datos + plan de implementación
- `ZAIKA_PRD.md` — PRD v1 (superseded por v2)

## Non-Negotiables
- **Privacidad del sorteo**: el resultado emisor→receptor solo legible por el emisor (RLS). El organizador nunca ve el mapeo.
- **Anonimato**: el emisor de preguntas/buzón jamás se expone al receptor (UI, API, logs, notifs).
- **Identidad única por email**: un mismo email = un usuario, sea Google o magic link, en cualquier año.
- **Dos ejes desacoplados**: `tipo_evento` (mecánica) y `ocasión` (temática); nada hardcodeado a "Navidad" ni al 1:1.
- **MVP Mínimo Navidad 2026**: el funnel Free completo debe estar antes del 1 dic 2026; el resto difiere sin romper la promesa Free (PRD v2 §47).
- **License**: (pendiente — sugerido MIT para el producto; definir con el dueño)

## Architecture Notes
- **Next.js full-stack**: un solo codebase TS; Server Components/Actions + Route Handlers (ADR-001)
- **Postgres + RLS**: defensa en profundidad contra fugas de sorteo; Drizzle para tipos (ADR-003)
- **Pagos plan B**: Pro por código de un solo uso en Navidad; spike pasarela Bolivia ene 2027 (ADR-004)
- **Email canal primario en iOS**: push web requiere PWA instalada; email permanente (ADR-006)
- **Cron diario de pistas**: entrega goteada idempotente sin depender de que el usuario abra la app (ADR-008)

## Open Questions
- [ ] Nombre definitivo de marca (ZAIKA es provisional)
- [ ] Pasarela de pago con soporte Bolivia (spike ene 2027; plan B código manual para Navidad)
- [ ] Proveedor final de Postgres: Neon vs Supabase (default Neon; cerrar al provisionar)
- [ ] Licencia legal del producto / términos de uso además del aviso de privacidad
- [ ] Producción de clips de audio del narrador Santa (bloqueante solo para audio Pro)
- [ ] Dominio y marca final para OG images

## Glossary
- **Intercambio**: evento de amigo secreto (o futuro tipo de evento) con participantes, sorteo y cierre
- **Amigo Secreto**: tipo de evento MVP; asignación 1:1 en ciclo cerrado
- **Sorteo / Asignación**: resultado emisor→receptor, secreto por fila (RLS)
- **Derangement**: permutación sin puntos fijos; nadie se regala a sí mismo
- **Pista**: respuesta de la encuesta entregada de forma goteada a quien regala
- **Encuesta de pistas**: banco estático de preguntas personales que alimenta las pistas
- **Buzón (Pro) / Preguntas anónimas (Free)**: sistema unificado de mensajes anónimos; Pro lo envuelve en narrador temático
- **Álbum / Recuerdos**: fotos y mensajes del grupo, habilitados solo en revelación/cierre
- **Revelación**: fase post-"Finalizar" donde se abre el álbum y se entregan pistas pendientes
- **Tier Free/Pro**: estado de experiencia a nivel de intercambio, no de usuario
- **Slot de invitación**: participante pre-creado con link único que se "reclama" al registrarse
- **Ocasión**: eje temático (Navidad en MVP); configura narrador, textos y temas
