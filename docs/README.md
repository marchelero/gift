# Documentación — ZAIKA

Índice de la etapa inicial (pre-código) del producto. Fuente vigente del contrato de producto: el **PRD v2**.

## Contrato de producto
- **[PRD v2](prds/2026-10-15_0201-zaika.prd.md)** ← fuente de verdad. Incluye MVP Mínimo Navidad (§47) y Done del MVP (§59).
- `ZAIKA_PRD.md` (raíz) — PRD v1, superseded, solo histórico.

## Decisiones de arquitectura
- **[Índice ADRs](adr/README.md)** — 8 decisiones: Next.js, Auth, Postgres+RLS, Pagos (plan B), Storage, Email/Push, PWA, Entornos/Jobs.

## Planes
- **[Modelo de datos](plans/2026-10-15_0201-zaika-modelo-datos.md)** — esquema físico, RLS por tabla, índices.
- **[Plan de implementación](plans/2026-10-15_0201-zaika-plan-implementacion.md)** — fases A–F, matriz de corte, gates hasta Navidad 2026.

## Contexto
- **[PROJECT.md](PROJECT.md)** — stack, non-negotiables, glossary, open questions.

## Estados de este repo
- Etapa actual: **documentación inicial completa; sin código**. Siguiente paso natural: ejecutar Fase A del plan (scaffold) cuando el dueño lo indique.
