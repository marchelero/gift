# ADR-003: Base de datos — PostgreSQL con Row-Level Security (RLS); proveedor Neon (default) o Supabase Postgres

- **Estado:** Aceptado
- **Fecha:** 2026-10-15
- **Contexto:** El requisito más duro del PRD es la privacidad del sorteo: el resultado (emisor→receptor) solo es legible por el participante dueño (RN-06, RN-14, secciones 19/34). Se necesita RLS real a nivel de fila, no solo checks en la capa de aplicación. Además: un desarrollador solo, costo bajo en año 1, migraciones versionadas, y capacidad de crecer a Latam.

## Decisión

**PostgreSQL** como única base de datos, con:

- **Row-Level Security activada** en tablas sensibles (`Asignacion`, `Mensaje`, `RespuestaEncuesta`, `PistaEntrega`, `ItemWishlist`, `RecuerdoAlbum`). Policies atadas al `sub` de la sesión (email/UUID de usuario) inyectado por el pool del servidor (p.ej. `request.jwt.claims` o variable de transacción `app.current_user_id`).
- **Proveedor: Neon** como default (serverless Postgres, branching para staging/dev, generous free tier, compatible con drivers estándar). **Alternativa aceptada: Supabase Postgres** si se quiere RLS + Storage + Realtime del mismo proveedor (ADR-005) — la decisión final de proveedor se cierra al provisionar, sin cambiar el esquema ni las policies.
- **ORM: Drizzle** (coherente con ADR-001): tipos TypeScript derivados del schema, migraciones con drizzle-kit, SQL cercano al metal para policies.

## Alternativas consideradas

| Opción | Por qué no |
|---|---|
| SQLite/Turso | RLS no existe; el aislamiento crítico tendría que reimplementarse 100% en app — riesgo de seguridad inaceptable para el diferencial del producto. |
| MongoDB | Mismos problemas de control fino de acceso por fila; modelo relacional encaja mejor (intercambios, asignaciones, mensajes). |
| MySQL | RLS no comparable a Postgres; Postgres es el estándar de facto para este patrón. |
| Supabase end-to-end (Auth+Storage+DB) | Viable y rápida, pero acopla auth (ADR-002 ya eligió Auth.js) y storage al proveedor; se deja como plan B si el tempo aprieta. |

## Consecuencias

- **Positivas:** defensa en profundidad — aunque un bug de UI pida datos ajenos, la policy de fila devuelve vacío; branching de Neon permite datos de prueba en staging sin tocar prod; Drizzle reduce ORM overhead.
- **Negativas / riesgos:** RLS con pools (p.ej. PgBouncer/Neon pooler) exige setear claims por transacción con cuidado (session vs transaction pooling); migrar policies es más delicado que migrar columnas; el sorteo debe correr con privilegio de servicio (bypass RLS controlado) y escribir resultados que luego solo el dueño lee.
- **Restricciones derivadas:** toda query a tablas con RLS pasa por el rol de aplicación con `SET ROLE`/claims; el rol superuser solo se usa en migraciones; audits de acceso a `Asignacion` se loguean sin exponer el par emisor→receptor en logs planos (RNF-07).
