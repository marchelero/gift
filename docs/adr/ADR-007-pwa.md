# ADR-007: PWA — Manifest + Service Worker (serwist/Workbox), installable, offline mínimo, push handler

- **Estado:** Aceptado
- **Fecha:** 2026-10-15
- **Contexto:** PRD secciones 1/43/33: mobile-first, PWA sin instalación obligatoria, instalable en Android nativo e iOS 16.4+ vía "Agregar a inicio". Soporte offline mínimo (lectura de contenido ya cargado). El service worker también es el receiver de Web Push (ADR-006). El participante casual no debe notar fricción; el organizador quiere algo "que se sienta app".

## Decisión

- **Manifest** completo: nombre, short_name, icons (múltiples densidades + maskable), theme/background colors alineados al tema base Navidad, `display: standalone`, `start_url` y `scope` correctos.
- **Service Worker con Serwist** (sucesor moderno de Workbox, alineado con build de Next) o Workbox directo si Serwist tiene fricción. Estrategias:
  - **App shell + assets estáticos:** cache-first con precache en build.
  - **Datos del intercambio ya cargados:** stale-while-revalidate o network-first con fallback a cache (lectura offline aceptable).
  - **Nunca cachear en claro:** respuestas con datos sensibles (resultado, mensajes, wishlist) no se persisten en Cache Storage sin cifrado/opción — se decide por endpoint: el default es **no persistir** datos sensibles en SW cache; solo shell + assets públicos. El "offline mínimo" se refiere a que la UI no se rompa, no a que el sorteo sea offline.
  - **Push handler:** `push` + `notificationclick` con open de la ruta correcta del intercambio.
- **iOS:** documentar y guiar en onboarding el "Agregar a inicio" (banner no bloqueante post-registro). Mientras no esté instalada, el push no llega → email primario (ADR-006).

## Alternativas consideradas

| Opción | Por qué no |
|---|---|
| App nativa (RN/Flutter) | Fuera de alcance del MVP (PRD sección 5); coste x10 y dos stores. |
| Solo web sin SW | Pierde push y sensación de app; no cumple RNF-03. |
| Workbox clásico a mano | Viable; Serwist reduce boilerplate y mejora compatibilidad con bundlers modernos. Se cae a Workbox si hay blockers. |

## Consecuencias

- **Positivas:** instalación sin store, updates silenciosos, base para push; costo cero de distribución.
- **Negativas / riesgos:** SW caches envejecidos → versionado de precache y `skipWaiting` controlado; iOS es impredecible con notificaciones → el producto no depende de push en iOS; debug de SW en dev es tedioso → feature flag para desactivar SW en dev.
- **Restricciones derivadas:** service worker jamás recibe ni persiste el resultado del sorteo; `notificationclick` no lleva payload sensible en la URL; testing E2E debe registrar/desregistrar SW para evitar flakiness.
