# Requerimientos: Marketplace inmobiliario BBVA México

**Versión:** Draft 2.0 — 23 de septiembre de 2026
**Versión anterior:** Draft 1.1 — 16 de septiembre de 2026
**Autor:** Producto y tecnología de Aper
**Nota de esta versión:** amplía y corrige el Draft 1.1 a partir de la validación funcional hecha sobre un prototipo navegable del producto (BBVA Mi Casa). Donde el Draft 1.1 quedaba ambiguo o incompleto, esta versión resuelve la ambigüedad con la decisión que el prototipo validó; donde algo del Draft 1.1 no se sostuvo al construirlo, se corrige explícitamente y se aclara por qué.

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

### 2.3 Corrección importante respecto del Draft 1.1: dos canales de asesoría distintos

El Draft 1.1 mencionaba un solo tipo de derivación ("asesor comercial de Pulppo vía WhatsApp" en la ficha de propiedad, y "asesor hipotecario de BBVA vía Salesforce" en el simulador) pero no lo remarcaba como una distinción de diseño. Al construir el prototipo confirmamos que **son dos integraciones separadas, con dueños, canales y datos distintos**, y hay que tratarlas así en el diseño técnico:

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

*(Sección nueva respecto del Draft 1.1 — no existía ningún requisito no funcional; sin esto, cualquier equipo de desarrollo tiene que adivinar el nivel de calidad esperado.)*

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

## 6. Glosario

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

**HU-C-01.** Como usuario, quiero buscar una propiedad directamente desde la página principal, sin tener que navegar antes a otra pantalla, para llegar más rápido a resultados relevantes.
- Criterios de aceptación:
  - La página principal muestra, de forma prominente y por encima del scroll (*above the fold*), un buscador con selector de tipo de propiedad y campo de texto libre (zona/colonia), acompañado de tabs de contexto (por ejemplo, Compra / Desarrollos / Oportunidades) para orientar el tipo de búsqueda antes de ejecutarla.
  - Al confirmar la búsqueda (botón "Buscar propiedad"), navega a Búsqueda y exploración de propiedades (A.3).
  - Regla de negocio a confirmar: definir si el tipo y el texto ingresados acá deben pre-cargarse como filtro inicial en A.3, o si A.3 se abre sin ningún filtro aplicado — es un punto que quedó sin resolver al construir el prototipo (ver Anexo de trazabilidad).

**HU-C-02.** Como usuario, quiero ver en la página principal propiedades recién publicadas y de zonas destacadas, para descubrir opciones sin tener que buscar activamente.
- Criterios de aceptación:
  - Se muestra un carrusel de propiedades recién publicadas, ordenado por fecha de publicación descendente.
  - Se muestra un carrusel de zonas destacadas, configurable desde backoffice (ver B.4).
  - Se muestra un carrusel de propiedades destacadas, curado manualmente desde backoffice (ver B.4).

**HU-C-03.** Como usuario, quiero acceder desde la página principal a un menú desplegable con los accesos relevantes de la plataforma, para navegar sin perder contexto.
- Criterios de aceptación:
  - El menú incluye, como mínimo: Propiedades (con acceso a búsqueda), Simulador de crédito, Academia financiera, Ayuda.
  - En desktop, el menú se muestra como barra de navegación fija en el encabezado; en mobile, como menú desplegable (drawer).

**HU-C-04.** Como usuario, quiero ver banners promocionales en la página principal, para enterarme de campañas o contenido priorizado por negocio.
- Criterios de aceptación:
  - Los banners son configurables desde backoffice (contenido, imagen y destino), ver B.4.

**HU-C-05.** Como usuario, quiero explorar la plataforma por tipo de propiedad y por zona/mercado, para orientar mi búsqueda por criterios distintos al texto libre.
- Criterios de aceptación:
  - Existe un bloque de "Explorar por tipo de propiedad" (departamento, casa, oficina, local comercial, etc.) que lleva a la búsqueda (A.3) pre-filtrada por ese tipo.
  - Existe una sección de precios de referencia por zona (precio por m², comparación por segmento), independiente del listado de propiedades — ver A.2.1.
  - Existe contenido de FAQ destacado, con acceso al listado completo (A.12).

### A.2.1 Precios de referencia por zona

*(Funcionalidad adicional, no mencionada en el Draft 1.1, identificada como necesaria durante la construcción del prototipo: da contexto de mercado antes de buscar.)*

**HU-C-06.** Como usuario, quiero consultar el precio de referencia por m² y la evolución de precios de una zona, para decidir dónde buscar antes de filtrar propiedades puntuales.
- Criterios de aceptación:
  - Por zona: precio por m² (mediana), evolución histórica, comparación por segmento (tipo de propiedad / cantidad de ambientes).
  - Desde la ficha de una zona, acceso directo a la búsqueda de propiedades de esa zona (A.3).
  - Regla de negocio: los datos de mercado provienen de una fuente mantenida por el equipo de Data (agregación propia o proveedor externo de datos inmobiliarios) — no son un valor fijo.

## A.3 Búsqueda y exploración de propiedades

**Objetivo:** permitir encontrar propiedades del inventario de Pulppo mediante listado, filtros, orden y mapa.

**HU-C-07.** Como usuario, quiero buscar propiedades por texto, tipo, precio y ubicación, para encontrar rápido las que me interesan.
- Criterios de aceptación:
  - Buscador de texto libre (zona/colonia como mínimo).
  - Filtros preestablecidos: operación (compra/renta), tipo de propiedad, rango de precio.
  - Los filtros se combinan entre sí (AND).
  - Filtrado y paginado del lado del servidor (no traer todo el inventario al cliente) — Pulppo debe soportar consulta paginada/filtrada, o el filtrado ocurre sobre una réplica/índice propio (a definir con Infraestructura).

**HU-C-08.** Como usuario, quiero ordenar los resultados de búsqueda, para priorizar según lo que más me importa.
- Criterios de aceptación:
  - Modos de ordenamiento preestablecidos: precio (ascendente/descendente), más recientes, relevancia.

**HU-C-09.** Como usuario, quiero ver los resultados de búsqueda en un mapa, para entender su ubicación relativa.
- Criterios de aceptación:
  - Vista de mapa con un pin por propiedad, sincronizada con los filtros aplicados.
  - Alternar entre vista de listado y vista de mapa sin perder los filtros.
  - En la vista de mapa, un carrusel de las cards de resultados (vertical en desktop, horizontal en mobile).

**HU-C-10.** Como usuario, quiero ver en cada card de resultado la información clave de la propiedad y accesos directos, para decidir si entrar al detalle.
- Criterios de aceptación:
  - La card muestra: foto principal, precio, zona, tipo, ambientes, m².
  - La card incluye un acceso directo para marcarla favorita (usuario autenticado, ver A.8) y para simular crédito para esa propiedad puntual (ver A.5), sin tener que entrar a la ficha.
  - Regla de negocio — badge "En tu rango": si el usuario autenticado tiene al menos una simulación guardada, la card muestra un indicador cuando el precio de la propiedad está dentro de su capacidad simulada; si no tiene ninguna simulación guardada, el indicador no se muestra.

## A.4 Ficha de detalle de propiedad

**Objetivo:** dar toda la información de una propiedad puntual y las acciones para avanzar sobre ella.

**HU-C-11.** Como usuario, quiero ver el detalle completo de una propiedad, para evaluarla en profundidad.
- Criterios de aceptación:
  - Se muestra precio, precio por m², datos del desarrollador (si aplica), descripción, amenities.
  - Galería de fotos y planos.
  - Recorrido 360°, cuando la propiedad lo tenga cargado en el inventario de Pulppo.
  - Ubicación en un mini-mapa.
  - Badge "En tu rango", con la misma regla de A.3.

**HU-C-12.** Como usuario autenticado, quiero guardar una propiedad como favorita desde su ficha, para encontrarla después sin volver a buscarla.
- Criterios de aceptación: ver A.8 (Favoritos) — la mecánica de guardar/quitar es la misma desde la card de resultados (A.3) y desde la ficha.

**HU-C-13.** Como usuario, quiero simular un crédito hipotecario para esta propiedad puntual, para saber si me alcanza y cuánto enganche necesito.
- Criterios de aceptación: ver A.5 — la ficha ofrece el acceso directo al simulador ya vinculado a esta propiedad.

**HU-C-14.** Como usuario autenticado, quiero contactar a un asesor inmobiliario sobre esta propiedad, para resolver dudas o avanzar en el proceso de compra.
- Criterios de aceptación:
  - El botón deriva al asesor comercial de **Pulppo vía WhatsApp** (ver 2.3), con la propiedad de referencia identificada en el mensaje.
  - Requiere sesión iniciada; si no hay sesión, se solicita login y, al completarlo, se ejecuta la derivación.
  - Queda un registro del contacto en el historial de la cuenta (ver A.11).

## A.5 Simulador de crédito hipotecario

**Objetivo:** estimar capacidad de compra y cuota mensual de un crédito hipotecario a tasa fija, sin requerir cuenta, y derivar a un asesor hipotecario cuando el usuario quiere avanzar.

**HU-C-15.** Como usuario (sin necesidad de sesión), quiero simular cuánto puedo pagar dado mi ingreso, enganche y plazo, para saber qué propiedades están a mi alcance.
- Criterios de aceptación:
  - Entradas: ingreso mensual, enganche disponible, plazo (10/15/20/25 años — parametrizable desde backoffice, ver B.5), condición de cliente BBVA (tasa preferencial).
  - Salida: capacidad de compra estimada, cuota mensual, % de la cuota sobre el ingreso, comparación de cuota por plazo.
  - El simulador es de acceso público: no requiere sesión ni pide ningún dato personal identificable para calcular.
  - Regla de negocio (parametrizable desde backoffice, ver B.5): cuota máxima = ingreso × porcentaje máximo definido (ej. 30%); crédito máximo = amortización estándar a tasa fija sobre esa cuota y el plazo elegido; capacidad de compra = crédito máximo + enganche.

**HU-C-16.** Como usuario que llega desde una propiedad puntual (A.4), quiero que el simulador calcule el enganche que necesito para esa propiedad específica, en vez de tener que adivinarlo, para saber de entrada cuánto me falta ahorrar.
- Criterios de aceptación:
  - Al simular con una propiedad de referencia, el campo de enganche no se pide: se calcula como `precio de la propiedad − crédito máximo` según el ingreso y plazo ingresados.
  - Si el crédito máximo ya cubre el precio de la propiedad sin necesitar enganche, se informa positivamente en vez de mostrar un valor en cero sin contexto.

**HU-C-17.** Como usuario, quiero ver un aviso si la cuota resultante es una porción alta de mi ingreso, para tomar una decisión informada.
- Criterios de aceptación:
  - Se muestra un aviso cuando la cuota supera el porcentaje máximo definido en B.5.

**HU-C-18.** Como usuario autenticado, quiero guardar mi simulación, para retomarla después sin tener que rehacerla.
- Criterios de aceptación:
  - Guardar requiere sesión; si el usuario no está autenticado, se le solicita login y, al completarlo, la simulación se guarda automáticamente con los datos ya ingresados (sin pedirlos de nuevo).
  - Lo guardado: ingreso, enganche, plazo, capacidad de compra, cuota, tasa aplicada, fecha, y la propiedad de referencia si la hubiera.

**HU-C-19.** Como usuario, quiero acceder a un asesor hipotecario de BBVA desde mi simulación, para avanzar en el proceso de crédito.
- Criterios de aceptación:
  - El botón deriva al asesor hipotecario de BBVA **vía Salesforce** (no WhatsApp — ver 2.3), enviando el contexto de la simulación (ingreso, enganche, plazo, capacidad, propiedad de referencia si existe).

**HU-C-20.** Como usuario, quiero ver propiedades del catálogo compatibles con mi simulación, para pasar de "cuánto puedo pagar" a "qué puedo comprar".
- Criterios de aceptación: ver A.9 (Oportunidades) — esta es la misma funcionalidad, accesible tanto desde el resultado de la simulación como desde su propia sección.

## A.6 Registro y autenticación

**Objetivo:** identificar al usuario para las funciones que dependen de cuenta (guardar simulación, favoritos, mensajes, notificaciones, Academia).

**HU-C-21.** Como usuario, quiero registrarme con correo y contraseña, para crear una cuenta.
- Criterios de aceptación:
  - Alta con email, nombre, contraseña y confirmación de contraseña.
  - Verificación de email antes de habilitar acciones sensibles (guardar simulación, contactar asesores).
  - Un email ya registrado no puede volver a registrarse.

**HU-C-22.** Como usuario, quiero iniciar sesión con correo/contraseña o con Google, para acceder a mi cuenta.
- Criterios de aceptación:
  - Login con email/contraseña, validado contra el hash almacenado.
  - Login social con Google (OAuth 2.0 / OpenID Connect).

**HU-C-23.** Como usuario, quiero poder restablecer mi contraseña si la olvido, para recuperar el acceso a mi cuenta.
- Criterios de aceptación:
  - Solicitud de restablecimiento por email, con enlace de un solo uso y expiración corta.

**HU-C-24.** Como usuario, quiero que cualquier acción que requiera cuenta me pida iniciar sesión en el momento (no antes), y que al loguearme se complete lo que quería hacer, para no perder lo que ya había cargado.
- Criterios de aceptación:
  - El simulador, la búsqueda, el mapa y la ficha de propiedad son de acceso público.
  - Guardar simulación, marcar favorito, contactar asesores, ver Perfil/Favoritos/Mis simulaciones/Notificaciones/Academia requieren sesión.
  - Al pedirse login desde cualquiera de esas acciones, tras autenticarse exitosamente la plataforma vuelve a la pantalla de origen y ejecuta la acción pendiente automáticamente.

## A.7 Menú y navegación de cuenta (side sheet)

*(El Draft 1.1 solo mencionaba, dentro de Página principal, "un menú desplegable con accesos relevantes". Al revisar el prototipo en detalle confirmamos que es un mecanismo más grande que un dropdown: un panel lateral compartido por toda el área de cuenta, con una variante adicional en desktop. Se documenta acá porque de esto depende cómo se navega a Perfil, Favoritos, Configuración y todo lo demás.)*

**HU-C-25.** Como usuario, quiero abrir un panel lateral (side sheet) desde el ícono de mi cuenta en el encabezado, para navegar a cualquier sección de mi cuenta sin perder el contexto de la pantalla en la que estoy.
- Criterios de aceptación:
  - El panel se abre tocando el avatar del encabezado (igual en mobile y desktop), como una superposición sobre la pantalla actual, con fondo oscurecido y cierre al tocar afuera o el botón de cerrar.
  - El encabezado del panel muestra avatar, nombre de la cuenta (o un texto genérico si el nombre aún no cargó) y email, con acceso directo a Perfil.
  - El avatar del encabezado muestra un indicador visual cuando hay notificaciones sin leer (ver A.10).
  - El panel agrupa los accesos en 3 bloques: **(1)** Perfil, Favoritos, Simulaciones, Mensajes, Notificaciones, Academia BBVA, Oportunidades, Ayuda y preguntas frecuentes, Precios por zona; **(2)** Simula tu crédito, Configuración; **(3)** Cerrar sesión.
  - Regla de negocio a confirmar: en el prototipo, el panel en mobile no incluye el acceso a "Oportunidades" (sí está en la variante de desktop, ver HU-C-26) — es una inconsistencia detectada al revisarlo, no una decisión de diseño; hay que resolver si se agrega a mobile o se saca de desktop.

**HU-C-26.** Como usuario en desktop, quiero ver un panel de navegación de cuenta fijo al costado mientras estoy dentro de cualquier sección de mi cuenta, para cambiar de sección sin tener que reabrir un menú cada vez.
- Criterios de aceptación:
  - En desktop, las pantallas de Perfil, Favoritos, Simulaciones, Mensajes, Notificaciones, Academia, Oportunidades, Ayuda, Precios por zona y Configuración muestran, además del side sheet de la HU-C-25, una barra de navegación fija a la izquierda con los mismos accesos (salvo la diferencia de "Oportunidades" ya señalada), sin necesidad de abrir ni cerrar nada.
  - En mobile, esas mismas pantallas muestran solo un título de pantalla y dependen del side sheet (HU-C-25) para cambiar de sección.

## A.8 Perfil, favoritos, simulaciones guardadas y configuración de cuenta

**HU-C-27.** Como usuario autenticado, quiero ver y editar mis datos personales, para mantenerlos actualizados.
- Criterios de aceptación:
  - Editables de forma independiente, campo por campo: nombre completo y teléfono (opcional) — edición inline, se confirma al presionar Enter o al salir del campo.
  - Foto de perfil (avatar): el usuario puede reemplazarla.
  - El email de la cuenta se muestra pero no es editable directamente desde Perfil — cambiarlo requiere el flujo de verificación descrito abajo, no una edición libre.
  - Si el email de la cuenta todavía no fue verificado, se muestra un aviso persistente con dos acciones: reenviar el enlace de confirmación, o abrir el correo y confirmar. El usuario puede seguir usando la plataforma mientras tanto, salvo las acciones que requieren email verificado (ver HU-C-21).
  - *(Resuelve el punto "(verificar)" del Draft 1.1: los campos editables de Perfil son nombre, teléfono y avatar — el email queda fuera de la edición libre por su propio flujo de verificación.)*

**HU-C-28.** Como usuario autenticado, quiero marcar y desmarcar propiedades como favoritas desde cualquier card o ficha, y verlas todas juntas en una sección, para encontrarlas rápido después.
- Criterios de aceptación:
  - El ícono de favorito está disponible tanto en la card de resultados (A.3) como en la ficha de detalle (A.4), y refleja el mismo estado en ambos lugares.
  - Sección "Favoritos" con el listado completo y estado vacío orientativo si no hay ninguno.
  - Si una propiedad favorita deja de existir en el inventario de Pulppo, se refleja como no disponible (no se elimina silenciosamente del listado sin avisar).

**HU-C-29.** Como usuario autenticado, quiero ver el historial de mis simulaciones guardadas, para retomarlas o compararlas.
- Criterios de aceptación: ver A.5 (HU-C-18) para el detalle de qué se guarda; esta sección lista todo lo guardado, con estado vacío orientativo si no hay ninguna.

**HU-C-30.** Como usuario autenticado, quiero vincular una propiedad ya vista a una simulación guardada, para dejar registrada la relación entre ambas.
- Criterios de aceptación:
  - Vincular no recalcula la simulación: solo la asocia como referencia.

**HU-C-31.** Como usuario autenticado, quiero configurar mis preferencias de notificaciones y gestionar la privacidad de mi cuenta, para tener control sobre lo que recibo y sobre mis datos.
- Criterios de aceptación:
  - Preferencias de notificación, cada una activable/desactivable de forma independiente: notificaciones push (oportunidades y alertas de propiedades), emails de novedades (resumen periódico de propiedades), alertas de precio (cuando una propiedad favorita, ver A.8, cambia de precio).
  - Regla de negocio a confirmar: hoy la preferencia de "alertas de precio" existe como control, pero el evento que la dispararía (cambio real de precio de un favorito) no está automatizado — ver A.10 (Notificaciones) y A.13 (pendientes).
  - Acceso a los términos y condiciones y a la política de privacidad de la plataforma (contenido gestionado desde backoffice, ver B.3).
  - Acción de eliminar cuenta, señalada explícitamente como irreversible. Al tratarse de datos personales, este flujo debe cumplir con el derecho de cancelación/oposición de la LFPDPPP (RNF-01): confirmación explícita antes de ejecutar, y borrado o anonimización de los datos asociados a la cuenta (favoritos, simulaciones, mensajes, notificaciones) dentro de los plazos que defina la política de privacidad.

## A.9 Oportunidades

*(El Draft 1.1 la mencionaba como un bullet suelto dentro del Simulador — "acceder a un conjunto de propiedades compatibles con la simulación realizada". Al construirla confirmamos que amerita ser tratada como sección propia, con su propia regla de negocio y su propio estado vacío.)*

**HU-C-32.** Como usuario autenticado con al menos una simulación guardada, quiero ver propiedades del catálogo que están dentro de mi capacidad, para no tener que cruzar precio y capacidad manualmente.
- Criterios de aceptación:
  - Regla de negocio v1: propiedad "oportunidad" = precio de la propiedad ≤ capacidad de compra de la última simulación guardada por esa cuenta.
  - Si el usuario no tiene ninguna simulación guardada, se muestra un estado vacío con acceso directo al simulador.
  - *(Pendiente: evaluar con Data si el criterio debe incorporar más variables — zona, tipo de propiedad — en una iteración futura.)*

## A.10 Notificaciones

*(El Draft 1.1 solo mencionaba "un panel de notificaciones" sin detalle. Construir el prototipo obligó a definir tipos y reglas de enrutamiento — se documentan acá.)*

**HU-C-33.** Como usuario autenticado, quiero recibir notificaciones de eventos relevantes y que cada una me lleve directo a donde corresponde, para no tener que buscar manualmente qué las generó.
- Criterios de aceptación:
  - Tipos mínimos: bienvenida a la plataforma, simulación guardada, nueva oportunidad que matchea (A.9), actualización de una propiedad favorita.
  - Cada notificación tiene un estado leída/no leída, con contador de no leídas.
  - Regla de enrutamiento: si la notificación tiene una propiedad asociada, navega directo a esa ficha (A.4); si no, navega a la sección relacionada según su tipo (ej. una notificación de bienvenida navega a Inicio, una de "nueva oportunidad" navega a la propiedad puntual que hizo match).
  - Al tocarla, se marca como leída.
  - Cada tipo de notificación debe respetar la preferencia que el usuario configuró en Configuración de cuenta (A.8, HU-C-31) — por ejemplo, "actualización de una propiedad favorita" depende de la preferencia "Alertas de precio".

## A.11 Contacto con asesores

*(Consolida en un solo lugar, para trazabilidad, los dos flujos de derivación descritos en 2.3, A.4 y A.5.)*

**HU-C-34.** Como usuario autenticado, quiero ver el historial de mis contactos con asesores, para saber sobre qué propiedad o simulación hablé y cuándo.
- Criterios de aceptación:
  - Un registro por cada contacto iniciado (inmobiliario vía Pulppo/WhatsApp, hipotecario vía BBVA/Salesforce), con fecha y la propiedad o simulación de origen.
  - Un mismo contacto (misma propiedad/simulación + mismo tipo de asesor) no se duplica si ya existía.

## A.12 Academia financiera y preguntas frecuentes

**HU-C-35.** Como usuario autenticado, quiero acceder a contenido educativo sobre crédito hipotecario, para entender mejor el proceso antes de decidir.
- Criterios de aceptación:
  - Contenido organizado en módulos cortos (teoría + mini-evaluación) sobre: qué es un crédito hipotecario y el CAT, enganche y plazos, tipos de hipoteca, requisitos y documentación, gastos adicionales y beneficios fiscales, mercado y momento de decisión.
  - El contenido incluye multimedia (imágenes/video), administrado desde backoffice (ver B.3).
  - Progreso guardado por cuenta.
  - Regla de negocio — alcance del contenido: Academia financiera se acota a contenido de crédito e hipotecas; no incluye pasos de cuenta/perfil (crear cuenta, completar perfil) ni checklists de acciones de producto (simular, explorar, contactar) — esos ya se resuelven en sus propias secciones (A.5–A.11) y mezclarlos con el contenido educativo diluye el propósito de Academia.
  - *(Pendiente: evaluar si al menos la teoría debería ser accesible sin sesión, igual que el simulador — HU-C-24 —, para bajar la fricción de entrada.)*

**HU-C-36.** Como usuario, quiero acceder a preguntas frecuentes, para resolver dudas sin contactar a un asesor.
- Criterios de aceptación:
  - Listado por categoría, con detalle por pregunta.
  - Contenido administrado desde backoffice (ver B.3). No requiere sesión.

## A.13 Elementos pendientes de definición de negocio

*(Estos elementos ya están diseñados a nivel de interfaz, pero su función real todavía no fue definida por negocio. Se documentan acá para que ningún equipo los dé por resueltos.)*

- Tres accesos rápidos en la página principal: "Conocer emprendimientos e inversiones", "Ser cliente BBVA para obtener una mejor tasa" y "Simular crédito para reformas" — falta definir si son un catálogo aparte, contenido informativo, o un flujo con su propia lógica.
- Un acceso "Contactar asesor" general en el encabezado, con las opciones "asesor hipotecario" / "asesor inmobiliario" — falta definir si reutiliza los canales de A.11 o necesita un flujo propio para consultas sin propiedad ni simulación asociada.

---

# PARTE B — EXPERIENCIA DEL ADMINISTRADOR DE BACK OFFICE

## B.1 Actores y modelo de permisos

Dos roles base, según el Draft 1.1:

- **Administrador de contenido y configuración:** gestiona contenido editorial y parametrización de producto (B.3, B.4, B.5).
- **Administrador de seguridad:** gestiona usuarios, roles y permisos del propio backoffice (B.6).

**RN-BO-01.** El backoffice debe implementarse con control de acceso basado en roles (RBAC): cada acción descrita en esta Parte B debe estar asociada a un rol, y un usuario de backoffice solo puede ejecutar las acciones de los roles que tiene asignados. *(El Draft 1.1 ya distinguía dos roles; esta versión lo explicita como un modelo de permisos, no una convención informal, para que Seguridad pueda auditarlo.)*

## B.2 Autenticación y seguridad del backoffice

**HU-BO-01.** Como administrador de backoffice, quiero iniciar sesión con correo, contraseña y 2FA, para que el acceso al backoffice tenga una barrera adicional a la del sitio público.
- Criterios de aceptación:
  - El 2FA es obligatorio para todo usuario de backoffice (no opcional, a diferencia del acceso de clientes en A.6).
  - El administrador puede solicitar alta y baja de su propio mecanismo de 2FA.
  - El administrador puede solicitar recuperación de su contraseña, con las mismas garantías de RNF-01 (token de un solo uso, expiración corta).
  - El administrador puede modificar sus propios datos personales.

## B.3 Gestión de contenido

**HU-BO-02.** Como administrador de contenido, quiero crear, editar y eliminar artículos de Academia financiera, para mantener el contenido educativo actualizado sin depender de un despliegue técnico.
- Criterios de aceptación:
  - CRUD completo de artículos, incluyendo contenido multimedia (A.12).
  - Cambios auditados (RNF-06).

**HU-BO-03.** Como administrador de contenido, quiero crear, editar y eliminar preguntas frecuentes, para mantener actualizada la sección de Ayuda (A.12).
- Criterios de aceptación: CRUD completo, con categorización.

**HU-BO-04.** Como administrador de contenido, quiero gestionar el contenido estático legal de la plataforma (términos y condiciones, política de privacidad, y el aviso legal del simulador de crédito), para mantenerlo alineado a requisitos regulatorios sin depender de un despliegue técnico.
- Criterios de aceptación: edición de los tres textos de forma independiente, con versionado/fecha de última modificación visible. Los tres son accesibles por el usuario desde Configuración de cuenta (A.8, HU-C-31).

## B.4 Configuración de la página principal

**HU-BO-05.** Como administrador de contenido, quiero configurar el menú de navegación de la plataforma, para poder ajustar los accesos disponibles sin depender de un despliegue técnico.
- Criterios de aceptación: alta/edición/orden/baja de ítems del menú (A.2, HU-C-03).

**HU-BO-06.** Como administrador de contenido, quiero configurar la disposición de los distintos carruseles de la página principal (recién publicadas, zonas destacadas, propiedades destacadas, etc.), para priorizar qué se muestra y en qué orden.
- Criterios de aceptación: orden y visibilidad de cada bloque configurable (A.2).

**HU-BO-07.** Como administrador de contenido, quiero configurar los banners promocionales de la página principal, para reflejar campañas vigentes.
- Criterios de aceptación: alta/edición/baja de banners (imagen, texto, destino, vigencia) — ver HU-C-04.

**HU-BO-08.** Como administrador de contenido, quiero destacar propiedades específicas del inventario de Pulppo en el carrusel correspondiente, para darles prioridad editorial.
- Criterios de aceptación: selección de propiedades existentes en el inventario de Pulppo (no creación de propiedades — ver 2.2, fuera de alcance).

## B.5 Configuración del simulador de crédito

**HU-BO-09.** Como administrador de contenido, quiero configurar los parámetros del simulador de crédito hipotecario, para que reflejen las condiciones vigentes de BBVA sin depender de un despliegue técnico.
- Criterios de aceptación:
  - Parametrizable como mínimo: tasa anual pública, tasa anual preferencial (cliente BBVA), plazos disponibles, porcentaje máximo de ingreso destinado a cuota (regla de HU-C-17).
  - Cambios auditados (RNF-06) y con fecha de vigencia.

## B.6 Gestión de usuarios y roles de backoffice

**HU-BO-10.** Como administrador de seguridad, quiero dar de alta, editar y dar de baja usuarios de backoffice, para controlar quién tiene acceso al panel de administración.
- Criterios de aceptación: CRUD de usuarios de backoffice, con auditoría (RNF-06).

**HU-BO-11.** Como administrador de seguridad, quiero dar de alta, editar y dar de baja roles de backoffice, y asignárselos a los usuarios, para mantener el modelo de permisos (B.1) actualizado.
- Criterios de aceptación: CRUD de roles, asignación de rol(es) por usuario, con auditoría (RNF-06).

---

## Anexo — Trazabilidad con el Draft 1.1

Toda funcionalidad del Draft 1.1 quedó representada en esta versión. Cambios de fondo respecto del original:

- Se separó la derivación a asesor inmobiliario (Pulppo/WhatsApp) de la derivación a asesor hipotecario (BBVA/Salesforce) como dos integraciones distintas — el Draft 1.1 las mencionaba en secciones separadas sin remarcar que son canales y dueños distintos (ver 2.3).
- "Los usuarios pueden acceder a un conjunto de propiedades compatibles con la simulación realizada" (bullet suelto en Simulador) pasó a ser su propia sección con regla de negocio explícita (A.9, Oportunidades).
- "Un panel de notificaciones" (bullet suelto en Perfil) se detalló en tipos y reglas de enrutamiento (A.10).
- Se agregaron: el buscador de la página principal como historia propia (HU-C-01 — el Draft 1.1 lo mencionaba dentro de "Búsqueda" sin dejar explícito que también es, ante todo, el elemento principal de la home), Precios de referencia por zona (A.2.1), Contacto con asesores como historial trazable (A.11), y el registro explícito de los 2 elementos de interfaz sin función definida (A.13).
- Se agregaron Requisitos no funcionales (sección 4), Supuestos y dependencias (sección 5) y Fuera de alcance (2.2), inexistentes en el Draft 1.1.
- El punto "(verificar)" del Draft 1.1 sobre qué datos personales puede editar el usuario (Perfil) se resolvió en HU-C-27 revisando el prototipo: nombre, teléfono y avatar son editables; el email tiene su propio flujo de verificación y no se edita libremente.
- El "menú desplegable" que el Draft 1.1 mencionaba de paso en Página principal resultó, al revisar el prototipo, un mecanismo más grande que un dropdown: un panel lateral (side sheet) compartido por toda el área de cuenta, con una barra de navegación fija adicional en desktop. Se documenta como su propia sección (A.7, HU-C-25 y HU-C-26), incluyendo una inconsistencia real detectada entre la versión mobile y desktop (falta "Oportunidades" en mobile) que queda como punto a resolver, no a corregir por cuenta propia.
- Se agregó la pantalla de Configuración de cuenta completa (A.8, HU-C-31): preferencias de notificaciones, acceso a términos/política de privacidad, y eliminar cuenta — no existía ninguna mención de esto en el Draft 1.1, y "eliminar cuenta" en particular es un requisito con implicancia regulatoria (LFPDPPP) que no puede quedar implícito.
