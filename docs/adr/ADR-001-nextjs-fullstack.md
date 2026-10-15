# ADR-001: Framework — Next.js (App Router) + TypeScript + Tailwind CSS

- **Estado:** Aceptado
- **Fecha:** 2026-10-15
- **Contexto:** PRD v2 requiere PWA mobile-first, API server-side (sorteo, RLS, emails), OAuth + magic link, previews OG para WhatsApp, y un solo desarrollador con ~6 semanas hasta Navidad 2026. El stack debe minimizar piezas móviles y maximizar velocidad de entrega sin sacrificar la privacidad del sorteo.

## Decisión

Usar **Next.js 15 (App Router) + TypeScript estricto + Tailwind CSS** como base única de frontend y backend (Server Components + Route Handlers / Server Actions). PWA con manifest + service worker (ver ADR-007). ORM: **Drizzle** con PostgreSQL (ver ADR-003).

## Alternativas consideradas

| Opción | Por qué no |
|---|---|
| SPA (Vite/React) + backend NestJS/FastAPI | Dos codebases, dos deployments, más auth glue; invierte tiempo escaso en orquestación en vez de producto. |
| SvelteKit / Remix | Viables técnicamente; menor ecosistema de ejemplos PWA/Auth de referencia y el desarrollador ya es más productivo en el ecosistema React/Next. |
| Django/Rails + templates | PWA y DX de componentes modernos cuestan más; despliegue y tooling menos integrados con Vercel/edge. |

## Consecuencias

- **Positivas:** un solo lenguaje (TS) de UI a DB; OG images y middleware (WebView detection) nativos; deploy barato en Vercel (o Node standalone); Drizzle da tipos derivados del esquema.
- **Negativas / riesgos:** acoplamiento a convenciones de Next (Server Actions pueden cambiar); el sorteo y los jobs goteados no viven en request cycles → se implementan como endpoint protegido + cron/queue (ver ADR-008); SSR obliga a cuidar el manejo de secrets solo en server.
- **Restricción derivada:** ningún resultado de sorteo, mensaje anónimo ni credencial puede viajar en Client Components ni en payloads públicos; las queries a tablas sensibles pasan exclusivamente por capa server con RLS (ADR-003).
