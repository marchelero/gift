# PRD — ZAIKA
### Plataforma de intercambios de regalos con experiencia, no solo organización
Versión 1.0 · Documento base para diseño de arquitectura, base de datos, backend, frontend y QA

> ⚠️ **SUPERSEDED (2026-10-15):** este documento v1.0 fue reemplazado por el **PRD v2** en `docs/prds/2026-10-15_0201-zaika.prd.md`. El v2 corrige inconsistencias (terminología Buzón/preguntas) y añade glosario, cumplimiento Ley 1644, límites técnicos, targets, MVP Mínimo Navidad y checklist de Done. Mantener este archivo solo como histórico; no es la fuente vigente.

---

## 1. Resumen Ejecutivo

ZAIKA es una plataforma web/PWA para organizar y **vivir** intercambios de regalos. A diferencia de herramientas puramente administrativas (sorteo, reglas, listas), ZAIKA construye una capa de experiencia alrededor del intercambio: comunicación anónima temática, una encuesta de pistas personales entregada progresivamente, y un cierre emocional con recuerdos y resúmenes personalizados que quedan guardados en la app. El producto **no reemplaza ni digitaliza el momento presencial de revelación** — lo acompaña antes y después.

El lanzamiento se enfoca en el **intercambio de regalos de Navidad** (amigo secreto, asignación 1:1), pero la plataforma está diseñada desde el día uno para **incorporar nuevos tipos de evento y ocasiones por configuración** (por ejemplo, todos regalan a una pareja en una boda, todos regalan a una persona, o una cuota colectiva), sin rediseño estructural.

Modelo de negocio: freemium por evento (no suscripción). El organizador paga una única vez (US$ 4,99) para desbloquear la experiencia Pro para todo el grupo. Free es completamente funcional y sostiene la adquisición; Pro vende experiencia, no funcionalidades básicas retenidas artificialmente.

Público inicial: personas en Bolivia (amigos, familias, oficinas, universidades, grupos sociales), sin foco corporativo/RRHH en esta etapa. Competidor de referencia: Pikkado (~1.7M usuarios, 57+ países), fuerte en organización; ZAIKA compite en experiencia, emoción e interacción progresiva, no en paridad de funciones administrativas.

---

## 2. Visión del Producto

Que organizar un intercambio de regalos deje de sentirse como una tarea logística (crear grupo, sortear, recordar reglas) y se convierta en una experiencia compartida que el grupo recuerda y repite — primero cada Navidad, y eventualmente en cada ocasión donde tenga sentido un intercambio.

---

## 3. Problema que Resuelve

- Organizar un intercambio por WhatsApp/grupos manuales es caótico: listas perdidas, sorteos manuales propensos a error o filtración, reglas que nadie recuerda, wishlists que se pierden en mensajes.
- Las soluciones existentes (ej. Pikkado) resuelven la logística pero dejan la experiencia plana: el intercambio "sucede" pero no se siente especial más allá del sorteo.
- No existe una capa ligera de interacción anónima y cierre emocional que haga que el grupo quiera repetir la experiencia al año siguiente con la misma app.

---

## 4. Objetivos

1. Ofrecer un flujo Free completo y realmente usable de principio a fin (crear, invitar, sortear, comunicar, cerrar).
2. Ofrecer una experiencia Pro que se perciba como "vívelo" y no como "más funciones administrativas".
3. Garantizar privacidad absoluta del resultado del sorteo y del anonimato en toda interacción.
4. Ser mobile-first, funcionar como PWA sin fricción de instalación obligatoria.
5. Sostener reutilización anual del grupo (participantes) como mecanismo de retención.
6. Modelar la plataforma sobre dos ejes independientes — **tipo de evento** (mecánica) y **ocasión** (temática) — para habilitar nuevos formatos de intercambio y nuevas ocasiones sin cambios estructurales.
7. Preparar la base técnica para escalar internacionalmente sin rediseño estructural.

---

## 5. No Objetivos (fuera de alcance del MVP)

- Marketplace, venta o entrega física de regalos, logística, delivery.
- Soluciones empresariales, RRHH, patrocinadores corporativos.
- Aplicación nativa (iOS/Android) — solo Web + PWA en esta fase.
- "Modo evento" que intente digitalizar la revelación presencial.
- Chat tradicional bidireccional en tiempo real.
- Gamificación compleja (rankings públicos, niveles, badges acumulables entre eventos).
- Tipos de evento distintos al amigo secreto (asignación 1:1). La plataforma se prepara para ellos (ver secciones 15 y 53), pero no se implementan en esta versión.
- Manejo de dinero entre participantes: cualquier forma de recaudación, cuota o pago colectivo queda fuera de la app. (El tipo de evento "cuota colectiva" se menciona solo como dirección futura; requeriría su propio documento.)
- Ocasiones adicionales en el lanzamiento: solo se entrega completa la ocasión Navidad; el sistema admite agregar más por configuración.
- Reutilización de datos de perfil, wishlist o respuestas entre años — solo se reutiliza el listado de participantes.
- Funciones que dependan de IA (audio generado por IA, asistente de regalos): fuera de esta versión por control de costos.

---

## 6. Público Objetivo

**Primario:** personas 16–45 años en Bolivia (extensible a Latam), organizando intercambios entre amigos, familia, compañeros de trabajo (como grupo informal, no como beneficio corporativo formal), grupos universitarios y sociales.

**Explícitamente fuera:** empresas comprando licencias para sus empleados como beneficio corporativo estructurado (eso requeriría RRHH, facturación empresarial, gestión multi-departamento — no está en el MVP).

---

## 7. Personas de Usuario

**7.1 La Organizadora — "María, 29 años"**
Coordina el amigo secreto de su grupo de amigas cada diciembre. Usa WhatsApp para todo. Quiere que sea fácil crear el grupo, que nadie se filtre el sorteo, y que quede bonito para compartir. Es la usuaria con mayor probabilidad de pagar el Pro porque es quien más invierte emocionalmente en que "salga bien".

**7.2 El Participante Casual — "Diego, 34 años"**
Recibe un link por WhatsApp, no quiere instalar nada, solo quiere completar su perfil rápido, ver pistas, y descubrir su amigo secreto el día del evento. Bajísima tolerancia a fricción — si el registro toma más de 1 minuto, abandona.

**7.3 El Participante Entusiasta — "Camila, 22 años"**
Disfruta el Buzón de Santa, responde la encuesta, sube fotos al álbum. Es quien genera boca a boca si la experiencia le gusta.

**7.4 El Administrador Recurrente — "Grupo de oficina, liderado por Jorge"**
Reutiliza el mismo grupo de participantes año tras año. Le importa que el proceso de reutilización sea instantáneo (no quiere re-invitar a 15 personas cada diciembre).

---

## 8. Propuesta de Valor

| | FREE — "Organiza" | PRO — "Hazlo memorable" |
|---|---|---|
| Promesa | Un intercambio funcional, sin fricción | Una experiencia que el grupo recuerda |
| Público | Todos | El organizador que quiere ir un paso más |
| Precio | Gratis (con publicidad discreta) | US$ 4,99 por intercambio, pago único |

---

## 9. Modelo de Negocio

- **Freemium por evento**, no por usuario ni por suscripción mensual.
- El **organizador** paga una vez y desbloquea Pro **para todos los participantes** de ese intercambio específico.
- Free se sostiene con publicidad discreta (máx. 2 espacios, nunca en momentos sensibles — ver sección 29).
- Pasarela de pago: **dependencia abierta**. Stripe no opera nativamente en Bolivia; se requiere una pasarela alternativa con soporte para Bolivia (a definir — bloqueante para desarrollo del módulo de pagos, no para el resto del MVP). Ver sección 51 (Dependencias).
- Nota de realismo financiero: con las proyecciones de referencia del año 1 (~100 eventos Pro), el ingreso no cubre costos operativos por sí solo; el MVP debe construirse optimizando costos de infraestructura (especialmente almacenamiento de audio y notificaciones) más que optimizando monetización temprana.

---

## 10. Free vs Pro — Detalle Funcional

### FREE incluye:
- Creación de intercambio (nombre, fecha, ocasión, presupuesto sugerido opcional)
- Gestión de participantes (agregar, quitar, reenviar invitación)
- Invitaciones vía WhatsApp / enlace / email (link único por participante, o link genérico con código revocable)
- Sorteo automático con reglas de exclusión y validación previa de resolubilidad
- **Encuesta de preguntas personales** (banco estático, respondida al registrarse) con **entrega goteada de pistas** al participante que le regala (ver sección 23)
- **Wishlist enriquecida**: de 0 a 10 ítems por participante, cada uno con nombre/descripción corta (obligatorio), foto (opcional, comprimida a miniatura) y link externo a cualquier sitio/tienda/marketplace (opcional, texto libre, sin scraping ni preview de precio — ZAIKA no vende ni intermedia productos)
- Preguntas anónimas básicas (límite: **2 preguntas por participante** durante todo el intercambio)
- Buzón anónimo básico (sin narrador temático, sin audio)
- Resultado del sorteo (privado, individual)
- Notificaciones push + email, configurables por el participante y consultables dentro de la app
- Historial persistente en la app: cada intercambio pasado conserva su resumen de cierre, consultable en cualquier momento (no solo enviado por email)
- Reutilización de grupo (solo participantes)
- Cierre de intercambio + resumen básico (texto simple, sin estadísticas agregadas), guardado en la app
- Álbum de recuerdos: **habilitado únicamente durante la fase de revelación/cierre** (una vez el organizador inicia el cierre, cualquier participante puede subir fotos y mensajes cortos), hasta **10 ítems** por intercambio (cada foto o mensaje cuenta como 1), sin diseño premium
- Publicidad discreta (máx. 2 espacios, nunca en momentos sensibles)

### PRO agrega (todo lo anterior +):
1. **Buzón temático** (Santa en ocasión Navidad / narrador neutro en ocasiones futuras) con audio pregrabado
2. Preguntas anónimas **ilimitadas** (hasta un límite técnico razonable, ej. 20 por participante, para evitar abuso)
3. **Encuesta ampliada**: preguntas adicionales al banco estándar, para pistas más específicas
4. **Álbum de recuerdos premium** (ítems ilimitados dentro de límites técnicos, diseño elegante, sin marca de agua), igualmente habilitado solo en fase de revelación/cierre
5. **Temas visuales premium** (5 temas para la ocasión Navidad: Navidad Clásica, Winter, Navidad Dorada, Santa, Noche de Navidad; ocasiones futuras definen su propio set en su configuración)
6. **Cierre premium** (resumen con estadísticas: participantes, regalos, preguntas, pistas, recuerdos, duración, y reconocimientos simbólicos como "más misterioso")
7. **Sin publicidad**

**Nota de estrategia:** la encuesta con entrega goteada de pistas es Free porque reemplaza directamente al antiguo "perfil de gustos" estático — es la misma función, mejor entregada, y es un diferenciador fuerte frente a Pikkado incluso en el tier gratuito. El límite de Free no está en tener o no tener pistas, sino en la cantidad y profundidad de estas, y en la experiencia narrativa alrededor de ellas (Buzón temático).

**Regla de negocio clave:** Pro se activa a nivel de *intercambio*, no de usuario. Si el organizador paga, todos los participantes de ese intercambio específico ven la experiencia Pro sin pagar individualmente.

---

## 11. Análisis Competitivo

| Dimensión | Pikkado | ZAIKA |
|---|---|---|
| Organización / sorteo / reglas | Fuerte, maduro | Paridad funcional (suficiente, no diferenciador) |
| Wishlist / perfiles | Fuerte | Paridad funcional, complementada con encuesta de pistas |
| Encuesta con pistas goteadas | Inexistente | **Diferenciador principal**, disponible desde Free |
| Experiencia narrativa | Débil/inexistente | **Diferenciador** (Buzón temático, Santa/narrador) |
| Recuerdos / álbum | Básico o inexistente | **Diferenciador** (álbum en revelación, cierre emocional persistente en la app) |
| Base instalada | ~1.7M usuarios, 57+ países | Nueva entrante |
| Modelo de precio | (variable, referencia externa) | Pago único por evento, US$4,99 |

**Conclusión estratégica:** ZAIKA no compite en cobertura funcional administrativa (ahí Pikkado ya ganó por volumen y tiempo en el mercado). Compite en la capa emocional/experiencial que Pikkado no ha construido. El roadmap y priorización deben protegerse de la tentación de "igualar features administrativas" y enfocarse en profundizar la experiencia.

---

## 12. Arquitectura Funcional (visión de alto nivel)

Módulos principales:

1. **Gestión de Intercambios** (creación, configuración, tipo de evento, ocasión, estado)
2. **Gestión de Participantes** (invitación, registro, perfil)
3. **Motor de Asignación** (sorteo con reglas de exclusión, validación de resolubilidad, aislamiento de secretos; implementado como estrategia intercambiable por tipo de evento)
4. **Comunicación Anónima** (Buzón, preguntas)
5. **Encuesta de Pistas** (banco estático + motor de entrega goteada con cadencia automática)
6. **Recuerdos / Álbum**
7. **Cierre y Resúmenes** (estadísticas, resúmenes persistentes, emails personalizados)
8. **Notificaciones** (push + email)
9. **Monetización** (pagos, publicidad)
10. **Identidad y Permisos** (roles, autenticación ligera de participantes)

---

## 13. Mapa de Funcionalidades (MVP)

```
ZAIKA
├── Intercambios
│   ├── Crear / Editar / Finalizar
│   ├── Tipo de evento (MVP: Amigo Secreto)  ← eje de mecánica
│   ├── Ocasión (MVP: Navidad)               ← eje de temática
│   └── Estados (Borrador, Activo, Sorteado, En Revelación, Finalizado, Archivado)
├── Participantes
│   ├── Invitar (link único, link genérico + código, WhatsApp, email)
│   ├── Registro (Google OAuth / magic link) y perfil (wishlist)
│   └── Reutilización (importar solo listado desde intercambio anterior)
├── Asignación / Sorteo
│   ├── Reglas de exclusión + validación de resolubilidad
│   └── Resultado individual (privado)
├── Encuesta de Pistas
│   ├── Banco estático de preguntas (base / ampliada Pro)
│   └── Entrega goteada al participante que regala (cadencia automática)
├── Comunicación Anónima
│   ├── Preguntas (básicas Free / ilimitadas Pro)
│   ├── Buzón temático (Pro, con audio pregrabado)
│   └── Reportar mensaje
├── Recuerdos
│   └── Álbum (habilitado solo en fase de revelación/cierre; básico / premium)
├── Cierre
│   ├── Finalizar intercambio → fase de revelación
│   ├── Resumen individual (persistente en la app)
│   ├── Resumen grupal (Pro, persistente en la app)
│   └── Emails de cierre (aviso de respaldo)
├── Notificaciones
├── Monetización
│   ├── Pago Pro (por evento)
│   └── Publicidad (Free)
└── Cuenta / Autenticación
```

---

## 14. Roles y Permisos

| Rol | Descripción | Puede ver |
|---|---|---|
| **Organizador** | Crea y administra el intercambio | Configuración, lista de participantes, estado del sorteo (NO el resultado individual de nadie, incluido el suyo, salvo que también sea participante — en cuyo caso ve solo el suyo) |
| **Participante** | Miembro del intercambio | Su propio resultado, su perfil, mensajes que él mismo envía/recibe, álbum compartido |
| **Sistema** | Automatizaciones (sorteo, emails, notificaciones) | Acceso técnico completo, sin exposición a UI ni a humanos |

**Regla de negocio crítica:** el Organizador **nunca** tiene una vista de "quién le tocó a quién" para todo el grupo. Esto se mantiene incluso en el resumen de cierre Pro — las estadísticas agregadas (cantidad de regalos, preguntas, etc.) no exponen el mapeo de asignaciones individuales.

---

## 15. Tipos de Evento y Ocasiones

La plataforma se modela sobre **dos ejes independientes** del intercambio:

**(a) Tipo de evento — la mecánica.** Define cómo se asignan los participantes, qué reglas de privacidad rigen y qué módulos aplican. En el MVP el **único tipo de evento soportado es `amigo_secreto`** (asignación 1:1 en ciclo cerrado: cada participante regala a uno y recibe de uno). El motor de asignación se implementa como **estrategia intercambiable por tipo**, de modo que incorporar un tipo nuevo sea agregar una estrategia y una configuración, nunca reescribir el modelo central ni los módulos.

Ejemplos de tipos de evento futuros (referencia, **no se implementan en esta versión**; ver sección 49):

| Tipo de evento futuro | Mecánica | Secreto |
|---|---|---|
| Todos regalan a una persona | N emisores → 1 receptor | No |
| Todos regalan a una pareja (boda) | N emisores → 1 receptor (la pareja cuenta como una unidad) | No |
| Cuota colectiva | Sin asignación; aporte a un fondo común | N/A |

**(b) Ocasión — la temática.** Define narrador, nombre del buzón, textos y temas visuales. En el lanzamiento se entrega completa la **ocasión Navidad** (narrador "Santa", "Buzón de Santa", 5 temas premium + tema base). El sistema admite agregar más ocasiones (incluida una genérica neutra) por configuración.

| Ocasión | Narrador | Nombre del Buzón | Temas visuales |
|---|---|---|---|
| Navidad (lanzamiento) | Santa | "Buzón de Santa" | 5 premium + base |
| (Futuras) | Configurable | Configurable | Configurable |

**Regla de diseño crítica:** ninguna referencia a "Santa", "Navidad" ni a la mecánica 1:1 debe quedar hardcodeada en el código o el modelo. Todo se resuelve por configuración (narrador, textos, audio, temas) y por estrategia (mecánica), de forma que agregar un tipo de evento o una ocasión no requiera cambios estructurales.

---

## 16. Flujo de Creación del Intercambio

**Objetivo:** que el organizador cree un intercambio en menos de 60 segundos.
**Actor:** Organizador.
**Precondiciones:** el organizador debe autenticarse (Google o magic link, ver sección 33) antes de crear el intercambio, ya que este queda asociado a su identidad (email) desde el inicio.
**Flujo principal:**
1. Organizador entra a ZAIKA → "Crear intercambio"
2. Ingresa nombre del intercambio, **ocasión** (en el lanzamiento: Navidad) y **tipo de evento** (en el lanzamiento: Amigo Secreto, único valor disponible)
3. Define fecha del intercambio (fecha de la revelación presencial)
4. Define presupuesto sugerido (opcional, informativo para la wishlist)
5. Define reglas de exclusión (opcional: ej. "pareja no puede tocarle a su pareja")
6. Sistema crea el intercambio en estado **Borrador**
**Flujos alternativos:** organizador reutiliza un grupo anterior → salta a importar participantes (sección 27).
**Errores:** nombre vacío, fecha en el pasado → validación bloqueante con mensaje claro.
**Reglas de negocio:** un intercambio no puede sortearse con menos de 3 participantes.
**Criterios de aceptación:** intercambio creado y visible en "Mis intercambios"; estado = Borrador; ocasión y tipo de evento almacenados y usados para theming y mecánica respectivamente.

---

## 17. Flujo de Invitación

**Objetivo:** que agregar participantes sea tan fácil como compartir un link.
**Actor:** Organizador.
**Flujo principal:**
1. Organizador agrega participantes por nombre (mínimo) + canal de contacto (teléfono o email, opcional para envío directo)
2. Sistema genera link único de invitación por participante
3. Envío vía WhatsApp (deep link), copiar enlace, o email
**Flujo alternativo — invitación genérica:** link + código de grupo revocable, para grupos grandes o auto-registro. El auto-registrado se incorpora sin aprobación previa (no se agrega fricción al caso dominante de grupos privados), pero **aparece de inmediato en la lista del organizador**, quien puede eliminarlo mientras el intercambio no esté sorteado. El link genérico puede revocarse y regenerarse (invalidando el anterior).
**Errores:** número de teléfono inválido → no bloquea, solo se deshabilita el envío directo por WhatsApp, se ofrece copiar link.
**Reglas de negocio:** los links de invitación (únicos y genéricos) se invalidan una vez el intercambio pasa a estado Sorteado. **No se admiten altas de participantes desde el sorteo en adelante** (ver RN-11); la única vía sería un re-sorteo bajo RN-02.
**Nota técnica crítica:** dado que WhatsApp es el canal principal de invitación, el link se abrirá frecuentemente dentro del navegador embebido (WebView) de WhatsApp, el cual **bloquea el login con Google OAuth** (error `disallowed_useragent`). El sistema debe detectar el WebView y resolverlo antes de intentar cualquier autenticación (ver sección 33).
**Criterios de aceptación:** cada invitación única es trazable; el organizador ve estado "Invitado / Registrado" por participante; un slot de invitación única no puede reclamarse dos veces.

---

## 18. Flujo de Registro (Participante)

**Objetivo:** onboarding sin fricción, sin contraseñas que crear ni recuperar.
**Actor:** Participante.
**Precondiciones:** ninguna cuenta previa requerida; la identidad se resuelve en este mismo flujo.
**Flujo principal:**
1. Participante abre el link de invitación
2. Sistema detecta si el navegador es un WebView (ej. WhatsApp, Instagram, Messenger). Si lo es, **muestra de inmediato** un interstitial "Abre esto en tu navegador" con botón para reabrir en el navegador predeterminado del dispositivo, **antes** de intentar cualquier login (Google bloquea OAuth en WebView en el 100% de los casos; intentar primero solo garantiza un fallo visible)
3. Una vez en navegador real, se presenta **"Continuar con Google"** como método principal
4. Si el participante no tiene o no desea usar Google, opción secundaria: **magic link por email** (ingresa su email → recibe un link de un solo uso → hace clic → queda autenticado, sin contraseña)
5. Tras autenticarse (por cualquiera de los dos métodos): si el link era único, **reclama el slot** del participante pre-creado por el organizador; si su nombre no venía preasignado, se completa/confirma con el nombre obtenido de Google o ingresado manualmente
6. Se le ofrece (no se obliga) agregar la PWA a su pantalla de inicio
7. Completa perfil: responde la encuesta de pistas (ver sección 23) y, opcionalmente, su wishlist (puede omitirse y completarse después)
**Flujos alternativos:** el email ya existe en el sistema (participante recurrente) → se reconoce automáticamente como el mismo usuario, sin duplicar identidad, independientemente de si esta vez usó Google o magic link.
**Errores:** link de invitación expirado/inválido → pantalla de error clara con opción de contactar al organizador. Magic link expirado (ej. > 15 minutos) → opción de reenviar.
**Reglas de negocio:** el **email es el identificador único de identidad**, sin importar el método de autenticación usado (Google o magic link) ni si el mismo participante usó métodos distintos en distintos intercambios. Ver sección 33 para el detalle completo del esquema de autenticación.
**Criterios de aceptación:** participante queda vinculado al intercambio; visible para el organizador como "Registrado"; ningún participante puede terminar con dos identidades distintas si usó el mismo email en momentos diferentes.

---

## 19. Flujo de Sorteo

**Objetivo:** ejecutar el sorteo respetando exclusiones, con privacidad garantizada.
**Actor:** Organizador (dispara la acción) / Sistema (ejecuta el algoritmo).
**Precondiciones:** mínimo 3 participantes registrados; reglas de exclusión definidas (si aplica) y matemáticamente resolubles.
**Flujo principal:**
1. Organizador presiona "Realizar sorteo"
2. Sistema **valida previamente** que las exclusiones no hagan el sorteo irresoluble (ej. participante excluido de todos los demás): verifica que exista una asignación válida antes de intentar nada
3. Sistema ejecuta el algoritmo de derangement con restricciones (nadie se saca a sí mismo, respeta exclusiones)
4. Resultado se almacena cifrado/aislado por participante; **ningún resultado se muestra automáticamente**
5. Intercambio pasa a estado **Sorteado**
6. Se notifica a todos: "El sorteo se realizó, descubre tu amigo secreto"
**Flujos alternativos:** sorteo irresoluble → sistema informa al organizador **qué exclusión específica lo impide**, sin sugerir cómo "forzar" el resultado.
**Errores:** menos de 3 participantes → bloqueo con mensaje.
**Reglas de negocio:** el sorteo es un evento único, **idempotente y transaccional** (no se ejecuta dos veces sobre el mismo intercambio). Re-sortear requiere confirmación explícita de doble paso y solo está permitido si **ningún** participante ha visto su resultado aún (ver sección 39, RN-02).
**Criterios de aceptación:** cada participante tiene una asignación válida, sin autoasignación, sin violar exclusiones; el dato no es accesible por el organizador ni por ningún rol distinto al participante dueño del resultado.

---

## 20. Flujo del Participante (día a día durante el intercambio)

**Objetivo:** dar continuidad de interacción entre el registro y el evento presencial.
**Actor:** Participante.
**Flujo principal:**
1. Participante ve su resultado (a quién le regala él — dato siempre visible desde que se sortea)
2. Puede ver el perfil/wishlist de la persona a la que le regala
3. Puede enviar preguntas anónimas a esa persona (ver sección 21)
4. Puede recibir preguntas/pistas de su propio Santa Secreto (quien le regala a él) sin saber quién es
5. Va recibiendo, de forma goteada, las pistas de la encuesta que respondió la persona a la que le regala (sección 23)

---

## 21. Flujo de Preguntas (anónimas)

**Objetivo:** permitir que quien regala conozca mejor a su asignado, sin revelar identidad.
**Actor:** Participante (como emisor y como receptor, en distintos momentos).
**Precondiciones:** sorteo realizado.
**Flujo principal:**
1. Participante A (quien le regala a B) escribe una pregunta corta
2. Sistema la entrega a B de forma anónima, sin ningún indicio de quién la envió
3. B responde (opcional) — la respuesta llega solo a A, también sin poder B saber si fue vista
**Reglas de negocio:** Free = 2 preguntas máx. por participante en todo el intercambio; Pro = hasta 20. El límite aplica al emisor, no al receptor.
**Moderación mínima:** cada mensaje ofrece la opción "Reportar". Reportar no expone al emisor al receptor; la revisión de mensajes reportados ocurre únicamente mediante un flujo de soporte auditado (acceso excepcional registrado en log, nunca una vista de administrador dentro de la app). Sin filtros automáticos de contenido en el MVP.
**Errores:** intento de superar el límite → bloqueado con mensaje ("Actualiza a Pro para preguntas ilimitadas" en Free).
**Criterios de aceptación:** en ningún punto del flujo (logs, UI, notificaciones) se expone el emisor al receptor.

---

## 22. Flujo Buzón Temático (Santa / Narrador)

**Objetivo:** envolver la pregunta anónima en una experiencia narrativa, no un chat plano.
**Actor:** Participante (Pro).
**Flujo principal:**
1. A escribe su pregunta en el Buzón
2. Si ocasión = Navidad: se presenta como "🎅 Santa tiene una pregunta para ti", con introducción en audio pregrabado (frase genérica + texto de la pregunta)
3. En ocasiones futuras: se presenta con el narrador configurado para esa ocasión (texto/tono equivalente)
**Reglas de negocio:** el audio combina clips pregrabados (frases de introducción) + texto de la pregunta leído o mostrado; **no se genera audio nuevo por IA en el MVP** (control de costos).
**Criterios de aceptación:** el Buzón temático solo está disponible en intercambios Pro; en Free existe la versión "preguntas básicas" sin envoltorio narrativo ni audio.

---

## 23. Flujo de Encuesta de Pistas con Entrega Goteada

**Objetivo:** darle al participante que regala información real y concreta sobre su asignado, sin depender de IA, entregada de forma progresiva para generar motivos de retorno a la app.
**Actor:** Participante (como respondiente de la encuesta, y como receptor de las pistas de la persona a la que regala).
**Precondiciones:** participante autenticado y registrado en el intercambio.
**Flujo principal:**
1. Al completar su registro (o en cualquier momento antes del cierre), el participante responde una **encuesta estática** de 10–12 preguntas personales predefinidas por el sistema (ej. color favorito, salado o dulce, talla de zapato, talla de ropa, accesorio favorito, hobby, bebida favorita, etc.)
2. El sistema programa la **entrega goteada** con una cadencia calculada automáticamente:
   - `N` = cantidad de respuestas del participante; `D` = días calendario restantes hasta la fecha del evento (zona horaria America/La_Paz), mínimo 1
   - `pistas_por_dia = ceil(N / D)`; las entregas ocurren en un horario fijo local (ej. 09:00), una por día salvo compresión
   - Las entregas **comienzan cuando el sorteo se ejecuta** (o al responder la encuesta, si se completa después del sorteo)
3. Cada pista se entrega **una a la vez**, únicamente a la persona que le regala a quien respondió, vía notificación push/email ("🎁 Nueva pista sobre tu amigo secreto")
4. El participante entra a la app para ver la pista acumulada junto con las anteriores, dentro de la sección de su intercambio
**Flujos alternativos:** el participante responde la encuesta tarde (a pocos días del evento) → el sistema comprime automáticamente la cadencia; si `D = 1`, entrega todas las pistas pendientes ese día, sin dejar ninguna sin entregar.
**Errores:** participante no completa la encuesta → no se generan pistas para quien le regala a él (no bloquea el resto del flujo, pero se notifica al organizador como advertencia opcional).
**Reglas de negocio:** el banco de preguntas es **fijo y estático en el MVP** (no editable por el organizador). Solo se entrega la pista al participante correspondiente (quien le regala a quien respondió) — nunca se expone la respuesta completa del cuestionario a nadie más. En Pro, la encuesta se amplía con preguntas adicionales más específicas (ver sección 10). Al entrar el intercambio en **En Revelación**, todas las pistas pendientes se entregan de inmediato.
**Criterios de aceptación:** las pistas llegan estrictamente en el orden y cadencia calculada; ninguna pista revela la identidad de quien la responde más allá de lo que el propio contenido de la respuesta implique (aceptable, ya que el receptor de la pista ya sabe a quién le regala).

---

## 24. Flujo de Recuerdos (Álbum)

**Objetivo:** que el grupo conserve momentos del intercambio, sin riesgo de filtrar el sorteo antes de tiempo.
**Actor:** Participante / Organizador.
**Precondiciones:** el intercambio debe estar en estado **En Revelación** — es decir, el organizador ya inició "Finalizar intercambio" (sección 25).
**Flujo principal:**
1. Mientras el intercambio está en estado Sorteado (o anterior), el álbum **no está disponible** para subir contenido (evita que una foto filtre indicios sobre quién tiene qué regalo antes de la revelación presencial)
2. Al iniciar el organizador la fase de revelación/cierre, el álbum se habilita: cualquier participante puede subir fotos y mensajes cortos
3. Free: máximo **10 ítems** totales por intercambio (cada foto o mensaje cuenta como 1), presentación simple (grid)
4. Pro: ítems ilimitados (dentro de límites técnicos razonables), presentación premium (con tema visual aplicado)
**Reglas de negocio:** el álbum se congela (solo lectura) una vez el intercambio pasa a estado Finalizado. El contenido subido es visible para todos los participantes del intercambio, no público fuera del grupo.
**Criterios de aceptación:** no existe ninguna vía en la UI para subir contenido al álbum mientras el intercambio no esté en En Revelación; contenido congelado al finalizar.

---

## 25. Flujo de Cierre

**Objetivo:** cerrar formalmente el intercambio, generar los artefactos de cierre y dejarlos disponibles de forma persistente dentro de la app.
**Actor:** Organizador (dispara "Finalizar intercambio").
**Precondiciones:** intercambio en estado Sorteado.
**Flujo principal:**
1. Organizador presiona "Finalizar intercambio"
2. Sistema confirma la acción (doble confirmación; irreversible salvo soporte manual)
3. El intercambio entra en **En Revelación**: se habilita el álbum (sección 24) y se entregan todas las pistas pendientes (sección 23)
4. Se ejecuta en cadena: generación de estadísticas → generación de resúmenes individuales → generación de resumen grupal (si Pro) → envío de emails de cierre (como notificación de respaldo) → **guardado del resumen completo dentro de la app, asociado permanentemente al intercambio** → guardado en historial → habilitación de "Reutilizar grupo" para el próximo ciclo
**Reglas de negocio:** una vez finalizado, no se pueden agregar más preguntas ni subir al álbum. El resumen de cierre **no depende del email para ser consultado** — el email es un canal de aviso adicional, pero el resumen vive en la sección de Historial de la app de forma indefinida.
**Criterios de aceptación:** estado final = **Finalizado**; todos los artefactos (resumen, álbum) quedan accesibles dentro de la app en cualquier momento futuro, sin depender de que el usuario conserve el email de cierre.

---

## 26. Flujo de Emails

**Objetivo:** cierre emocional vía email personalizado, sin filtrar información indebida.
**Actor:** Sistema (automatizado al finalizar).
**Flujo principal:** cada participante recibe un email individual con: nombre del intercambio, agradecimiento, fecha, cantidad de participantes, cantidad de preguntas/pistas/regalos (agregados del grupo, no desglose por persona), **su propio** resultado del sorteo, link al álbum, mensaje de cierre.
**Reglas de negocio:** el email nunca incluye el resultado de sorteo de otra persona que no sea el destinatario.
**Criterios de aceptación:** contenido 100% personalizado por destinatario, generado server-side, sin posibilidad de acceder al email de otro participante vía manipulación de link.

---

## 27. Flujo de Reutilización

**Objetivo:** que el organizador arme el intercambio del año siguiente en segundos.
**Actor:** Organizador.
**Precondiciones:** existe al menos un intercambio Finalizado previo del mismo organizador.
**Flujo principal:**
1. Al crear un nuevo intercambio, se ofrece "Reutilizar grupo de [intercambio anterior]"
2. Sistema importa **únicamente** el listado de participantes (nombre + email, que es su identificador único)
3. El organizador **debe poder editar** la lista importada (quitar/agregar personas) antes de confirmar (paso imprescindible, no opcional)
4. El nuevo intercambio se crea en estado Borrador, sin perfiles, wishlist, respuestas, ni resultados del año anterior
5. Al reabrir su link/invitación, cada participante se autentica de nuevo (Google o magic link, indistintamente del método usado el año anterior — el sistema lo reconoce por email) y debe volver a completar su perfil en el nuevo ciclo
**Reglas de negocio:** el historial del intercambio anterior permanece aislado y no editable desde el nuevo intercambio. El email como identificador único (sección 33) es lo que permite reconocer a un participante recurrente sin re-crear su identidad, aunque cambie de método de login entre ciclos.
**Criterios de aceptación:** el nuevo intercambio no contiene ninguna referencia a datos sensibles (sorteo, respuestas) del año anterior; solo nombres/emails. Un mismo email nunca genera dos identidades distintas.

---

## 28. Sistema de Notificaciones

**Canales:** push (vía PWA, con las limitaciones de iOS descritas en sección 33) + email (canal primario y permanente, no solo transitorio).
**Eventos que disparan notificación:** invitación recibida, pregunta en el buzón, nueva pista de la encuesta disponible (entrega goteada, sección 23), recordatorio de fecha próxima del evento, sorteo realizado, intercambio finalizado / resumen disponible.
**Reglas de negocio:** todas las notificaciones quedan asociadas al intercambio correspondiente y son consultables dentro de la app (no solo como push efímero); notificaciones configurables por el participante (opt-out granular por tipo y canal); nunca deben revelar contenido sensible en el texto de la notificación (ej. no mostrar la pregunta completa en la notificación push, solo "Tienes un mensaje nuevo").

---

## 29. Sistema de Publicidad (Free)

- Máximo 2 espacios publicitarios simultáneos en toda la experiencia Free.
- Prohibido: popups agresivos, video obligatorio, interstitials recurrentes.
- **Nunca** se muestra publicidad durante: revelación de resultado del sorteo, respuesta a una pregunta del buzón, visualización de wishlist/perfil sensible, proceso de pago.
- Meta cualitativa de producto: intrusión percibida ≈ 2/10.
- Decisión de implementación: el **sistema** de reglas de dónde-sí/dónde-no se construye en el MVP; la **activación** real de una red publicitaria específica es una decisión post-lanzamiento (dependencia de negocio, no bloqueante técnico).

---

## 30. Sistema Free/Pro (mecanismo técnico)

- El estado Pro es un **atributo del intercambio** (`tier: 'free' | 'pro'`), no del usuario ni de una cuenta global.
- El organizador puede pagar el upgrade en cualquier estado desde **Borrador hasta Sorteado**; el tier se aplica de inmediato a **todo el intercambio**, y todos los participantes heredan el acceso Pro dentro de ese intercambio únicamente.
- Los límites (preguntas, fotos, temas disponibles) se validan siempre contra el tier del intercambio al que pertenece la acción, nunca contra un estado global de usuario.

---

## 31. Requisitos Funcionales Detallados

- **RF-01.** Crear un intercambio con nombre, fecha, ocasión y tipo de evento (y presupuesto sugerido opcional).
- **RF-02.** Generar links de invitación únicos y trazables por participante.
- **RF-03.** Ofrecer invitación genérica (link + código) con auto-registro visible para el organizador, remoción pre-sorteo y revocación/regeneración del link.
- **RF-04.** Permitir el registro del participante vinculándolo al slot de invitación reclamado, usando el email como identidad única.
- **RF-05.** Ejecutar un sorteo tipo derangement respetando exclusiones, de forma idempotente y transaccional.
- **RF-06.** Validar previamente la resolubilidad del sorteo e informar al organizador qué exclusión concreta lo impide cuando no sea resoluble.
- **RF-07.** Impedir que cualquier rol distinto al participante dueño acceda a su resultado de sorteo; registrar `resultado_visto` para habilitar el re-sorteo solo bajo RN-02.
- **RF-08.** Permitir de 0 a 10 ítems de wishlist por participante, con nombre/descripción corta obligatoria, foto opcional (miniatura) y link externo opcional de texto libre, sin preview, scraping de precio ni flujo de compra.
- **RF-09.** Ofrecer la encuesta de pistas (base Free de 10–12 preguntas; ampliada en Pro) con banco estático.
- **RF-10.** Entregar las pistas de forma goteada con cadencia calculada automáticamente (`ceil(N/D)`, America/La_Paz), iniciada al ejecutarse el sorteo, con compresión automática por respuesta tardía y entrega total de pendientes al entrar en En Revelación.
- **RF-11.** Limitar preguntas anónimas según el tier (2 Free / 20 Pro) sin exponer nunca al emisor.
- **RF-12.** Permitir reportar un mensaje sin exponer al emisor al receptor, con revisión por soporte auditado.
- **RF-13.** Ofrecer el Buzón temático solo en Pro, con narrador y audio pregrabado configurados por ocasión.
- **RF-14.** Habilitar el álbum únicamente en estado En Revelación, con límite de 10 ítems (Free) o ilimitado dentro de límites técnicos (Pro), y congelarlo (solo lectura) al finalizar.
- **RF-15.** Generar y persistir el resumen de cierre (individual y grupal Pro) dentro de la app de forma indefinida, accesible desde el Historial.
- **RF-16.** Enviar emails de cierre personalizados, generados server-side por destinatario, sin exponer datos de terceros.
- **RF-17.** Permitir reutilizar solo el listado de participantes de un intercambio anterior, con edición de la lista importada antes de confirmar.
- **RF-18.** Soportar notificaciones push (con fallback a email) y email directo, con opt-out granular por tipo y canal, todas asociadas al intercambio y consultables dentro de la app.
- **RF-19.** Restringir la publicidad para no aparecer en las pantallas listadas en la sección 29.
- **RF-20.** Registrar el tier (Free/Pro) a nivel de intercambio, permitir el upgrade pagando entre Borrador y Sorteado, y validar los límites en consecuencia.
- **RF-21.** Autenticar usuarios (organizadores y participantes, mismo esquema) vía Google OAuth como método principal y magic link por email como fallback, usando el email como identificador único de identidad en ambos casos.
- **RF-22.** Detectar navegadores WebView al abrir un link de invitación y mostrar el interstitial de apertura en el navegador del sistema **antes** de iniciar cualquier flujo de autenticación.
- **RF-23.** Aplicar el theming (colores, narrador, nombre del buzón, audio) según la **ocasión** del intercambio, por configuración.
- **RF-24.** Modelar el intercambio con un atributo `tipo_evento` separado de la ocasión (único valor soportado en el MVP: `amigo_secreto`), con el motor de asignación implementado como estrategia intercambiable, de modo que nuevas mecánicas se agreguen sin modificar el modelo central ni los módulos.
- **RF-25.** Permitir editar `fecha_evento` libremente en Borrador/Activo; en Sorteado, con doble confirmación y recálculo de la cadencia de pistas pendientes; y bloquear la edición desde En Revelación.
- **RF-26.** Permitir archivar y desarchivar intercambios Finalizados, con efecto solo sobre su visibilidad en listados.
- **RF-27.** Impedir altas de participantes desde el estado Sorteado en adelante (la única vía de incorporación post-sorteo es un re-sorteo bajo RN-02).

---

## 32. Requisitos No Funcionales

- **RNF-01 Rendimiento:** tiempo de carga inicial < 2.5s en 4G en dispositivos de gama media.
- **RNF-02 Disponibilidad:** el sorteo y la generación de emails de cierre deben ser operaciones idempotentes y transaccionales (no se puede ejecutar el sorteo dos veces sobre el mismo intercambio sin confirmación explícita).
- **RNF-03 Compatibilidad:** funcionamiento completo en Chrome/Safari (Android/iOS/desktop), soporte PWA instalable en Android nativo y en iOS 16.4+ vía "Agregar a inicio".
- **RNF-04 Escalabilidad funcional:** la plataforma debe admitir nuevos tipos de evento (estrategia de asignación) y nuevas ocasiones (configuración de narrador/temas/textos) sin cambios estructurales en el modelo de datos ni en los módulos.
- **RNF-05 Escalabilidad técnica:** arquitectura preparada para multi-idioma y multi-región (aunque el MVP lance solo en español/Bolivia).
- **RNF-06 Costos de notificaciones y almacenamiento:** el envío de notificaciones goteadas (encuesta) y el almacenamiento de audio/fotos deben estar acotados por evento para controlar costo variable a medida que crece el volumen de intercambios.
- **RNF-07 Auditoría:** todo acceso a datos sensibles (resultado de sorteo, mensajes reportados) debe quedar registrado en logs internos con propósito de auditoría de seguridad, sin exponer el contenido en logs planos.

---

## 33. Seguridad y Autenticación

**Esquema de autenticación (unificado para organizador y participante — mismo mecanismo, sin distinción por rol):**

- **Método principal: Google OAuth ("Continuar con Google").** El usuario se autentica con su cuenta Gmail existente; se obtiene email, nombre y foto de perfil verificados, sin pedir ningún dato adicional en el registro.
- **Método secundario (fallback): Magic Link por email.** Para quien no tiene o no desea usar Google. El usuario ingresa su email, recibe un link de un solo uso con expiración corta (ej. 15 minutos), y al hacer clic queda autenticado. No se implementa un sistema de usuario/contraseña tradicional (sin "olvidé mi contraseña", sin hash de contraseñas que gestionar) — intencional para mantener la fricción mínima también en el fallback.
- **El email es la clave única de identidad**, independientemente del método usado. Si una persona se autentica con Google un año y con magic link al siguiente (o viceversa), el sistema debe reconocerla como el mismo usuario mientras el email coincida. No debe existir la posibilidad de que un mismo email genere dos cuentas distintas.
- Un mismo esquema de auth aplica tanto al organizador como al participante — no se justifica mantener dos sistemas distintos, ya que una persona puede ser participante un año y organizador al siguiente.

**Manejo de WebView (WhatsApp y similares):**

- Google bloquea activamente el login OAuth cuando se ejecuta dentro de un navegador embebido (WebView), como el que usa WhatsApp, Instagram o Messenger para abrir links (error `disallowed_useragent`).
- El sistema **detecta el user-agent de WebView** al abrir cualquier link de invitación y, al detectarlo, muestra **de inmediato** un interstitial claro ("Abre esto en tu navegador") con un botón que fuerza la apertura en el navegador predeterminado del sistema operativo, **antes de intentar cualquier login**. Esto evita un fallo garantizado del método principal y no agrega fricción a quienes ya abren en un navegador real (Chrome/Safari).
- El magic link por email no tiene este problema (el correo se abre en la app de mail, no en el WebView), por lo que sirve como fallback natural si la detección de WebView falla en algún caso límite.

**Otros:**

- **Nota técnica crítica (iOS):** las notificaciones push web solo funcionan en iOS si el usuario agregó la PWA a la pantalla de inicio (iOS 16.4+). El email debe tratarse como canal de notificación **primario y permanente**, no como respaldo temporal, mientras la app nativa no exista.
- Cifrado en tránsito (HTTPS/TLS) obligatorio en toda la plataforma.
- Resultado del sorteo almacenado con control de acceso a nivel de fila (row-level security) atado al participante dueño (identificado por email), no accesible por queries administrativas estándar sin flujo explícito de soporte auditado.

---

## 34. Privacidad y Manejo del Anonimato

Separación de datos en tres capas:

1. **Datos del grupo** — nombre del intercambio, fecha, lista de participantes (visibles para el organizador).
2. **Datos privados del participante** — perfil, gustos, wishlist (visibles para el propio participante y para quien le fue asignado como emisor de regalo hacia él).
3. **Datos secretos del sorteo** — quién le regala a quién (visible únicamente por el propio participante respecto a su propio resultado; nunca por el organizador ni por otros participantes).

**Regla de anonimato en Buzón/preguntas:** el receptor nunca puede identificar al emisor por ningún medio de la UI (nombre, avatar, hora de envío correlacionable, estilo de escritura expuesto en metadata, etc.). La moderación de mensajes reportados ocurre solo por flujo de soporte auditado, nunca como una vista de administrador en la app.

---

## 35. Modelo de Datos Conceptual

### Entidades Principales

- **Usuario**: id, email (**único, clave de identidad**), nombre, foto_url (de Google si aplica), metodo_auth_ultimo (google | magic_link, informativo), fecha_creación. Representa tanto a organizadores como a participantes — un mismo registro de Usuario puede ser organizador de un intercambio y participante de otro.
- **Intercambio**: id, nombre, tipo_evento (enum; MVP: `amigo_secreto`), ocasion (enum/config; MVP: `navidad`), fecha_evento, estado, tier (free|pro), organizador_id (FK a Usuario), presupuesto_sugerido, reglas_exclusion, archivado_en (nullable), fecha_finalizacion, creado_en, actualizado_en.
- **Participante**: id, intercambio_id, usuario_id (FK a Usuario), nombre_mostrado (puede diferir del nombre de Google si el organizador lo preasignó distinto), estado_registro, perfil_id, reclamado_en (nullable — cuándo se reclamó el slot de invitación).
- **Perfil**: participante_id, gustos (texto/tags), wishlist (lista de items).
- **ItemWishlist**: id, participante_id, nombre_descripcion (obligatorio, corto), foto_url (opcional, comprimida/miniatura), link_externo (opcional, texto libre tipo URL), orden, fecha. Máximo 10 ítems por participante.
- **PreguntaBanco**: id, texto, tier (base | pro), orden, activa. Banco estático de la encuesta, administrado por el sistema.
- **RespuestaEncuesta**: id, participante_id (FK), pregunta_id (FK), respuesta_texto, fecha.
- **PistaEntrega**: id, intercambio_id, respuesta_encuesta_id (FK), participante_receptor_id (quien le regala al que respondió), fecha_programada_envio, fecha_entregada, estado (pendiente/entregada).
- **Asignación (Sorteo)**: id, intercambio_id, emisor_participante_id, receptor_participante_id, resultado_visto_en (nullable) — tabla con máxima restricción de acceso. Genérica (emisor→receptor); la mecánica 1:1 se valida en la estrategia, no en constraints rígidos.
- **Mensaje (Buzón)**: id, intercambio_id, emisor_participante_id (oculto en capa de presentación), receptor_participante_id, contenido, tipo (pregunta/respuesta), reportado (boolean, default false), fecha.
- **RecuerdoAlbum**: id, intercambio_id, participante_id (autor), tipo (foto/mensaje), contenido_url/texto, fecha — solo insertable si el intercambio está en estado En Revelación o Finalizado.
- **Notificación**: id, usuario_id (FK), intercambio_id (FK), tipo, canal, estado_envio, fecha.
- **PreferenciaNotificacion**: id, usuario_id (FK), tipo, canal (push | email), habilitado (boolean). Default: todo habilitado.
- **SuscripcionPush**: id, usuario_id, endpoint, claves (p256dh/auth), user_agent, fecha.
- **Pago**: id, intercambio_id, organizador_id, monto, estado, pasarela, fecha.

### Relaciones

- Un **Intercambio** tiene muchos **Participantes** (1:N).
- Un **Participante** tiene un **Perfil** (1:1).
- Un **Intercambio** tiene muchas **Asignaciones**, una por emisor (N:1 respecto al emisor). La forma del conjunto (ciclo/derangement, todos-a-uno, etc.) depende del tipo de evento y se valida en la estrategia de asignación.
- Un **Participante** puede enviar/recibir muchos **Mensajes** (vía intercambio_id, con emisor/receptor).
- Un **Intercambio Pro** genera un **Pago** (1:1 por evento pagado).
- Un **Intercambio** puede referenciar a un **Intercambio anterior** solo a través de una relación de "grupo origen" limitada al listado de participantes (no relación completa entre entidades).

---

## 36. Estados de un Intercambio

`Borrador → Activo → Sorteado → En Revelación → Finalizado ⇄ Archivado`

- **Borrador:** creado, participantes en proceso de agregarse, sorteo no ejecutado.
- **Activo:** se activa automáticamente al enviar la primera invitación; participantes registrándose. (El sorteo puede ejecutarse desde Borrador o Activo con ≥3 participantes registrados — el gate es el mínimo, no el estado.)
- **Sorteado:** sorteo ejecutado, resultados individuales disponibles, interacción habilitada (buzón, preguntas, entrega goteada de pistas); álbum aún **no** habilitado; no se admiten altas de participantes.
- **En Revelación:** el organizador inició "Finalizar intercambio"; álbum habilitado para todos y pistas pendientes entregadas de inmediato, a la espera de que se completen los artefactos de cierre.
- **Finalizado:** cierre completado, resúmenes generados y persistidos en la app, álbum congelado (solo lectura).
- **Archivado:** transición manual y reversible desde Finalizado (Archivar/Desarchivar); solo afecta la visibilidad en listados. Visible solo en historial, sin acciones activas salvo "Reutilizar grupo".

---

## 37. Estados de Participantes

`Invitado → Registrado → Perfil completo → (Sorteado — heredado del intercambio) → Finalizado`

- **Perfil completo** = encuesta de pistas respondida. (La wishlist es opcional y no bloquea este estado.)

---

## 38. Reglas de Negocio (consolidado)

- **RN-01:** Mínimo 3 participantes registrados para ejecutar sorteo.
- **RN-02:** El sorteo no puede re-ejecutarse una vez que al menos un participante ha visto su resultado (`resultado_visto_en` no nulo para cualquier asignación).
- **RN-03:** El tier Pro se define a nivel de intercambio y hereda a todos los participantes de ese intercambio.
- **RN-04:** Preguntas anónimas: 2 máx (Free) / 20 máx (Pro), por emisor, por intercambio.
- **RN-05:** Reutilización de grupo solo transfiere nombre + contacto de participantes, nunca datos de perfil, wishlist, mensajes o resultados.
- **RN-06:** El organizador nunca tiene acceso de lectura al mapeo completo de asignaciones del sorteo.
- **RN-07:** La publicidad no puede renderizarse en las pantallas de resultado de sorteo, respuesta a preguntas, ni checkout de pago.
- **RN-08:** El álbum se congela (solo lectura) al finalizar el intercambio.
- **RN-09:** Los emails de cierre son generados server-side por destinatario; ningún link de email puede exponer el contenido de otro destinatario.
- **RN-10:** El álbum Free admite máximo 10 ítems (cada foto o mensaje corto cuenta como 1).
- **RN-11:** No se admiten altas de participantes desde el estado Sorteado en adelante; la única vía es un re-sorteo bajo RN-02.
- **RN-12:** Las pistas pendientes de entrega se entregan todas al entrar el intercambio en En Revelación.
- **RN-13:** El upgrade Pro está disponible desde Borrador hasta Sorteado y se aplica de inmediato a todo el intercambio.

---

## 39. Casos Límite

- Intercambio con exclusiones que hacen el sorteo matemáticamente irresoluble (ej. 4 personas, 2 parejas, ambas excluidas entre sí) → el sistema debe detectarlo **antes** de intentar sortear y explicar qué exclusión lo causa.
- Participante que nunca completa la encuesta → quien le regala a él simplemente no recibe pistas goteadas (no bloquea el resto del flujo, pero se pierde parte de la experiencia); considerar un recordatorio suave si a mitad de camino aún no ha respondido.
- Participante que responde la encuesta muy cerca de la fecha del evento → la cadencia se comprime automáticamente para no dejar pistas sin entregar (ver sección 23).
- Organizador que también es participante → debe poder ver su propio resultado como participante, pero seguir sin acceso a los demás como organizador.
- Cambio de `fecha_evento` con el sorteo ya realizado → se recalcula la cadencia de las pistas pendientes; no editable desde En Revelación.
- Reutilización de grupo cuando algunos participantes del año anterior ya no participarán → el organizador debe poder editar la lista importada antes de confirmar el nuevo intercambio.
- Intento de acceder a un link de invitación ya usado/expirado → mensaje claro, sin exponer si el link corresponde a un intercambio válido o no (evitar enumeración).
- Alta de participante posterior al sorteo → bloqueada (RN-11); el sistema explica que solo un re-sorteo (bajo RN-02) permitiría incorporar a alguien.
- Participante en iPhone que nunca agrega la PWA a inicio → debe seguir recibiendo notificaciones relevantes por email sin degradación de experiencia crítica.

---

## 40. Errores y Validaciones

- Toda acción destructiva o irreversible (finalizar intercambio, re-sortear) requiere confirmación explícita de doble paso.
- Validaciones de formularios en cliente y servidor (nunca confiar solo en validación de UI).
- Mensajes de error orientados al usuario final (sin jerga técnica ni stack traces expuestos).

---

## 41. UX/UI

- Mobile-first, diseño limpio, sin estética infantil.
- Tono: navideño (en ocasión Navidad) / (futuro) neutro-cálido por ocasión, moderno, elegante, con toques emocionales pero no "empresarial-frío" ni "app de niños".
- Momentos de alta sensibilidad (ver resultado del sorteo, leer una pregunta) deben tener microinteracciones cuidadas (ej. animación de "revelar", no un simple texto plano) — sin que esto se confunda con digitalizar la revelación presencial del regalo físico, que sigue siendo un evento aparte.

---

## 42. Arquitectura de Navegación (alto nivel)

```
Home (login/crear/unirse)
├── Mis Intercambios (organizador)
│   ├── Detalle Intercambio
│   │   ├── Participantes
│   │   ├── Reglas
│   │   ├── Sorteo
│   │   └── Finalizar
├── Mi Intercambio (participante)
│   ├── Mi Resultado
│   ├── Encuesta (mía, a responder) / Wishlist (mía)
│   ├── Persona a la que regalo (wishlist + pistas goteadas recibidas)
│   ├── Buzón
│   ├── Álbum (solo visible/activo en fase de revelación)
│   └── Notificaciones del evento
└── Historial
    └── Intercambios pasados (resumen + álbum persistente, Finalizados/Archivados)
```

---

## 43. Responsive / PWA

- PWA instalable (manifest + service worker) para caching básico de assets estáticos y soporte offline mínimo (lectura de contenido ya cargado).
- Push notifications vía Web Push API, con las limitaciones de iOS ya documentadas (sección 33).
- Diseño responsive con breakpoints estándar (mobile / tablet / desktop), priorizando siempre el layout mobile como base.

---

## 44. Métricas y Analytics

- Tasa de conversión Free → Pro por intercambio.
- Tasa de finalización del flujo de registro de participantes (invitado → perfil completo).
- Tasa de uso de funcionalidades Pro dentro de intercambios pagados (Buzón temático, Encuesta ampliada, Álbum premium) — para validar qué feature realmente sostiene el valor percibido.
- Tasa de finalización de la encuesta de pistas (% de participantes que la completan) — mide adopción del mecanismo de retención principal del MVP.
- Tasa de reutilización de grupo año contra año (retención real del producto).

---

## 45. KPIs (sugeridos para V1)

- % de intercambios que llegan a estado Finalizado (salud del funnel completo).
- % conversión Pro sobre total de intercambios creados.
- NPS o encuesta simple post-cierre ("¿qué tan probable es que reutilices ZAIKA el próximo año?").
- Tiempo promedio desde creación hasta primer participante registrado (fricción de invitación).

---

## 46. MVP (alcance mínimo viable)

Incluye: autenticación vía Google OAuth + magic link con manejo de WebView, creación de intercambio con tipo de evento (amigo secreto) y ocasión (Navidad), invitaciones (links únicos y genérico), registro de participantes, wishlist, encuesta de pistas con entrega goteada (base Free / ampliada Pro), sorteo con exclusiones y validación de resolubilidad, preguntas básicas (Free) y Buzón temático (Pro) sin audio generado por IA (solo pregrabado), álbum habilitado solo en fase de revelación (básico y premium), cierre con resumen persistente en la app + email de aviso, reutilización de grupo (solo participantes), publicidad como sistema de reglas (activación diferida), y pago único vía pasarela a definir.

---

## 47. V2 (siguiente iteración, no MVP)

- Más ocasiones temáticas (cumpleaños, despedidas, San Valentín, etc.) reutilizando el sistema de "ocasión" ya construido.
- TTS/IA para generar audio dinámico del narrador (más allá de clips pregrabados).
- App nativa (iOS/Android).
- Funcionalidades adicionales de retención fuera de temporada (a definir).

---

## 48. Funciones Futuras (exploratorias, sin compromiso)

- **Nuevos tipos de evento**: todos regalan a una persona, todos regalan a una pareja (boda), cuota colectiva. Cada uno se implementará como una nueva estrategia de asignación + configuración; los que impliquen manejo de dinero entre participantes requerirán su propio documento (hoy fuera de la app).
- **Nuevas ocasiones**: agregar ocasiones por configuración (incluida una genérica neutra) sin cambios estructurales.
- **Asistente de Regalos con IA**: reevaluar una vez la Encuesta de Pistas esté validada en uso real; podría combinarse con las respuestas de la encuesta como input de mayor calidad para la IA.
- Expansión a otros países de Latam.
- Integraciones opcionales con servicios de terceros para wishlist (sin llegar a marketplace propio).
- Banco de preguntas de la encuesta editable por el organizador (personalización más allá del set estático del MVP).

---

## 49. Riesgos

- **Estacionalidad:** dependencia fuerte de diciembre en el lanzamiento; mitigada parcialmente por el sistema de ocasiones/tipos de evento, pero no resuelta en el MVP.
- **Pasarela de pago en Bolivia:** dependencia externa no resuelta al momento de este documento.
- **Push en iOS:** limitación de plataforma fuera de control del producto; mitigado con email como canal primario.
- **Percepción de "features administrativas insuficientes" frente a Pikkado:** si el mercado espera paridad total de funciones organizativas, ZAIKA puede percibirse como "incompleto" pese a ganar en experiencia.
- **Baja tasa de completitud de la encuesta:** si los participantes no responden la encuesta, se pierde el mecanismo principal de retención (pistas goteadas) y el valor diferencial frente a Pikkado se reduce; conviene monitorear esta métrica desde el lanzamiento (ver sección 45).
- **Conversión Pro dependiente del Buzón temático y el álbum premium:** al ser la propuesta Pro fundamentalmente narrativa y de cierre, conviene medir su atractivo desde el lanzamiento (ver sección 44) y ajustar la oferta si la conversión es baja.

---

## 50. Dependencias

- Pasarela de pago con soporte para Bolivia (**pendiente de definición — bloqueante para el módulo de pagos**).
- Servicio de envío de email transaccional (cierre, notificaciones, magic links).
- Servicio de Web Push.
- Proveedor de Google OAuth (configuración de credenciales/consola de Google Cloud).
- Producción de los audios pregrabados del narrador (contenido, no código).

---

## 51. Costos Potenciales de Servicios Externos

- Hosting/infraestructura y almacenamiento de audio/fotos (bajo en año 1 dado el volumen proyectado ~2.000 eventos) — controlado vía límites por evento (RNF-06).
- Envío de emails transaccionales (volumen bajo-medio, costo marginal).
- Pasarela de pago (comisión por transacción, a confirmar según proveedor elegido).

---

## 52. Estrategia de Escalabilidad

- **Dos ejes desacoplados del diseño:** `tipo_evento` (mecánica, mediante estrategias de asignación) y `ocasión` (temática, mediante configuración). Nada hardcodeado a "Navidad" ni a la mecánica 1:1, de modo que agregar un tipo de evento o una ocasión no requiera reescritura (RNF-04).
- **Modelo de asignación genérico:** la tabla `Asignación` (emisor→receptor) sirve tanto para el ciclo 1:1 como para mecánicas futuras; la forma se valida en la estrategia, no en el esquema.
- Arquitectura preparada para internacionalización (textos, formatos de fecha/moneda) aunque el copy del MVP esté solo en español.
- Separación clara de datos sensibles (sección 34) permite escalar auditorías de seguridad/compliance a futuro sin rediseño.

---

## 53. Criterios de Aceptación (generales del sistema)

- Ningún rol puede acceder al resultado del sorteo de un participante que no sea él mismo.
- El Buzón y sus derivados (preguntas, respuestas) nunca exponen la identidad del emisor al receptor.
- Reutilizar grupo jamás transfiere datos de sorteo, wishlist, perfil o mensajes de años anteriores.
- La publicidad Free nunca aparece en las pantallas listadas en la sección 29.
- El sistema de tier (Free/Pro) es siempre validado a nivel de intercambio, nunca de usuario global.
- Agregar un tipo de evento o una ocasión nuevos no requiere modificar el modelo de datos central ni los módulos existentes (solo estrategia/configuración).

---

## 54. Historias de Usuario (muestra representativa)

- Como **organizador**, quiero crear un intercambio en menos de un minuto autenticándome con Google, para no perder tiempo antes de invitar a mi grupo.
- Como **participante**, quiero unirme con mi cuenta de Google en un tap, para no tener fricción al registrarme.
- Como **participante**, quiero responder una encuesta corta sobre mis gustos, para que quien me regale reciba pistas útiles con el tiempo.
- Como **participante**, quiero recibir pistas de a poco sobre la persona a la que le regalo, para tener un motivo de volver a la app antes del evento.
- Como **participante**, quiero enviar preguntas anónimas a quien le regalo, para conocer mejor sus gustos sin arruinar la sorpresa.
- Como **organizador**, quiero reutilizar el grupo del año pasado (solo los nombres), para no reinvitar a todos manualmente.
- Como **participante**, quiero que el resumen de mi intercambio quede guardado en la app, para poder volver a verlo cuando quiera, no solo en un email que puedo perder.

---

## 55. Backlog Priorizado (alto nivel, orientado a MVP)

1. Autenticación (Google OAuth + magic link) + manejo de WebView de WhatsApp (crítico, base de todo lo demás).
2. Motor de sorteo + reglas de exclusión + validación de resolubilidad + aislamiento de datos secretos.
3. Creación de intercambio + invitaciones + registro de participantes.
4. Wishlist + Encuesta de pistas (banco estático) + motor de entrega goteada con cadencia automática.
5. Preguntas anónimas básicas (Free) + reportar.
6. Sistema de tier Free/Pro + integración de pago (bloqueado por definición de pasarela).
7. Buzón temático + audio pregrabado + encuesta ampliada (Pro).
8. Cierre: cadena de finalización, resumen persistente en la app, email de aviso.
9. Álbum de recuerdos (habilitado en fase de revelación; básico y premium).
10. Reutilización de grupo.
11. Sistema de reglas de publicidad (activación diferida).
12. Temas visuales premium.

---

## 56. Roadmap Sugerido

- **Fase 1 (MVP core):** ítems 1–5 del backlog — auth/WebView, sorteo, invitaciones, encuesta/pistas, preguntas básicas. Producto ya usable en Free, incluyendo el diferenciador principal (pistas goteadas).
- **Fase 2 (Monetización):** ítems 6–7 — pago, tier Pro, buzón temático con audio, encuesta ampliada.
- **Fase 3 (Cierre y experiencia):** ítems 8–9 — cierre persistente, álbum en revelación.
- **Fase 4 (Retención):** ítem 10 — reutilización de grupo (debe estar lista antes de la segunda temporada de uso).
- **Fase 5 (Refinamiento comercial):** ítems 11–12 — publicidad activa, temas premium adicionales.

---

## 57. Análisis Crítico

**Partes débiles del concepto:**
- La dependencia casi total del negocio a la temporada de diciembre sigue siendo el mayor riesgo estructural; el sistema de tipos de evento y ocasiones mitiga, no resuelve, este riesgo.
- El pago único por evento, aunque correcto para reducir fricción de decisión, limita el techo de ingresos por usuario recurrente comparado con un modelo de suscripción; es una decisión consciente y razonable, pero condiciona el crecimiento de ingresos a **volumen de eventos**, no a profundización de gasto por cliente.

**Funcionalidades que sobran (para el MVP):**
- Ocasiones o tipos de evento adicionales en el lanzamiento (llegan por configuración más adelante).
- Más de 5 temas visuales premium si el diseño real no logra diferenciarlos claramente entre sí (mejor 3 bien hechos que 5 mediocres).

**Funcionalidades que faltan (para completar el MVP, no como featurazo nuevo):**
- El mecanismo de edición de la lista de participantes importada al reutilizar grupo (ya incluido en el flujo, sección 27, pero se marca aquí como imprescindible, no opcional).
- La validación previa de sorteo irresoluble (secciones 19/39) — sin esto, un caso límite común (exclusiones mal configuradas) puede bloquear por completo el evento del usuario en el peor momento.

**Riesgos frente a Pikkado:**
- Pikkado puede replicar rápidamente cualquier función "administrativa" que ZAIKA agregue, porque ya tiene la base y el equipo para hacerlo a escala. Competir en esa cancha es una carrera perdida de antemano.
- Pikkado podría copiar el concepto de "experiencia" (buzón temático, encuesta de pistas, álbum) si ve tracción en ZAIKA — la ventaja real de ZAIKA no puede ser solo la idea, sino la ejecución y el pulido de la experiencia.

**Qué podría hacer Pikkado mejor que ZAIKA:** volumen, confianza de marca ya instalada, cobertura internacional, soporte multilenguaje ya maduro.

**Qué podría hacer ZAIKA mejor:** profundidad emocional del cierre (álbum en revelación, resumen persistente en la app), un mecanismo de retención simple y verificable (la encuesta con entrega goteada, sin depender de IA ni costos variables), y una identidad de marca más "vívelo" que la propuesta funcional-fría típica de herramientas de organización.

**Verdadera diferenciación:** ZAIKA no gana siendo "Pikkado con más funciones". Gana siendo la opción que la gente recuerda y comenta después del evento — las pistas que fueron llegando de a poco, el buzón que hablaba con voz de Santa, el álbum armado en la revelación. La organización es el costo de entrada; la experiencia progresiva es el producto real.

**Qué NO se debe programar todavía:** app nativa, TTS/IA para audio dinámico, Asistente de Regalos con IA, tipos de evento distintos al amigo secreto (solo se prepara la base), más ocasiones de lanzamiento, marketplace o cualquier forma de venta/logística de regalos, manejo de dinero entre participantes (cuota), gamificación acumulativa entre años, banco de preguntas de encuesta editable por el organizador, y cualquier forma de "modo evento" que intente digitalizar la revelación presencial.

---

*Fin del documento. Listo para servir de base a diseño de arquitectura, modelo de base de datos, y generación de prompts de UI para desarrollo del MVP.*
