# ADR-005: Storage — Cloudflare R2 (default) o Supabase Storage; URLs firmadas, miniaturas server-side

- **Estado:** Aceptado
- **Fecha:** 2026-10-15
- **Contexto:** El producto almacena fotos de wishlist (opcionales, miniatura), fotos del álbum (solo en revelación) y clips de audio del narrador (pregrabados, servidos a todos los intercambios Pro de una ocasión). Límites del PRD sección 40: fotos ≤ 5–8 MB originales con miniaturas derivadas; audio ≤ 500 KB por clip. Costos deben quedar acotados por evento (RNF-06). Preferencia: sin servidor propio de uploads.

## Decisión

- **Bucket de objetos en Cloudflare R2** (default): sin egress fees, S3-compatible, integrable desde Next.js route handlers, CDN incluida.
- **Alternativa aceptada: Supabase Storage** si se eligió Supabase Postgres en ADR-003 (un solo proveedor para dev).
- **Flujo de subida:** el cliente pide un upload firmado (route handler autenticada) → sube directo al bucket con key namespaced por intercambio/usuario (`intercambio/{id}/wishlist/{participante}/{uuid}`) → el servidor confirma y **deriva miniatura** (sharp en un paso async o en el mismo handler con límite de tamaño) → solo la miniatura (y el original si el tier lo permite para álbum premium) se persisten como URL en DB.
- **Lectura:** URLs firmadas de lectura con expiración corta para contenido privado (wishlist, álbum); audio del narrador y OG images pueden ser públicos/cacheados por CDN.
- **Límites server-side:** validar Content-Type y tamaño antes de aceptar; strip de metadatos EXIF en fotos (privacidad: GPS).

## Alternativas consideradas

| Opción | Por qué no (default) |
|---|---|
| S3/GCS directo | Perfectos, pero egress y config más costosa para un MVP pequeño; R2 es más simple en costo. |
| Vercel Blob | Viable en el ecosistema Vercel; menos maduras las políticas de bucket/ACL que R2; se revisa si el deploy es 100% Vercel. |
| Subida a la BD (bytea/base64) | Destruye costos y performance; anti-patrón. |
| Google Drive / links externos del usuario | Fuera de control, rotura de links, mala UX; el PRD exige foto comprimida alojada. |

## Consecuencias

- **Positivas:** costo predecible y bajo a volumen año 1 (~2.000 eventos); miniaturas obligatorias protegen bandwidth y UX mobile; EXIF strip reduce riesgo de privacidad accidental.
- **Negativas / riesgos:** un paso async de miniatura puede fallar → estado pendiente + reintento; URLs firmadas complican el share público (el álbum es solo para el grupo — se resuelve con link de acceso vía app, no URL pública cruda).
- **Restricciones derivadas:** ningún path de storage expone el email del dueño; keys opacas; el álbum se sirve solo a miembros del intercambio (check server-side además de URL firmada).
