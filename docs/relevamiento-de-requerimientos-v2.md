# Requerimientos: Marketplace inmobiliario BBVA México

**Versión:** 1.0 — 23 de septiembre de 2026
**Autor:** Producto y tecnología de Aper
**Base:** relevamiento funcional construido a partir de la validación de un prototipo navegable del producto (BBVA Mi Casa).

---

## 1. Objetivo

Construir una plataforma para la compra de inmuebles en México, con dos objetivos de negocio:

1. **Colocación de préstamos hipotecarios BBVA**, generando leads calificados hacia un asesor hipotecario a partir de una simulación de crédito.
2. **Generación de ingresos por venta de inmuebles**, generando leads hacia el asesor comercial del proveedor de inventario (Pulppo) a partir del descubrimiento de propiedades.

La plataforma no origina el crédito ni tramita la venta: su función es **descubrimiento, simulación y derivación calificada** hacia los canales de atención de BBVA y de Pulppo.

## 2. Alcance

### 2.1 Incluye

- Construcción de la plataforma web que permite a los usuarios descubrir, comparar y simular financiamiento para inmuebles.
- Construcción del backoffice para la administración de contenido, configuración y usuarios de la plataforma.
- Integración con el inventario de propiedades provisto por **Pulppo** (fuente única de verdad del catálogo — la plataforma no da de alta, edita ni da de baja propiedades).
- Integración con los sistemas de atención de **Pulppo** (asesor comercial/inmobiliario, vía WhatsApp) y de **BBVA** (asesor hipotecario, vía Salesforce) — son dos integraciones distintas, con dos proveedores distintos; ver 2.3.

### 2.2 Fuera de alcance

- Procesamiento de pagos, señas, reservas o firma de contratos dentro de la plataforma.
- Originación, scoring o aprobación del crédito hipotecario — la plataforma simula y deriva; la originación ocurre en los sistemas de BBVA, fuera de esta plataforma.
- Alta, edición o baja de propiedades del inventario — es responsabilidad de Pulppo; el backoffice de esta plataforma solo cura/destaca lo que ya existe en ese inventario (ver B.4).
- CRM propio de seguimiento de leads — se delega íntegramente a Pulppo (leads inmobiliarios) y a Salesforce de BBVA (leads hipotecarios).
- Aplicaciones móviles nativas (iOS/Android). Alcance inicial: plataforma web responsiva. *(Pendiente de confirmar con negocio si corresponde una segunda fase nativa.)*
- Contenido generado por otros usuarios (reseñas, preguntas públicas sobre una propiedad, foros).
- Chat en tiempo real propio de la plataforma — el contacto con asesores ocurre en los canales de Pulppo/BBVA (WhatsApp, Salesforce), no en un chat interno.

### 2.3 Dos canales de asesoría distintos

La derivación a un asesor comercial (ficha de propiedad) y a un asesor hipotecario (simulador) **son dos integraciones separadas, con dueños, canales y datos distintos**, y hay que tratarlas así en el diseño técnico:

| | Asesor inmobiliario (Pulppo) | Asesor hipotecario (BBVA) |
|---|---|---|
| Se ofrece desde | Ficha de propiedad (A.4) | Simulador / Mis simulaciones (A.5, A.8) |
| Canal | WhatsApp | Salesforce (formulario/webhook, no WhatsApp) |
| Dato que viaja | Propiedad de interés | Datos de la simulación (ingreso, enganche, plazo, capacidad) |
| Dueño del lead | Pulppo | BBVA |

## 3. Actores

| Actor | Descripción |
|---|---|
| Usuario anónimo | Cualquier visitante sin sesión iniciada. |
| Usuario autenticado | Usuario que inició sesión (email/contraseña o Google). |
| Administrador de contenido y configuración | Usuario de backoffice que gestiona contenido editorial y parametrización de la plataforma. |
| Administrador de seguridad | Usuario de backoffice que gestiona usuarios, roles y permisos del propio backoffice. |

## 4. Requisitos no funcionales

*(Sin requisitos no funcionales explícitos, cualquier equipo de desarrollo tiene que adivinar el nivel de calidad esperado.)*

- **RNF-01 — Seguridad:** contraseñas con hashing (bcrypt/argon2), verificación de email, rate limiting en login, TLS en tránsito y cifrado en reposo para datos personales y financieros. Cumplimiento de la LFPDPPP (aviso de privacidad, consentimiento, derechos ARCO) para todo dato personal recolectado.
- **RNF-02 — Disponibilidad:** la plataforma pública (Parte A) es de cara al cliente BBVA; se espera un SLA de disponibilidad acorde a un canal digital de adquisición (a definir con Infraestructura, sugerido ≥ 99.5%).
- **RNF-03 — Rendimiento:** tiempos de carga y de respuesta de búsqueda/filtrado acordes a un e-commerce/marketplace (a definir umbrales exactos con Infraestructura).
- **RNF-04 — Accesibilidad:** cumplimiento de pautas WCAG 2.1 nivel AA como mínimo, dado que es un canal de un banco regulado.
- **RNF-05 — Compatibilidad:** soporte de los navegadores/dispositivos que defina la política de BBVA para canales digitales; diseño responsivo mobile/desktop (validado en el prototipo).
- **RNF-06 — Auditoría:** todo cambio hecho desde el backoffice (contenido, configuración, usuarios, roles) debe quedar registrado con usuario, fecha/hora y detalle del cambio.
- **RNF-07 — Escalabilidad:** la plataforma debe soportar el volumen de catálogo e inventario que Pulppo provee, sin degradar tiempos de búsqueda (ver A.3).
- **RNF-08 — Internacionalización:** contenido en español (México); moneda en pesos mexicanos (MXN).

## 5. Supuestos y dependencias

- Pulppo expone una API/feed con el inventario de propiedades (incluyendo disponibilidad actualizada) que la plataforma puede consumir.
- Pulppo expone un canal de derivación de leads inmobiliarios (WhatsApp Business API o equivalente).
- BBVA/Salesforce exponen un mecanismo de integración (API o webhook) para la derivación de leads hipotecarios, junto con la información necesaria para que el asesor reciba el contexto de la simulación.
- BBVA provee y mantiene actualizadas las tasas y parámetros vigentes del crédito hipotecario, para configurar el simulador (ver B.5).
- Existe infraestructura/hosting institucional de BBVA (o aprobado por BBVA) para publicar la plataforma y el backoffice.
- El contenido de Academia financiera y FAQ es provisto/aprobado por el equipo de negocio/legal de BBVA.

## 6. Restricciones técnicas

*(Sección distinta de los Requisitos no funcionales (4): ahí se describe QUÉ calidad debe tener la plataforma, sin atarse a una tecnología puntual; acá se dejan asentadas decisiones de arquitectura ya tomadas o propuestas por la organización, que si no se documentan corren el riesgo de perderse antes de llegar a diseño técnico.)*

- **RT-01 — Librería de UI propia (pendiente de definición):** se propuso que el frontend consuma los componentes de una librería de UI propia de Aper, en vez de construir componentes desde cero, para mantener consistencia visual y de código entre productos. Nombre de la librería y alcance exacto (qué componentes cubre, versionado, proceso de contribución) todavía sin definir.
- **RT-02 — Soluciones de terceros para capacidades ya resueltas por la industria:** para funcionalidades donde ya existe una solución de terceros madura y ampliamente adoptada (por ejemplo, mapas interactivos — ver A.3, A.4), usar esa solución en vez de construir una propia. No aplica a la librería de UI (RT-01), que es una decisión de negocio, no una ausencia de alternativas de mercado.

## 7. Glosario

- **Aper:** equipo de producto y tecnología autor de este documento (ver portada).
- **Pulppo:** proveedor externo de inventario de propiedades y de atención comercial/inmobiliaria vía WhatsApp.
- **Backoffice:** panel de administración interno, no expuesto al público, para gestionar contenido, configuración y usuarios de la plataforma.
- **2FA:** autenticación de dos factores.
- **LFPDPPP:** Ley Federal de Protección de Datos Personales en Posesión de los Particulares (México).
- **Lead:** contacto calificado derivado a un asesor (inmobiliario o hipotecario).
- **RBAC:** control de acceso basado en roles (*Role-Based Access Control*).
- **Enganche:** pago inicial de una compra financiada con crédito hipotecario.
- **CAT:** Costo Anual Total, indicador regulatorio mexicano de costo de un crédito.

---

# PARTE A — EXPERIENCIA DEL CLIENTE

## A.1 Actores de esta parte

Usuario anónimo, usuario autenticado (ver sección 3).

## A.2 Página principal (Home)

**Objetivo:** ser la puerta de entrada de la plataforma, orientando hacia búsqueda, simulación y contenido educativo.

- Los usuarios pueden buscar una propiedad directamente desde la página principal, mediante un buscador prominente (por encima del scroll) con selector de tipo de propiedad y campo de texto libre (zona/colonia), acompañado de tabs de contexto (por ejemplo, Compra / Desarrollos / Oportunidades). Al confirmar la búsqueda, navega a Búsqueda y exploración de propiedades (A.3). *Regla de negocio a confirmar: si el tipo/texto ingresado acá debe pre-cargarse como filtro inicial en A.3, o si A.3 se abre sin filtro — quedó sin resolver al construir el prototipo.*
- Los usuarios pueden ver en la página principal un carrusel de propiedades recién publicadas (ordenado por fecha de publicación descendente), un carrusel de zonas destacadas y un carrusel de propiedades destacadas — los tres configurables/curados desde backoffice (B.4).
- Los usuarios pueden acceder desde la página principal a un menú con los accesos relevantes de la plataforma (como mínimo: Propiedades con acceso a búsqueda, Simulador de crédito, Academia financiera, Ayuda) — en desktop como barra de navegación fija en el encabezado, en mobile como menú desplegable (drawer).
- Los usuarios pueden ver banners promocionales en la página principal, configurables desde backoffice (contenido, imagen y destino — B.4).
- Los usuarios pueden explorar por tipo de propiedad (bloque que lleva a la búsqueda, A.3, pre-filtrada por ese tipo), acceder a precios de referencia por zona (A.2.1) y a contenido de FAQ destacado, con acceso al listado completo (A.12).

### A.2.1 Precios de referencia por zona

*(Identificada como necesaria al construir el prototipo: da contexto de mercado antes de buscar.)*

- Los usuarios pueden consultar, por zona, el precio de referencia por m² (mediana), su evolución histórica y una comparación por segmento (tipo de propiedad / cantidad de ambientes), con acceso directo desde ahí a la búsqueda de propiedades de esa zona (A.3). *Regla de negocio: los datos de mercado provienen de una fuente mantenida por el equipo de Data (agregación propia o proveedor externo de datos inmobiliarios) — no son un valor fijo.*

## A.3 Búsqueda y exploración de propiedades

**Objetivo:** permitir encontrar propiedades del inventario de Pulppo mediante listado, filtros, orden y mapa.

- Los usuarios pueden buscar propiedades por texto libre (zona/colonia como mínimo) y filtrar por operación (compra/renta), tipo de propiedad y rango de precio — los filtros se combinan entre sí (AND). El filtrado y paginado debe ocurrir del lado del servidor, no trayendo todo el inventario al cliente (Pulppo debe soportar consulta paginada/filtrada, o el filtrado ocurre sobre una réplica/índice propio, a definir con Infraestructura).
- Los usuarios pueden ordenar los resultados de búsqueda por precio (ascendente/descendente), más recientes o relevancia.
- Los usuarios pueden ver los resultados en un mapa, con un pin por propiedad sincronizado con los filtros aplicados, alternando entre vista de listado y de mapa sin perderlos; en la vista de mapa, un carrusel de las cards de resultados (vertical en desktop, horizontal en mobile).
- Cada card de resultado muestra foto principal, precio, zona, tipo, ambientes y m², con accesos directos para marcarla favorita (usuario autenticado, ver A.8) y para simular crédito para esa propiedad puntual (ver A.5), sin tener que entrar a la ficha. *Regla de negocio — badge "En tu rango": si el usuario autenticado tiene al menos una simulación guardada, la card muestra un indicador cuando el precio de la propiedad está dentro de su capacidad simulada; si no tiene ninguna simulación guardada, el indicador no se muestra.*

## A.4 Ficha de detalle de propiedad

**Objetivo:** dar toda la información de una propiedad puntual y las acciones para avanzar sobre ella.

- Los usuarios pueden ver el detalle completo de una propiedad: precio, precio por m², datos del desarrollador (si aplica), descripción, amenities, galería de fotos con múltiples imágenes navegable, planos (cuando la propiedad los tenga cargados en el inventario de Pulppo), recorrido 360° (cuando la propiedad lo tenga cargado), video (cuando esté disponible), ubicación en un mini-mapa, y el badge "En tu rango" (misma regla de A.3). *Regla de negocio: los filtros de búsqueda "tiene recorrido 360° / tiene video / tiene planos" (A.3) deben corresponderse con el contenido real de la ficha — si el catálogo marca una propiedad con alguno de estos atributos, la ficha debe mostrar ese contenido.*
- Los usuarios autenticados pueden guardar una propiedad como favorita desde su ficha (misma mecánica de guardar/quitar que desde la card de resultados, A.3 — ver A.8).
- Los usuarios pueden simular un crédito hipotecario para esta propiedad puntual desde su ficha, con acceso directo al simulador ya vinculado a ella (A.5).
- Los usuarios autenticados pueden contactar a un asesor inmobiliario sobre esta propiedad: el botón deriva al asesor comercial de Pulppo vía WhatsApp (ver 2.3), con la propiedad de referencia identificada en el mensaje; si no hay sesión, se solicita login y, al completarlo, se ejecuta la derivación; queda un registro del contacto en el historial de la cuenta (A.11).

## A.5 Simulador de crédito hipotecario

**Objetivo:** estimar capacidad de compra y cuota mensual de un crédito hipotecario a tasa fija, sin requerir cuenta, y derivar a un asesor hipotecario cuando el usuario quiere avanzar.

- Los usuarios, sin necesidad de sesión, pueden simular cuánto pueden pagar a partir de ingreso mensual, enganche disponible y plazo (10/15/20/25 años, parametrizable desde backoffice — B.5), y de si son cliente BBVA (tasa preferencial). El resultado incluye capacidad de compra estimada, cuota mensual, % de la cuota sobre el ingreso y comparación de cuota por plazo. El simulador no pide ningún dato personal identificable para calcular. *Regla de negocio (parametrizable desde B.5): cuota máxima = ingreso × porcentaje máximo definido (ej. 30%); crédito máximo = amortización estándar a tasa fija sobre esa cuota y el plazo elegido; capacidad de compra = crédito máximo + enganche.*
- Cuando el usuario llega desde una propiedad puntual (A.4), el simulador calcula el enganche que necesita para esa propiedad específica en vez de pedirlo: enganche = precio de la propiedad − crédito máximo, según el ingreso y plazo ingresados. Si el crédito máximo ya cubre el precio de la propiedad sin necesitar enganche, se informa positivamente en vez de mostrar un valor en cero sin contexto.
- Los usuarios ven un aviso cuando la cuota resultante supera el porcentaje máximo de ingreso definido en B.5.
- Los usuarios autenticados pueden guardar su simulación; si no hay sesión, se solicita login y, al completarlo, la simulación se guarda automáticamente con los datos ya ingresados, sin pedirlos de nuevo. Se guarda: ingreso, enganche, plazo, capacidad de compra, cuota, tasa aplicada, fecha, y la propiedad de referencia si la hubiera.
- Los usuarios pueden acceder a un asesor hipotecario de BBVA desde su simulación: el botón deriva vía Salesforce (no WhatsApp — ver 2.3), enviando el contexto de la simulación (ingreso, enganche, plazo, capacidad, propiedad de referencia si existe).
- Los usuarios pueden ver propiedades del catálogo compatibles con su simulación, tanto desde el resultado de la simulación como desde su propia sección (A.9, Oportunidades).

## A.6 Registro y autenticación

**Objetivo:** identificar al usuario para las funciones que dependen de cuenta (guardar simulación, favoritos, mensajes, notificaciones, Academia).

- Los usuarios pueden registrarse con correo y contraseña: alta con email, nombre, contraseña y confirmación de contraseña; verificación de email antes de habilitar acciones sensibles (guardar simulación, contactar asesores); un email ya registrado no puede volver a registrarse.
- Los usuarios pueden iniciar sesión con correo/contraseña (validado contra el hash almacenado) o con Google (OAuth 2.0 / OpenID Connect).
- Los usuarios pueden restablecer su contraseña por email, con un enlace de un solo uso y expiración corta.
- El simulador, la búsqueda, el mapa y la ficha de propiedad son de acceso público; guardar simulación, marcar favorito, contactar asesores, y ver Perfil/Favoritos/Mis simulaciones/Notificaciones/Academia requieren sesión. Al pedirse login desde cualquiera de esas acciones, tras autenticarse exitosamente la plataforma vuelve a la pantalla de origen y ejecuta la acción pendiente automáticamente.

## A.7 Menú y navegación de cuenta (side sheet)

*(Es un mecanismo más grande que un simple dropdown: un panel lateral compartido por toda el área de cuenta, con una variante adicional en desktop. Se documenta acá porque de esto depende cómo se navega a Perfil, Favoritos, Configuración y todo lo demás.)*

- Los usuarios pueden abrir un panel lateral (side sheet) desde el avatar del encabezado (igual en mobile y desktop), como una superposición sobre la pantalla actual, con fondo oscurecido y cierre al tocar afuera o el botón de cerrar. El encabezado del panel muestra avatar, nombre de la cuenta y email, con acceso directo a Perfil; el avatar muestra un indicador visual cuando hay notificaciones sin leer (A.10). El panel agrupa los accesos en 3 bloques: **(1)** Perfil, Favoritos, Simulaciones, Mensajes, Notificaciones, Academia BBVA, Oportunidades, Ayuda y preguntas frecuentes, Precios por zona; **(2)** Simula tu crédito, Configuración; **(3)** Cerrar sesión. *Regla de negocio a confirmar: en el prototipo, el panel en mobile no incluye el acceso a "Oportunidades" (sí está en la variante de desktop) — es una inconsistencia detectada al revisarlo, no una decisión de diseño; hay que resolver si se agrega a mobile o se saca de desktop.*
- En desktop, las pantallas de Perfil, Favoritos, Simulaciones, Mensajes, Notificaciones, Academia, Oportunidades, Ayuda, Precios por zona y Configuración muestran, además del side sheet, una barra de navegación fija a la izquierda con los mismos accesos (salvo la diferencia de "Oportunidades" ya señalada), sin necesidad de abrir ni cerrar nada. En mobile, esas mismas pantallas muestran solo un título de pantalla y dependen del side sheet para cambiar de sección.

## A.8 Perfil, favoritos, simulaciones guardadas y configuración de cuenta

- Los usuarios autenticados pueden ver y editar sus datos personales: nombre completo y teléfono (opcional), editables de forma independiente con edición inline (se confirma al presionar Enter o al salir del campo), y foto de perfil reemplazable. El email de la cuenta se muestra pero no es editable directamente — si todavía no fue verificado, se muestra un aviso persistente con dos acciones (reenviar el enlace de confirmación, o abrir el correo y confirmar), y el usuario puede seguir usando la plataforma mientras tanto salvo las acciones que requieren email verificado (A.6).
- Los usuarios autenticados pueden marcar y desmarcar propiedades como favoritas desde cualquier card (A.3) o ficha (A.4), con el mismo estado reflejado en ambos lugares, y verlas listadas en una sección "Favoritos" con estado vacío orientativo si no hay ninguna. Si una propiedad favorita deja de existir en el inventario de Pulppo, se refleja como no disponible en vez de eliminarse silenciosamente del listado.
- Los usuarios autenticados pueden ver el historial de sus simulaciones guardadas (ver A.5 para el detalle de qué se guarda), con estado vacío orientativo si no hay ninguna, y vincular una propiedad ya vista a una simulación guardada — la vinculación solo la asocia como referencia, no recalcula la simulación.
- Los usuarios autenticados pueden configurar sus preferencias de notificaciones (push, emails de novedades, alertas de precio — cada una activable/desactivable de forma independiente) y gestionar la privacidad de su cuenta: acceso a términos y condiciones y a la política de privacidad (contenido gestionado desde backoffice, B.3), y una acción de eliminar cuenta, señalada explícitamente como irreversible. *Regla de negocio a confirmar: hoy la preferencia "alertas de precio" existe como control, pero el evento que la dispararía (cambio real de precio de un favorito) no está automatizado (ver A.10 y A.13). El flujo de eliminar cuenta debe cumplir con el derecho de cancelación/oposición de la LFPDPPP (RNF-01): confirmación explícita antes de ejecutar, y borrado o anonimización de los datos asociados a la cuenta (favoritos, simulaciones, mensajes, notificaciones) dentro de los plazos que defina la política de privacidad.*

## A.9 Oportunidades

*(Amerita ser tratada como sección propia, con su propia regla de negocio y su propio estado vacío, más allá del resultado de la simulación en sí.)*

- Los usuarios autenticados con al menos una simulación guardada pueden ver propiedades del catálogo que están dentro de su capacidad, sin tener que cruzar precio y capacidad manualmente. *Regla de negocio v1: propiedad "oportunidad" = precio de la propiedad ≤ capacidad de compra de la última simulación guardada por esa cuenta.* Si el usuario no tiene ninguna simulación guardada, se muestra un estado vacío con acceso directo al simulador. *(Pendiente: evaluar con Data si el criterio debe incorporar más variables — zona, tipo de propiedad — en una iteración futura.)*

## A.10 Notificaciones

*(Construir el prototipo obligó a definir tipos y reglas de enrutamiento concretas — se documentan acá.)*

- Los usuarios autenticados reciben notificaciones de eventos relevantes (tipos mínimos: bienvenida a la plataforma, simulación guardada, nueva oportunidad que matchea — A.9, actualización de una propiedad favorita), cada una con estado leída/no leída y contador de no leídas. Al tocar una notificación, se marca como leída y navega directo a la propiedad asociada (A.4) si la tiene, o a la sección relacionada según su tipo (por ejemplo, una notificación de bienvenida navega a Inicio, una de "nueva oportunidad" navega a la propiedad puntual que hizo match). Cada tipo de notificación debe respetar la preferencia que el usuario configuró en Configuración de cuenta (A.8) — por ejemplo, "actualización de una propiedad favorita" depende de la preferencia "Alertas de precio".

## A.11 Contacto con asesores

*(Consolida en un solo lugar, para trazabilidad, los dos flujos de derivación descritos en 2.3, A.4 y A.5.)*

- Los usuarios autenticados pueden ver el historial de sus contactos con asesores: un registro por cada contacto iniciado (inmobiliario vía Pulppo/WhatsApp, hipotecario vía BBVA/Salesforce), con fecha y la propiedad o simulación de origen. Un mismo contacto (misma propiedad/simulación + mismo tipo de asesor) no se duplica si ya existía.

## A.12 Academia financiera y preguntas frecuentes

- Los usuarios autenticados pueden acceder a contenido educativo sobre crédito hipotecario, organizado en módulos cortos (teoría + mini-evaluación) sobre: qué es un crédito hipotecario y el CAT, enganche y plazos, tipos de hipoteca, requisitos y documentación, gastos adicionales y beneficios fiscales, y mercado y momento de decisión. El contenido incluye multimedia (imágenes/video), administrado desde backoffice (B.3), con progreso guardado por cuenta. *Regla de negocio — alcance del contenido: Academia financiera se acota a contenido de crédito e hipotecas; no incluye pasos de cuenta/perfil (crear cuenta, completar perfil) ni checklists de acciones de producto (simular, explorar, contactar) — esos ya se resuelven en sus propias secciones (A.5–A.11), y mezclarlos con el contenido educativo diluye el propósito de Academia.* *(Pendiente: evaluar si al menos la teoría debería ser accesible sin sesión, igual que el simulador (A.6), para bajar la fricción de entrada.)*
- Los usuarios pueden acceder a preguntas frecuentes, organizadas por categoría con detalle por pregunta, con contenido administrado desde backoffice (B.3). No requiere sesión.

## A.13 Elementos pendientes de definición de negocio

*(Estos elementos ya están diseñados a nivel de interfaz, pero su función real todavía no fue definida por negocio. Se documentan acá para que ningún equipo los dé por resueltos.)*

- Tres accesos rápidos en la página principal: "Conocer emprendimientos e inversiones", "Ser cliente BBVA para obtener una mejor tasa" y "Simular crédito para reformas" — falta definir si son un catálogo aparte, contenido informativo, o un flujo con su propia lógica.
- Un acceso "Contactar asesor" general en el encabezado, con las opciones "asesor hipotecario" / "asesor inmobiliario" — falta definir si reutiliza los canales de A.11 o necesita un flujo propio para consultas sin propiedad ni simulación asociada.

---

# PARTE B — EXPERIENCIA DEL ADMINISTRADOR DE BACK OFFICE

## B.1 Actores y modelo de permisos

Dos roles base:

- **Administrador de contenido y configuración:** gestiona contenido editorial y parametrización de producto (B.3, B.4, B.5).
- **Administrador de seguridad:** gestiona usuarios, roles y permisos del propio backoffice (B.6).

**RN-BO-01.** El backoffice debe implementarse con control de acceso basado en roles (RBAC): cada acción descrita en esta Parte B debe estar asociada a un rol, y un usuario de backoffice solo puede ejecutar las acciones de los roles que tiene asignados. *(Se explicita como un modelo de permisos formal, no una convención informal, para que Seguridad pueda auditarlo.)*

## B.2 Autenticación y seguridad del backoffice

- Los administradores de backoffice pueden iniciar sesión con correo, contraseña y 2FA — obligatorio para todo usuario de backoffice, a diferencia del acceso de clientes (A.6). Pueden solicitar alta y baja de su propio mecanismo de 2FA, solicitar recuperación de su contraseña (con las mismas garantías de RNF-01: token de un solo uso, expiración corta) y modificar sus propios datos personales.

## B.3 Gestión de contenido

- Los administradores de contenido pueden crear, editar y eliminar artículos de Academia financiera (CRUD completo, incluyendo contenido multimedia — A.12), con cambios auditados (RNF-06).
- Los administradores de contenido pueden crear, editar y eliminar preguntas frecuentes (CRUD completo, con categorización) para mantener actualizada la sección de Ayuda (A.12).
- Los administradores de contenido pueden gestionar el contenido estático legal de la plataforma (términos y condiciones, política de privacidad, y el aviso legal del simulador de crédito), con edición independiente de los tres textos y versionado/fecha de última modificación visible. Los tres son accesibles por el usuario desde Configuración de cuenta (A.8).

## B.4 Configuración de la página principal

- Los administradores de contenido pueden configurar el menú de navegación de la plataforma (alta/edición/orden/baja de ítems — A.2).
- Los administradores de contenido pueden configurar la disposición de los distintos carruseles de la página principal (recién publicadas, zonas destacadas, propiedades destacadas, etc.): orden y visibilidad de cada bloque (A.2).
- Los administradores de contenido pueden configurar los banners promocionales de la página principal: alta, edición y baja, con imagen, texto, destino y vigencia (A.2).
- Los administradores de contenido pueden destacar propiedades específicas del inventario de Pulppo en el carrusel correspondiente, seleccionando entre las propiedades existentes en ese inventario (no creación de propiedades — ver 2.2, fuera de alcance).

## B.5 Configuración del simulador de crédito

- Los administradores de contenido pueden configurar los parámetros del simulador de crédito hipotecario para que reflejen las condiciones vigentes de BBVA: tasa anual pública, tasa anual preferencial (cliente BBVA), plazos disponibles, y porcentaje máximo de ingreso destinado a cuota (regla de A.5) — con cambios auditados (RNF-06) y fecha de vigencia.

## B.6 Gestión de usuarios y roles de backoffice

- Los administradores de seguridad pueden dar de alta, editar y dar de baja usuarios de backoffice, con auditoría (RNF-06).
- Los administradores de seguridad pueden dar de alta, editar y dar de baja roles de backoffice, y asignárselos a los usuarios, para mantener el modelo de permisos (B.1) actualizado, con auditoría (RNF-06).
