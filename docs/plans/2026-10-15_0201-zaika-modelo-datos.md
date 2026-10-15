# ZAIKA — Modelo de datos detallado

Derivado del PRD v2 sección 35 (`docs/prds/2026-10-15_0201-zaika.prd.md`). Esquema físico listo para Drizzle sobre PostgreSQL (ADR-003). Incluye tipos, enums, índices, constraints y **políticas RLS por tabla**.

> Convención: ids `uuid` (v4) por defecto; timestamps `timestamptz` en UTC; nombres de tabla en snake_case plural. El ORM (Drizzle) define los tipos TS equivalentes; este documento es la fuente de verdad del diseño.

---

## 1. Enums

```sql
CREATE TYPE estado_intercambio AS ENUM (
  'borrador', 'activo', 'sorteado', 'en_revelacion', 'finalizado', 'archivado'
);
CREATE TYPE tier AS ENUM ('free', 'pro');
CREATE TYPE tipo_evento AS ENUM ('amigo_secreto');  -- extensible: futuros tipos
CREATE TYPE ocasion AS ENUM ('navidad');            -- extensible: futuras ocasiones
CREATE TYPE estado_participante AS ENUM (
  'invitado', 'registrado', 'perfil_completo', 'finalizado'
);
CREATE TYPE metodo_auth AS ENUM ('google', 'magic_link');
CREATE TYPE estado_pista AS ENUM ('pendiente', 'entregada');
CREATE TYPE tipo_mensaje AS ENUM ('pregunta', 'respuesta');
CREATE TYPE tipo_recuerdo AS ENUM ('foto', 'mensaje');
CREATE TYPE canal_notif AS ENUM ('push', 'email');
CREATE TYPE estado_pago AS ENUM ('pendiente', 'completado', 'fallido', 'reembolsado');
CREATE TYPE estado_codigo AS ENUM ('activo', 'canjeado', 'revocado');
```

---

## 2. Tablas

### 2.1 `usuario`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | gen_random_uuid() |
| email | citext UNIQUE NOT NULL | **clave de identidad**; normalizado lowercase |
| nombre | text NOT NULL | |
| foto_url | text NULL | de Google si aplica |
| metodo_auth_ultimo | metodo_auth | informativo |
| email_verificado_en | timestamptz NULL | |
| suprimido_en | timestamptz NULL | Ley 1644: soft-delete/anonimización |
| creado_en | timestamptz DEFAULT now() | |
| actualizado_en | timestamptz | trigger |

**Índices:** UNIQUE(email). **RLS:** lectura solo por el propio usuario (o servicio autenticado); sin listado público.

### 2.2 `intercambio`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| organizador_id | uuid FK usuario NOT NULL | |
| nombre | varchar(60) NOT NULL | 3–60 chars |
| tipo_evento | tipo_evento NOT NULL DEFAULT 'amigo_secreto' | eje mecánica |
| ocasion | ocasion NOT NULL DEFAULT 'navidad' | eje temática |
| fecha_evento | date NOT NULL | revelación presencial |
| timezone | text NOT NULL DEFAULT 'America/La_Paz' | cálculo de pistas |
| estado | estado_intercambio NOT NULL DEFAULT 'borrador' | máquina de estados sección 36 PRD |
| tier | tier NOT NULL DEFAULT 'free' | RN-03 |
| presupuesto_sugerido | numeric(10,2) NULL | informativo |
| reglas_exclusion | jsonb NULL | pares de exclusiones; validado antes del sorteo |
| grupo_origen_intercambio_id | uuid NULL FK intercambio | solo listado de participantes |
| link_generico_token | text NULL UNIQUE | invitación genérica revocable |
| link_generico_revocado_en | timestamptz NULL | |
| fecha_finalizacion | timestamptz NULL | |
| archivado_en | timestamptz NULL | reversible |
| creado_en / actualizado_en | timestamptz | |

**Índices:** (organizador_id, estado), (estado, fecha_evento). **RLS:** organización visible solo a `organizador_id = sub`; participantes ven el intercambio donde son miembros (join a `participante`). Campos de configuración (nombre, fecha, etc.) legibles por miembros; `reglas_exclusion` solo por el organizador (no expone quién es pareja de quién a participantes — decisión: solo organizador).

### 2.3 `participante`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| intercambio_id | uuid FK intercambio NOT NULL | |
| usuario_id | uuid FK usuario NULL | null hasta registrarse |
| nombre_mostrado | varchar(60) NOT NULL | puede diferir del nombre de Google |
| email_invitado | citext NULL | email preasignado por organizador (opcional) |
| token_invitacion | text NULL UNIQUE | link único por slot |
| estado | estado_participante NOT NULL DEFAULT 'invitado' | |
| reclamado_en | timestamptz NULL | cuándo se reclamó el slot |
| creado_en | actualizado_en | timestamptz |

**Constraints:** UNIQUE(intercambio_id, usuario_id) UNIQUE(intercambio_id, token_invitacion).
**Índices:** (intercambio_id, estado), UNIQUE por usuario dentro del intercambio.
**RLS:** el organizador del intercambio ve la lista; un participante ve su propio registro (y no los emails ajenos salvo necesidad mínima — ver decisión abajo).

> **Decisión de exposición:** la lista de participantes del organizador muestra nombre_mostrado + estado. El email de otro participante **no** es visible a pares participantes (evita enum y fuga). El organizador ve el email que él mismo preasignó (`email_invitado`) para reenviar invitaciones.

### 2.4 `perfil`

| Columna | Tipo | Notas |
|---|---|---|
| participante_id | uuid PK FK participante | 1:1 |
| gustos | text NULL | tags/libre |
| encuesta_completada_en | timestamptz NULL | marca "perfil completo" |
| actualizado_en | | |

**RLS:** solo el propio participante y el servicio; **no** visible a otros participantes (las pistas se derivan de `respuesta_encuesta`, no de leer el perfil entero).

### 2.5 `item_wishlist`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| participante_id | uuid FK participante NOT NULL | |
| nombre_descripcion | varchar(120) NOT NULL | obligatorio |
| foto_url | text NULL | miniatura |
| foto_original_url | text NULL | solo si tier permite |
| link_externo | varchar(500) NULL | texto libre |
| orden | int NOT NULL DEFAULT 0 | |
| creado_en | | |

**Constraints:** CHECK (0–10 ítems por participante — enforce en app + trigger opcional).
**RLS:** visible al dueño y a **quien le regala** (emisor de la asignación hacia el dueño). Visible también al organizador? **No** — la wishlist es dato privado de la capa 2 (sección 34 PRD); solo el dueño y su Santa la ven.

### 2.6 `pregunta_banco`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| texto | varchar(200) NOT NULL | |
| tier | ('base','pro') NOT NULL | base Free; pro = encuesta ampliada |
| orden | int NOT NULL | |
| activa | boolean DEFAULT true | |

**RLS:** lectura pública a usuarios autenticados (banco estático, no sensible).

### 2.7 `respuesta_encuesta`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| participante_id | uuid FK participante NOT NULL | respondiente |
| pregunta_id | uuid FK pregunta_banco NOT NULL | |
| respuesta_texto | varchar(300) NOT NULL | |
| creado_en | | |

**Constraints:** UNIQUE(participante_id, pregunta_id).
**RLS:** solo el dueño; **nunca** legible por otros humanos. El servicio (motor de pistas) lee con privilegio para generar `pista_entrega`.

### 2.8 `pista_entrega`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| intercambio_id | uuid FK NOT NULL | |
| respuesta_encuesta_id | uuid FK NOT NULL | |
| participante_emisor_id | uuid FK participante NOT NULL | **quien recibe la pista** (le regala al que respondió) |
| participante_receptor_id | uuid FK participante NOT NULL | quien respondió (para trazabilidad interna; no se expone al emisor) |
| fecha_programada | timestamptz NOT NULL | 09:00 America/La_Paz del día calculado |
| fecha_entregada | timestamptz NULL | |
| estado | estado_pista DEFAULT 'pendiente' | |

**Índices:** (estado, fecha_programada) para el cron; UNIQUE(respuesta_encuesta_id, fecha_programada) evita duplicados por reprogramación.
**RLS:** el emisor ve solo sus pistas `participante_emisor_id = sub` y solo las `entregada` (o la próxima programada si se decide mostrar countdown); nunca ve el listado completo de respuestas del receptor.

### 2.9 `asignacion`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| intercambio_id | uuid FK NOT NULL | |
| emisor_participante_id | uuid FK participante NOT NULL | |
| receptor_participante_id | uuid FK participante NOT NULL | |
| resultado_visto_en | timestamptz NULL | RN-02 / RF-07 |
| creado_en | | |

**Constraints:** UNIQUE(intercambio_id, emisor_participante_id) — un emisor, un receptor en amigo_secreto; CHECK (emisor <> receptor). La forma completa (derangement) se valida en la estrategia de sorteo, no solo en constraints.
**Índices:** (intercambio_id), UNIQUE emisor.
**RLS — CRÍTICA:** solo el emisor dueño (`emisor_participante_id = sub`) puede SELECT su fila; UPDATE de `resultado_visto_en` solo por el dueño. El organizador y otros participantes: **cero acceso**. El servicio de sorteo escribe con privilegio de servicio (bypass controlado) dentro de una transacción atómica.

### 2.10 `mensaje`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| intercambio_id | uuid FK NOT NULL | |
| emisor_participante_id | uuid FK NOT NULL | oculto en capa de presentación |
| receptor_participante_id | uuid FK NOT NULL | |
| tipo | tipo_mensaje NOT NULL | |
| contenido | varchar(280/500) NOT NULL | 280 pregunta / 500 respuesta |
| reportado | boolean DEFAULT false | |
| creado_en | | |

**Constraints:** CHECK de longitud por tipo; CHECK (emisor <> receptor).
**RLS:** el emisor ve sus mensajes enviados; el receptor ve los que le llegaron **pero la API/presentación NUNCA retorna `emisor_participante_id`** — la columna existe para lógica interna y auditoría. Rate-limit de preguntas (2 Free/20 Pro) se valida en app contra conteo por (emisor, intercambio).

### 2.11 `recuerdo_album`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| intercambio_id | uuid FK NOT NULL | |
| participante_id | uuid FK NOT NULL | autor |
| tipo | tipo_recuerdo NOT NULL | |
| contenido_url | text NULL | si foto |
| contenido_texto | varchar(500) NULL | si mensaje |
| creado_en | | |

**Constraint:** solo insertable si el intercambio está en `en_revelacion` o `finalizado` (app + trigger opcional).
**RLS:** legible por cualquier miembro del intercambio (participantes + organizador). Escritura solo por el propio autor y solo en fase de revelación.

### 2.12 `notificacion`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| usuario_id | uuid FK NOT NULL | |
| intercambio_id | uuid FK NOT NULL | |
| tipo | text NOT NULL | p.ej. 'nueva_pista', 'pregunta_recibida', 'sorteo_realizado', 'cierre' |
| canal | canal_notif NOT NULL | |
| estado_envio | text NOT NULL | 'pendiente','enviado','fallido' |
| payload_json | jsonb NULL | mínimo; sin contenido sensible en claro |
| enviado_en | timestamptz NULL | |
| leido_en | timestamptz NULL | |

**Índices:** (usuario_id, leido_en) para bandeja in-app. **RLS:** solo el dueño.

### 2.13 `preferencia_notificacion`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| usuario_id | uuid FK NOT NULL | |
| tipo | text NOT NULL | |
| canal | canal_notif NOT NULL | |
| habilitado | boolean DEFAULT true | |

**Constraints:** UNIQUE(usuario_id, tipo, canal). **RLS:** solo el dueño.

### 2.14 `suscripcion_push`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| usuario_id | uuid FK NOT NULL | |
| endpoint | text NOT NULL UNIQUE | |
| p256dh | text NOT NULL | |
| auth | text NOT NULL | |
| user_agent | text NULL | |
| creado_en / eliminado_en | | soft-delete si se desregistra |

**RLS:** solo el dueño.

### 2.15 `pago`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| intercambio_id | uuid FK NOT NULL | |
| organizador_id | uuid FK NOT NULL | |
| monto | numeric(10,2) NOT NULL | |
| moneda | char(3) DEFAULT 'USD' | |
| estado | estado_pago NOT NULL | |
| pasarela | text NULL | null si es código manual |
| referencia_externa | text NULL | id de la pasarela |
| fecha | timestamptz | |

**Constraints:** UNIQUE(intercambio_id) cuando estado='completado' (un Pro por intercambio). **RLS:** solo organizador dueño; servicio de webhooks con privilegio.

### 2.16 `codigo_activacion`

| Columna | Tipo | Notas |
|---|---|---|
| id | uuid PK | |
| codigo | text NOT NULL UNIQUE | token aleatorio fuerte (no secuencial) |
| intercambio_id | uuid NULL FK | set al canjear |
| canjeado_por | uuid NULL FK usuario | |
| canjeado_en | timestamptz NULL | |
| revocado_en | timestamptz NULL | |
| estado | estado_codigo DEFAULT 'activo' | |
| creado_en | | |

**RLS:** nadie lee la tabla salvo servicio/operador; el canje es un procedure del servidor que valida estado y hace el UPDATE atómico + pone `intercambio.tier='pro'` + log de auditoría (RNF-07).

---

## 3. Relaciones (resumen ER)

```
usuario 1──N intercambio (como organizador)
usuario 1──N participante (a través de usuario_id)
intercambio 1──N participante
participante 1──1 perfil
participante 1──N item_wishlist
intercambio 1──N asignacion (emisor→receptor, N por emisor)
participante 1──N mensaje (emisor) / (receptor)
intercambio 1──N pista_entrega
intercambio 1──N recuerdo_album
intercambio 1──0..1 pago / codigo_activacion
```

---

## 4. Estrategia RLS (patrón común)

```sql
-- Ejemplo para asignacion (el patrón se replica en tablas sensibles)
ALTER TABLE asignacion ENABLE ROW LEVEL SECURITY;

CREATE POLICY asignacion_select_propia ON asignacion
  FOR SELECT
  USING (
    emisor_participante_id IN (
      SELECT p.id FROM participante p
      JOIN usuario u ON u.id = p.usuario_id
      WHERE u.email = current_setting('app.current_user_email')
    )
  );
```

- El pool del servidor (o middleware) hace `SET LOCAL app.current_user_email = $email` por transacción (coherente con ADR-003).
- El **servicio de sorteo/cron** usa un rol de servicio que bypass RLS de forma controlada y auditada, solo en esas operaciones.
- Ninguna policy expone `asignacion` al organizador ni a terceros.

---

## 5. Índices transversales de performance

- `participante(token_invitacion)` UNIQUE — lookup de invitación O(1).
- `pista_entrega(estado, fecha_programada)` — el cron diario hace un `WHERE estado='pendiente' AND fecha_programada <= now()`.
- `mensaje(intercambio_id, receptor_participante_id, creado_en)` — bandeja del buzón.
- `asignacion(intercambio_id)` — validación de resolubilidad y conteos.
- `usuario(email)` UNIQUE — upsert de identidad en cada login.

---

## 6. Notas de cifrado / protección

- **En tránsito:** TLS obligatorio (ADR/hosting).
- **En reposo:** el proveedor de DB cifra discos por defecto. Las columnas de `asignacion` no requieren cifrado a nivel de columna adicional si RLS + acceso de servicio está estrictamente controlado; se evalúa `pgcrypto` para `contenido` de mensajes solo si se detecta riesgo real (costo de query). Default del MVP: RLS + auditoría, sin cifrado por columna.
- **Logs:** nunca loguear `emisor→receptor`, contenido de mensajes ni tokens de invitación/magic link en claro (RNF-07).

---

*Documento vivo: si el PRD v2 cambia un RF/RN que afecte este esquema, se actualiza aquí primero y luego en Drizzle.*
