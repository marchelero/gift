# ZAIKA — Plan de implementación

Derivado del PRD v2 (secciones 47 y 57) y los ADRs. Restricción rectora: **1 desarrollador, objetivo MVP Mínimo Navidad en producción antes del 1 de diciembre de 2026** (ventana real: ~6 semanas desde 2026-10-15). Este plan desglosa las fases A–F en tareas, dependencias, criterios de aceptación y **matriz de corte** para decidir qué se sacrifica si el tiempo aprieta sin romper la promesa Free.

> Orden de verdad: PRD v2 → ADRs → modelo de datos → este plan. Si hay conflicto, el PRD manda; los ADRs resuelven cómo; este plan resuelve en qué orden y con qué recorte.

---

## Resumen de fases

| Fase | Semana aprox. | Objetivo | Resultado al final de la fase |
|---|---|---|---|
| **A** | 1 | Esqueleto | Next+DB+auth+WebView+CI funcionando |
| **B** | 2 | Núcleo social | Crear intercambio, invitar, registrar participantes |
| **C** | 3 | Sorteo | Motor de asignación + privacidad RLS + resultado |
| **D** | 4 | Experiencia | Encuesta+pistas goteadas, wishlist, preguntas Free |
| **E** | 5 | Cierre | Estados revelación/finalizado, resumen+email, reutilizar |
| **F** | 6 | Pro mínimo + pulido | Tier+código, theming, álbum revelación, PWA push, QA privacidad, prod |

**Gate de Navidad (hard):** al terminar Fase E + lo mínimo de F, el checklist "Done del MVP" del PRD (sección 59) debe poder ejecutarse en staging con un grupo real de prueba. Lo que no esté listo entra en la matriz de corte, no en la fecha de lanzamiento.

---

## Fase A — Esqueleto (semana 1)

**Objetivo:** base técnica viva: app desplegable, DB con migraciones, login real, detección de WebView, pipeline CI.

| # | Tarea | Detalle / ADR | Hecho cuando |
|---|---|---|---|
| A1 | Scaffold Next.js 15 + TS estricto + Tailwind + ESLint/Prettier | ADR-001 | `npm run build` y deploy preview en Vercel |
| A2 | Conectar Neon (branch dev) + Drizzle schema inicial (enums + `usuario`) + drizzle-kit migrate | ADR-003/008 | migración aplica limpia en dev |
| A3 | Auth.js con Google + magic link; upsert por email; sesión | ADR-002 | login Google y magic link en dev |
| A4 | Middleware detección WebView + pantalla interstitial "Abre en tu navegador" | ADR-002, PRD 18/33 | al abrir link en WhatsApp embebido se muestra y fuerza navegador |
| A5 | Layout base mobile-first + rutas protegidas (mis intercambios / mi intercambio) | ADR-001 | navegación mínima entre pantallas shell |
| A6 | CI GitHub Actions: lint + typecheck + test unit | ADR-008 | PR bloquea si falla |
| A7 | Aviso de privacidad (Ley 1644) estático + flujos de cuenta | PRD 34 | página pública + aceptación en registro |

**Dependencias:** ninguna (es la base).
**Riesgos:** config OAuth de Google (redirect URIs por entorno) — dedicar tiempo real a no bloquearse.

---

## Fase B — Núcleo social (semana 2)

**Objetivo:** el organizador crea un intercambio y el participante se registra y queda vinculado.

| # | Tarea | Detalle | Hecho cuando |
|---|---|---|---|
| B1 | CRUD intercambio (crear/editar borrador): nombre, fecha, ocasión, tipo_evento, presupuesto | PRD 16, RF-01 | crear < 60 s en UI |
| B2 | Estados de intercambio + máquina de transiciones (borrador→activo…) | PRD 36 | transiciones validadas en servidor |
| B3 | Agregar participantes (nombre + email/tel opcional) + generar `token_invitacion` único | PRD 17, RF-02 | lista del organizador con estado invitado/registrado |
| B4 | Invitación genérica: link + código revocable/regenerable | RF-03 | revocar invalida el anterior |
| B5 | Compartir por WhatsApp (deep link) + **preview OG** de la invitación | PRD 44, RF-28 | el link en WhatsApp muestra título+imagen atractivos |
| B6 | Reclamo de slot único al registrarse; pre-asignación por email | PRD 18, RF-04 | un token no se reclama dos veces |
| B7 | Registro de participante completo (nombre_mostrado, reconocimiento por email) | ADR-002 | mismo email → mismo usuario entre ciclos |

**Dependencias:** A (todo). **Bloqueante Navidad:** sí — es el funnel de entrada.

---

## Fase C — Sorteo (semana 3)

**Objetivo:** ejecutar el sorteo con privacidad absoluta y reglas de exclusión. El núcleo diferencial de confianza del producto.

| # | Tarea | Detalle | Hecho cuando |
|---|---|---|---|
| C1 | Definición de reglas de exclusión en UI (pares) + guardado en `reglas_exclusion` | PRD 16 | el organizador define y ve las suyas |
| C2 | **Validación de resolubilidad previa** al sorteo; informa qué exclusión impide | PRD 19/39, RF-06 | sorteo irresoluble explica la causa concreta |
| C3 | Motor de asignación: derangement con restricciones; estrategia intercambiable por `tipo_evento` | ADR-001, PRD 15/53 | nadie se asigna a sí mismo; respeta exclusiones |
| C4 | Sorteo **idempotente y transaccional**; no re-ejecutable bajo RN-02 | PRD 19, RN-02 | doble click no sortea dos veces; re-sortear exige doble confirmación + nadie vio resultado |
| C5 | Tabla `asignacion` con **RLS estricta**: solo emisor lee su fila; registra `resultado_visto_en` | ADR-003, modelo 2.9 | organizador y otros participantes no ven ningún resultado |
| C6 | UI "Mi resultado" (a quién le regalo) con revelación cuidada | PRD 41 | solo el dueño ve el suyo |
| C7 | Notificación "sorteo realizado" | ADR-006 | todos reciben aviso sin contenido sensible |
| C8 | Test E2E/integración de privacidad: intentar leer resultado ajeno falla | PRD 59 | caso de prueba automatizado |

**Dependencias:** B. **Bloqueante Navidad:** sí — sin esto no hay producto.
**Nota:** esta fase concentra el riesgo de seguridad; dedicar margen a tests de RLS y a no filtrar en logs.

---

## Fase D — Experiencia (semana 4)

**Objetivo:** los diferenciadores Free que hacen que el producto "se sienta ZAIKA": pistas goteadas, wishlist, preguntas anónimas.

| # | Tarea | Detalle | Hecho cuando |
|---|---|---|---|
| D1 | Banco de preguntas (seed: 10–12 base) + UI encuesta; marca `encuesta_completada_en` | RF-09, modelo 2.6/2.7 | perfil completo = encuesta respondida |
| D2 | **Motor de entrega goteada**: calcula `ceil(N/D)` America/La_Paz, genera `pista_entrega` al sortear o al responder | PRD 23, RF-10 | cadencia correcta con fechas de prueba |
| D3 | **Cron diario** que entrega pistas vencidas + notificación push/email "nueva pista" | ADR-008/006 | pista llega a hora local sin abrir la app |
| D4 | Compresión automática por respuesta tardía + flush total al entrar en revelación | PRD 23, RN-12 | si `D=1` entrega todo ese día |
| D5 | UI "pistas que recibí de mi Santa" (acumulado, orden correcto) | PRD 20 | solo el emisor ve sus pistas (RLS) |
| D6 | Wishlist (0–10 ítems: nombre, foto miniatura, link externo) + subida con miniatura/EXIF strip | RF-08, ADR-005 | foto comprimida; solo dueño y Santa la ven |
| D7 | **Preguntas anónimas Free** (2/emisor) con anonimato real en UI/API/logs | RF-11, RN-04 | receptor no ve emisor; límite aplicado |
| D8 | Bandeja de preguntas/recibidas + respuestas (opcional) | PRD 21 | respuesta llega solo al emisor |
| D9 | Reportar mensaje + revisión por soporte auditado | RF-12 | reporte no expone emisor |

**Dependencias:** C. **Bloqueante Navidad:** D1–D8 sí; D9 puede llegar en la semana 5 si no hay capacidad, pero debe existir al menos el botón y el registro.

---

## Fase E — Cierre (semina 5)

**Objetivo:** el intercambio termina bien y queda memoria persistente; el organizador puede repetir el año que viene.

| # | Tarea | Detalle | Hecho cuando |
|---|---|---|---|
| E1 | Finalizar intercambio: doble confirmación → `en_revelacion` | PRD 25 | irreversible salvo soporte |
| E2 | Al entrar en revelación: flush de pistas pendientes + **habilitar álbum** | RN-12, RF-14 | álbum solo en revelación/finalizado |
| E3 | **Álbum Free** (hasta 10 ítems: fotos/mensajes; grid simple) | RF-14 | no se sube nada fuera de revelación |
| E4 | Transición a `finalizado`: congelar álbum (solo lectura) | RN-08 | sin escritura tras finalizar |
| E5 | **Resumen individual persistente en la app** (historial) | RF-15 | se consulta sin depender del email |
| E6 | **Email de cierre** personalizado server-side por destinatario (solo su resultado, datos agregados) | RF-16, RN-09 | ningún email expone a terceros |
| E7 | **Reutilizar grupo**: importa solo participantes, con edición obligatoria antes de confirmar | RF-17, RN-05 | no arrastra perfil/wishlist/mensajes/resultados |
| E8 | Editar `fecha_evento` con reglas (recalcula pistas pendientes; bloqueado en revelación) | RF-25 | comportamiento por estado |
| E9 | Archivar/desarchivar (solo visibilidad) | RF-26 | reversible |

**Dependencias:** D. **Bloqueante Navidad:** E1–E7 sí; E8–E9 pueden afinarse en semana 6.

---

## Fase F — Pro mínimo + pulido + QA + prod (semana 6)

**Objetivo:** capa Pro que se puede vender por código en Navidad, theming de ocasión, PWA push, y endurecimiento de seguridad antes de producción. Prioridad estricta: lo que no esté aquí entra enero 2027 (PRD 47).

| # | Tarea | Detalle | Prioridad |
|---|---|---|---|
| F1 | **Tier Pro a nivel intercambio** + validación de límites (preguntas 20, álbum ampliado, sin publicidad) | RN-03/04, ADR-004 | Alta |
| F2 | **Activación por código de un solo uso** (plan B pagos) + auditoría de canje | RF-29, ADR-004 | Alta |
| F3 | **Theming por ocasión Navidad** (paleta, textos, nombre "Buzón de Santa") | RF-23 | Alta |
| F4 | **Buzón temático Pro**: envoltorio narrativo (textos; audio solo si los clips están producidos) | RF-13, PRD 22/40 | Alta (audio: media — ver matriz de corte) |
| F5 | Encuesta ampliada Pro (preguntas adicionales) | RF-09 | Media |
| F6 | Álbum premium (más ítems, diseño; si no, Free basta) | RF-14 | Media |
| F7 | Cierre premium con estadísticas | PRD 10 | Baja (enero) |
| F8 | Temas visuales premium (1–2 sólidos; más solo si el diseño lo permite) | PRD 57 | Media |
| F9 | **PWA**: manifest + service worker + push Android + guía "Agregar a inicio" iOS | ADR-007, RNF-03 | Alta |
| F10 | Email como canal primario iOS (sin degradación crítica sin PWA) | ADR-006 | Alta |
| F11 | **QA de privacidad**: checklist seguridad (RLS, anonimato, logs, links, OG) | PRD 59 | Crítica |
| F12 | Rate-limits (magic link, preguntas, subidas) | PRD 40 | Alta |
| F13 | Sistema de reglas de publicidad (espacios, pantallas prohibidas; sin red real) | RF-19 | Baja |
| F14 | Seeds + entorno staging con datos de prueba | ADR-008 | Alta |
| F15 | Runbook de deploy + migraciones gating prod + rollback | ADR-008 | Alta |
| F16 | Smoke test end-to-end con grupo real de prueba en staging | PRD 59 | Crítica |

**Dependencias:** E. **Bloqueante Navidad:** F1–F4, F9–F12, F14–F16. F5–F8, F13 pueden correr a enero.

---

## Matriz de corte (si el tiempo aprieta)

Regla: **nunca se corta nada que impida el funnel Free completo (crear→invitar→sortear→pistas→cerrar→historial)**. Cortar por este orden, de más a menos preferible:

| Prioridad de corte | Qué se sacrifica | Consecuencia aceptada |
|---|---|---|
| 1 | Audio pregrabado del Buzón temático (F4 audio) | Buzón temático solo con theming visual y textos; audio llega cuando se produzca |
| 2 | Encuesta ampliada Pro (F5) | Pro = buzón temático + temas + sin publicidad + álbum premium; encuesta igual que Free |
| 3 | Temas premium adicionales (F8) | 1 tema Navidad base basta; Pro se diferencia por buzón + sin publicidad |
| 4 | Álbum premium (F6) | Álbum Free en revelación; premium en enero |
| 5 | Cierre premium estadísticas (F7) | Resumen individual simple; diferido |
| 6 | Publicidad (F13) | Free sin publicidad en Navidad (peor ingreso, mejor UX; aceptable un año) |
| 7 (NO cortar) | RLS/privacidad, sorteo, pistas goteadas, email cierre, reutilizar, WebView, PWA push mínimo | — núcleo de la promesa |

**Si la reanlidad es que ni Fase E completa llega a tiempo:** se lanza un **Free ultra-mínimo** solo si: sorteo + resultado privado + wishlist + encuesta con pistas + preguntas 2 + resumen en app + reutilizar. El cierre por email y el álbum se convierten en "fase 1.1" de enero. Comunicar expectativa honesta a los primeros grupos piloto.

---

## Hitos de verificación (gates)

| Gate | Cuando | Criterio |
|---|---|---|
| Gate A | fin semana 1 | login + WebView + deploy + CI verdes |
| Gate B | fin semana 2 | crear e invitar funciona end-to-end en dev con 3 usuarios reales |
| Gate C | fin semana 3 | sorteo con exclusiones correcto; test de privacidad pasa (nadie ve resultado ajeno) |
| Gate D | fin semana 4 | pistas goteadas llegan en cadencia; preguntas anónimas sin fuga de identidad |
| Gate E | fin semana 5 | un intercambio completo Free pasa el checklist Done (PRD 59) en staging |
| **Gate Navidad** | antes 1 dic 2026 | checklist Done en staging con grupo piloto real + Pro activable por código + smoke QA privacidad aprobado |

---

## Fuera del alcance de este plan (confirmado con el usuario)

- Nada de código, scaffolding ni implementación en esta etapa inicial (solo PRD + ADRs + modelo + este plan).
- GTM detallado, QA strategy formal y wireframes (no solicitados en esta etapa; si se quieren, se añaden como fase complementaria).
- Pasarela de pago real (queda ADR-004 + spike enero 2027).

---

## Índice de artefactos de la etapa inicial

1. PRD v2: `docs/prds/2026-10-15_0201-zaika.prd.md`
2. ADRs: `docs/adr/` (índice en `docs/adr/README.md`)
3. Modelo de datos: `docs/plans/2026-10-15_0201-zaika-modelo-datos.md`
4. Este plan: `docs/plans/2026-10-15_0201-zaika-plan-implementacion.md`
