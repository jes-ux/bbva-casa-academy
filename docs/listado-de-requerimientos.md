# Requerimientos: Marketplace inmobiliario BBVA México

**Versión:** Draft 1.2, 25 de septiembre de 2026
**Autor:** Producto y tecnología de Aper

## 1. Objetivo

Construir una plataforma para la compra de inmuebles en México con los objetivos de colocación de préstamos hipotecarios y generación de ingresos por venta de inmuebles.

## 2. Alcance

### 2.1 Incluye

- Construcción de la plataforma que permita a los usuarios descubrir inmuebles y simular financiamiento.
- Construcción del backoffice para la administración de contenido, configuración y usuarios de la plataforma.
- Integración con el inventario de propiedades provisto por Pulppo.
- Integración con los sistemas de atención de asesores de Pulppo (asesor comercial, vía WhatsApp) y de BBVA (asesor hipotecario, vía Salesforce).

### 2.2 Fuera de alcance

- Pagos, señas, reservas o firma de contratos dentro de la plataforma.
- Originación o aprobación del crédito hipotecario. La plataforma simula y deriva.
- Alta, edición o baja de propiedades. El inventario es de Pulppo.
- CRM propio de seguimiento de leads. Se usan Pulppo y Salesforce.
- Aplicaciones móviles nativas. El alcance inicial es web responsiva.
- Chat propio en tiempo real y contenido generado por usuarios (reseñas, foros).

## 3. Actores

- Usuario: visitante sin sesión.
- Usuario autenticado: usuario con sesión iniciada.
- Administrador: usuario del backoffice que gestiona contenido y configuración.
- Administrador de seguridad: usuario del backoffice que gestiona usuarios y roles del backoffice.

## 4. Supuestos y dependencias

- Pulppo expone el inventario con disponibilidad actualizada y un canal de derivación de leads.
- BBVA y Salesforce exponen un mecanismo de derivación de leads hipotecarios que recibe el contexto de la simulación.
- BBVA provee las tasas y parámetros vigentes del crédito.
- El contenido de Academia, preguntas frecuentes y textos legales lo provee o aprueba negocio y legal de BBVA.
- Existe hosting institucional para la plataforma y el backoffice.

## 5. Requerimientos no funcionales y restricciones técnicas

- Seguridad: contraseñas con hash, TLS, cifrado de datos personales y 2FA obligatorio en el backoffice.
- Protección de datos: cumplimiento de la LFPDPPP (aviso de privacidad, consentimiento y derechos ARCO).
- Auditoría: todo cambio hecho en el backoffice queda registrado con usuario, fecha y detalle.
- Accesibilidad: WCAG 2.1 nivel AA como mínimo.
- Disponibilidad y rendimiento: umbrales a definir con Infraestructura.
- Plataforma responsiva (mobile y desktop), en español de México y con moneda MXN.
- Restricción técnica pendiente de definición: el frontend consume componentes de una librería de UI propia de Aper. Para capacidades ya resueltas por la industria (por ejemplo, mapas) se usa una solución de terceros.

## 6. Cómo leer el listado

Cada requerimiento tiene un ID, el origen del dato que lo alimenta y el requerimiento del otro lado con el que se relaciona. La regla es que todo lo que un usuario ve o usa y que sale de una configuración o contenido editorial tiene su par en el backoffice.

**Origen del dato:** Pulppo (inventario), Usuario (lo genera el usuario), Backoffice (lo configura un administrador), Sistema (se calcula solo).

**Estado:**
- Definido: estaba en la versión anterior.
- Ajustado: estaba, con un cambio de redacción o criterio, o se restituyó algo que se había perdido.
- Propuesta: no estaba. Requiere aprobación.
- A decidir: depende de una decisión abierta (sección 9).

**Fase:** columna a completar por el equipo.

## 7. PARTE A: Experiencia del Cliente

### Página principal

| ID | Requerimiento | Origen | Par en Backoffice | Estado | Fase |
|---|---|---|---|---|---|
| CL-01 | Los usuarios pueden descubrir propiedades recién publicadas. | Pulppo | BO-15 | Definido | |
| CL-02 | Los usuarios pueden descubrir propiedades de zonas destacadas. | Backoffice | BO-15, BO-18 | Definido | |
| CL-03 | Los usuarios pueden conocer propiedades destacadas. | Backoffice | BO-15, BO-17 | Definido | |
| CL-04 | Los usuarios pueden acceder a un menú desplegable con accesos de navegación. | Backoffice | BO-14 | Definido | |
| CL-05 | Los usuarios pueden explorar por tipo de propiedad y por zona desde un megamenú y desde un bloque de la página principal. | Pulppo y Backoffice | BO-14, BO-20 | Ajustado | |
| CL-06 | Los usuarios pueden visualizar banners promocionales. | Backoffice | BO-16 | Definido | |
| CL-07 | Los usuarios pueden acceder a un módulo de información sobre créditos. | Backoffice | BO-19 | Definido | |
| CL-08 | Los usuarios pueden acceder a precios de propiedades por zona. | Sistema o Backoffice | BO-20 | A decidir (D-01) | |
| CL-09 | Los usuarios pueden acceder a la propuesta de valor y a preguntas frecuentes. | Backoffice | BO-19, BO-10 | Definido | |
| CL-10 | Los usuarios pueden acceder al simulador de crédito hipotecario. | Sin origen | Sin par | Definido | |

### Búsqueda

| ID | Requerimiento | Origen | Par en Backoffice | Estado | Fase |
|---|---|---|---|---|---|
| CL-11 | Los usuarios pueden acceder a un buscador de propiedades desde la página principal, la ficha de la propiedad y el listado. | Pulppo | Sin par | Definido | |
| CL-12 | Los usuarios pueden refinar los resultados con un conjunto de filtros preestablecidos. | Backoffice o fijo | BO-21 | A decidir (D-02) | |
| CL-13 | Los usuarios pueden ordenar los resultados con modos de ordenamiento preestablecidos. | Backoffice o fijo | BO-21 | A decidir (D-02) | |
| CL-14 | Los usuarios pueden ver los resultados en un mapa. | Pulppo | Sin par | Definido | |
| CL-15 | Los usuarios pueden ver los resultados en un listado. | Pulppo | Sin par | Definido | |

### Ficha de la propiedad

| ID | Requerimiento | Origen | Par en Backoffice | Estado | Fase |
|---|---|---|---|---|---|
| CL-16 | Los usuarios pueden ver el precio, valor por m2, datos del desarrollador, entre otros. | Pulppo | BO-27 | Definido | |
| CL-17 | Los usuarios pueden ver una galería de fotos y planos. | Pulppo | Sin par | Definido | |
| CL-18 | Los usuarios pueden ver un recorrido 360° de la propiedad. | Pulppo | Sin par | Definido | |
| CL-19 | Los usuarios pueden ver el mapa con la localización de la propiedad. | Pulppo | Sin par | Definido | |
| CL-20 | Los usuarios pueden acceder a propiedades similares sugeridas. | Sistema | Sin par | Definido | |
| CL-21 | Los usuarios autenticados pueden guardar la propiedad en sus favoritas. | Usuario | Sin par | Definido | |
| CL-22 | Los usuarios pueden acceder al simulador de crédito con vinculación a la propiedad, sin necesidad de sesión. | Usuario | Sin par | Ajustado | |
| CL-23 | Los usuarios autenticados pueden agregar la propiedad a una simulación existente siempre que esté en rango. | Usuario | Sin par | Definido | |
| CL-24 | Los usuarios autenticados pueden acceder a un botón que los deriva a un asesor comercial de Pulppo vía WhatsApp. | Pulppo | BO-25 | Definido | |

### Ficha de precios por zona

| ID | Requerimiento | Origen | Par en Backoffice | Estado | Fase |
|---|---|---|---|---|---|
| CL-25 | Los usuarios pueden ver un gráfico de evolución del precio de las propiedades en la zona. | Sistema o Backoffice | BO-20 | A decidir (D-01) | |
| CL-26 | Los usuarios pueden acceder a las propiedades de la zona. | Pulppo | Sin par | Definido | |
| CL-27 | Los usuarios pueden ver datos generales del mercado en la zona. | Sistema o Backoffice | BO-20 | A decidir (D-01) | |

### Simulador de crédito hipotecario

| ID | Requerimiento | Origen | Par en Backoffice | Estado | Fase |
|---|---|---|---|---|---|
| CL-28 | Los usuarios pueden simular un préstamo hipotecario para conocer las condiciones de préstamos disponibles, sin necesidad de sesión. | Backoffice | BO-22 | Ajustado | |
| CL-29 | Los usuarios pueden ver el aviso legal del simulador. | Backoffice | BO-13 | Propuesta | |
| CL-30 | Los usuarios autenticados pueden guardar sus simulaciones realizadas. | Usuario | Sin par | Definido | |
| CL-31 | Los usuarios autenticados con simulaciones pueden acceder a un asesor hipotecario de BBVA vía Salesforce. | BBVA | BO-25 | Ajustado | |
| CL-32 | Los usuarios autenticados pueden acceder a un conjunto de propiedades compatibles con la simulación realizada. | Pulppo | Sin par | Definido | |
| CL-33 | Los usuarios autenticados pueden guardar propiedades compatibles junto a la simulación realizada. | Usuario | Sin par | Definido | |
| CL-34 | Los usuarios autenticados pueden eliminar propiedades guardadas compatibles con la simulación realizada. | Usuario | Sin par | Definido | |

### Registro y autenticación

| ID | Requerimiento | Origen | Par en Backoffice | Estado | Fase |
|---|---|---|---|---|---|
| CL-35 | Los usuarios pueden registrarse en la plataforma mediante correo y contraseña. | Usuario | Sin par | Definido | |
| CL-36 | Los usuarios pueden autenticarse en la plataforma mediante correo y contraseña. | Usuario | Sin par | Ajustado | |
| CL-37 | Los usuarios pueden registrarse y autenticarse mediante login social de Google. En el registro se guardan solo el nombre y el correo electrónico; el resto de los datos los completa el usuario desde su perfil. | Usuario | Sin par | Ajustado | |
| CL-38 | Los usuarios pueden solicitar el restablecimiento de su contraseña. | Usuario | Sin par | Definido | |
| CL-39 | Los usuarios deben aceptar los términos y condiciones y el aviso de privacidad al registrarse. | Backoffice | BO-11, BO-12 | Propuesta | |
| CL-40 | Los usuarios registrados con correo y contraseña deben verificar su correo electrónico. | Sistema | Sin par | Propuesta | |

### Cuenta del usuario

| ID | Requerimiento | Origen | Par en Backoffice | Estado | Fase |
|---|---|---|---|---|---|
| CL-41 | Los usuarios autenticados pueden acceder a un menú de cuenta con accesos a sus secciones, a Academia, a Ayuda, a Precios por zona y al simulador. | Sin origen | Sin par | Ajustado | |
| CL-42 | Los usuarios autenticados pueden acceder a su perfil para modificar sus datos personales. | Usuario | Sin par | Definido | |
| CL-43 | Los usuarios autenticados pueden acceder a sus propiedades favoritas. | Usuario | Sin par | Definido | |
| CL-44 | Los usuarios autenticados pueden acceder a sus simulaciones guardadas. | Usuario | Sin par | Definido | |
| CL-45 | Los usuarios autenticados pueden acceder a su mensajería. | A decidir | A decidir | A decidir (D-03) | |
| CL-46 | Los usuarios autenticados pueden acceder a un panel de notificaciones. | Sistema y Backoffice | BO-23 | Definido | |
| CL-47 | Los usuarios autenticados pueden acceder a un listado de oportunidades de propiedades. | Sistema | Sin par | Definido | |
| CL-48 | Los usuarios autenticados pueden acceder a una página de configuración de notificaciones y privacidad, con acceso a los términos y condiciones y a la política de privacidad. | Usuario y Backoffice | BO-11, BO-12 | Definido | |
| CL-49 | Los usuarios autenticados pueden eliminar su cuenta. | Usuario | BO-24 | Propuesta | |
| CL-50 | Los usuarios autenticados pueden cerrar sesión. | Usuario | Sin par | Definido | |

### Academia financiera y ayuda

| ID | Requerimiento | Origen | Par en Backoffice | Estado | Fase |
|---|---|---|---|---|---|
| CL-51 | Los usuarios autenticados pueden acceder a una sección de artículos de educación financiera con contenido multimedia. | Backoffice | BO-09 | A decidir (D-07) | |
| CL-52 | Los usuarios pueden acceder a una sección de ayuda y preguntas frecuentes. | Backoffice | BO-10 | Definido | |

## 8. PARTE B: Experiencia del Administrador de Backoffice

### Acceso y seguridad

| ID | Requerimiento | Habilita en el front | Estado | Fase |
|---|---|---|---|---|
| BO-01 | Los administradores pueden ingresar a la plataforma con correo, contraseña y 2FA. | Acceso al backoffice | Definido | |
| BO-02 | Los administradores pueden solicitar el alta y baja de mecanismos de 2FA. | Acceso al backoffice | Definido | |
| BO-03 | Los administradores pueden solicitar la recuperación de su contraseña. | Acceso al backoffice | Definido | |
| BO-04 | Los administradores pueden modificar sus datos personales. | Acceso al backoffice | Definido | |
| BO-05 | Los administradores de seguridad pueden realizar altas, bajas y modificaciones de usuarios de backoffice. | Acceso al backoffice | Definido | |
| BO-06 | Los administradores de seguridad pueden realizar altas, bajas y modificaciones de roles de usuarios de backoffice. | Acceso al backoffice | Definido | |
| BO-07 | Los administradores de seguridad pueden realizar la asignación de roles de usuarios de backoffice. | Acceso al backoffice | Definido | |
| BO-08 | Los administradores de seguridad pueden consultar el registro de auditoría de los cambios hechos en el backoffice. | Control de cambios | Propuesta | |

### Contenido educativo, ayuda y legal

| ID | Requerimiento | Habilita en el front | Estado | Fase |
|---|---|---|---|---|
| BO-09 | Los administradores pueden crear, editar y borrar artículos de educación financiera, incluido su contenido multimedia. | CL-51 | Definido | |
| BO-10 | Los administradores pueden crear, editar y borrar preguntas frecuentes de la plataforma. | CL-09, CL-52 | Definido | |
| BO-11 | Los administradores pueden realizar la gestión del contenido estático de los términos y condiciones de la plataforma. | CL-39, CL-48 | Definido | |
| BO-12 | Los administradores pueden realizar la gestión del contenido estático de la política de privacidad y el aviso de privacidad. | CL-39, CL-48 | Propuesta | |
| BO-13 | Los administradores pueden realizar la gestión del contenido estático legal referido al simulador de préstamos. | CL-29 | Definido | |

### Página principal

| ID | Requerimiento | Habilita en el front | Estado | Fase |
|---|---|---|---|---|
| BO-14 | Los administradores pueden configurar el menú de navegación de la plataforma, incluido el megamenú. | CL-04, CL-05 | Definido | |
| BO-15 | Los administradores pueden configurar la disposición de los diferentes bloques de carruseles de la página principal. | CL-01, CL-02, CL-03 | Definido | |
| BO-16 | Los administradores pueden configurar los banners promocionales de la página principal. | CL-06 | Definido | |
| BO-17 | Los administradores pueden destacar propiedades en el carrusel correspondiente de la página principal. | CL-03 | Definido | |
| BO-18 | Los administradores pueden destacar zonas en el carrusel correspondiente de la página principal. | CL-02 | Definido | |
| BO-19 | Los administradores pueden editar el contenido del módulo de información sobre créditos y de la propuesta de valor de la página principal. | CL-07, CL-09 | Propuesta | |

### Zonas, búsqueda y simulador

| ID | Requerimiento | Habilita en el front | Estado | Fase |
|---|---|---|---|---|
| BO-20 | Los administradores pueden gestionar el catálogo de zonas y los datos de mercado por zona. | CL-05, CL-08, CL-25, CL-27 | A decidir (D-01) | |
| BO-21 | Los administradores pueden configurar los filtros y modos de ordenamiento disponibles en la búsqueda. | CL-12, CL-13 | A decidir (D-02) | |
| BO-22 | Los administradores pueden realizar toda la configuración parametrizada del simulador de préstamos. | CL-28 | Definido | |

### Notificaciones, clientes, asesores y reportes

| ID | Requerimiento | Habilita en el front | Estado | Fase |
|---|---|---|---|---|
| BO-23 | Los administradores pueden gestionar las plantillas de las notificaciones y de los emails de novedades. | CL-46 | Propuesta | |
| BO-24 | Los administradores pueden consultar cuentas de clientes y ejecutar bajas por solicitud del titular. | CL-49 | Propuesta | |
| BO-25 | Los administradores pueden configurar los canales de derivación a asesores (WhatsApp de Pulppo y Salesforce de BBVA). | CL-24, CL-31 | A decidir (D-04) | |
| BO-26 | Los administradores pueden consultar reportes de leads derivados y de uso de la plataforma. | Objetivo del proyecto | A decidir (D-05) | |
| BO-27 | Los administradores pueden despublicar una propiedad de la plataforma sin modificar el inventario de Pulppo. | CL-16 y el resto de la ficha | A decidir (D-06) | |

## 9. Decisiones abiertas

- D-01: precios por zona, evolución y datos de mercado. Decidir si se calculan solos a partir del inventario de Pulppo o si un administrador los carga. Define también el dueño del catálogo de zonas. Afecta CL-08, CL-25, CL-27 y BO-20.
- D-02: filtros y ordenamientos preestablecidos. Decidir si son fijos por diseño o configurables desde el backoffice. Afecta CL-12, CL-13 y BO-21.
- D-03: mensajería. Decidir si es un historial de contactos con asesores (no requiere backoffice) o un chat (contradice el fuera de alcance). Se recomienda el historial. Afecta CL-45.
- D-04: canales de derivación. Decidir quién configura el número, el mensaje y los parámetros de WhatsApp y Salesforce: el backoffice, o los propios Pulppo y BBVA. Afecta CL-24, CL-31 y BO-25.
- D-05: reportes de leads y uso. Decidir si se resuelven en la plataforma o en Salesforce y Pulppo. El objetivo del proyecto es medir colocación e ingresos por venta. Afecta BO-26.
- D-06: despublicar una propiedad sin tocar el inventario de Pulppo. Decidir si hace falta. Afecta BO-27.
- D-07: Academia con o sin sesión. La versión original la ofrecía sin sesión; la versión anterior la pasó a solo autenticados. Afecta CL-51.
- D-08: "Tono y Voz de la marca en toda la plataforma". Se retiró como requerimiento porque no es una capacidad verificable. Si negocio necesita editar los textos estáticos de la plataforma, se agrega como un requerimiento concreto.

## 10. Cambios respecto de la versión anterior

- El simulador queda como público: la sesión se pide solo para guardar (CL-22, CL-28).
- Se agruparon duplicados: el menú de cuenta (CL-41) reemplaza los accesos repetidos de Perfil; Academia, Ayuda y FAQ y "tipos de propiedades" quedan en un solo lugar cada uno.
- Se restituyó el login con correo y contraseña (CL-36) y "vía Salesforce" en el asesor hipotecario (CL-31).
- Se agregó cómo funciona el registro con Google (CL-37).
- Propuestas nuevas: CL-29, CL-39, CL-40, CL-49, BO-08, BO-12, BO-19, BO-23 y BO-24.
- Se sumaron fuera de alcance, supuestos, no funcionales y decisiones abiertas.
- Se corrigieron errores de redacción ("Pulpo", "puede", "compatibles con a la", texto duplicado en cerrar sesión, verbo faltante en datos del mercado).
