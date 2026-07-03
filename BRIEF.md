# BBVA Mi Casa — Casa Academy
## Feature Brief · Equipo de Diseño · Julio 2026 · v0.2

---

## Contexto del producto

BBVA Mi Casa es una plataforma inmobiliaria desarrollada por Aper para BBVA México, en colaboración con Pulppo. Permite a usuarios buscar propiedades y acceder a créditos hipotecarios BBVA en un flujo integrado.

Casa Academy es el módulo educativo y de simulación de la plataforma. Su objetivo es acompañar al usuario desde que no sabe nada sobre hipotecas hasta que tiene un rango de compra claro y puede ver propiedades reales en ese rango.

---

## Principio rector

"Hacer fácil lo difícil." La Academy asesora con transparencia, inspira sin prometer lo que no hay, y usa lenguaje de persona — no de institución financiera.

---

## Alcance del MVP

El MVP termina en simulación. El usuario declara sus datos, ve su capacidad estimada, y con eso accede a propiedades filtradas por su rango.

No hay pre-aprobación bancaria real en esta versión. No se valida nada contra sistemas de BBVA. El resultado es orientativo y se comunica así explícitamente.

La pre-aprobación con match bancario real es fase 2 — se siembra en el MVP pero no se implementa.

---

## Flujo completo

### Paso 0 — Punto de entrada

Antes de cualquier contenido, el usuario elige su situación. La respuesta determina en qué módulo aterriza, sin obligarlo a empezar desde cero.

**Pregunta:** "¿En qué momento estás con tu compra?"

**Opciones:**
- Estoy empezando a informarme → Módulo 1
- Ya sé lo que quiero, quiero simular → Módulo 2
- Ya simulé, quiero ver propiedades → Módulo 3

Inmediatamente después, mostrar vista previa de la ruta completa:
- Módulo 1 · Entendé cómo funciona · 5 min
- Módulo 2 · Simulá tu capacidad · 5 min
- Módulo 3 · Buscá propiedades en tu rango · 3 min

Si el usuario entró por Módulo 2 o 3, los módulos anteriores aparecen como salteados — el progreso refleja su punto de entrada real.

---

### Módulo 1 — Entendé cómo funciona

Conceptos clave con tooltips en hover. Tiempo estimado: 5 minutos. Al terminar: mini-check de 3 preguntas antes de pasar al simulador.

**1.1 — Headline**

"Antes de elegir, entendé qué estás firmando."

Bajada: "Un crédito hipotecario es el compromiso financiero más grande que vas a asumir. Queremos que lo entiendas de principio a fin, no que confíes a ciegas."

**1.2 — CAT con tooltip**

Texto: "El CAT te dice el costo real de tu crédito, no solo la tasa de interés. Incluye comisiones, seguros y todos los cargos. Siempre comparé por CAT, no por tasa."

Tooltip sobre CAT: "Costo Anual Total. El indicador oficial que mide cuánto te cuesta realmente un crédito al año."

**1.3 — Tipos de tasa**

Mostrar en paralelo, no en tabla técnica:
- Tasa fija: tu cuota no cambia nunca. Más previsibilidad, cuota inicial más alta.
- Tasa variable: empieza más baja, pero puede subir con la inflación.
- Tasa creciente: arranca baja y sube gradualmente — ideal si esperás que tu ingreso crezca.

**1.4 — Enganche con tooltip**

Texto: "El enganche es lo que pagás de tu bolsillo antes de que arranque el crédito. A mayor enganche, menos intereses pagás en total."

Tooltip sobre enganche: "El pago inicial. Generalmente entre el 10% y el 20% del valor de la propiedad."

**1.5 — Mini-check**

3 preguntas de comprensión. No es un examen — es confirmación de avance.

Preguntas:
- ¿Qué mide el CAT?
- ¿Cuál tipo de tasa tiene cuota siempre igual?
- ¿Qué es el enganche?

Definir si es obligatorio o saltable para pasar al Módulo 2.

**1.6 — Micro-logro y cierre**

Al completar el mini-check, mostrar feedback de avance:

"Entendiste los conceptos clave. Ya podés simular tu capacidad."

Barra de progreso: 1 de 3 módulos completos.

Transición al Módulo 2: "Elegiste comparar por CAT. Lo vamos a usar cuando veamos tus opciones."

CTA: "Simular mi capacidad"

---

### Módulo 2 — Simulá tu capacidad

Simulador inline. Sin redirect. Cada campo tiene tooltip. El resultado es orientativo — no una pre-aprobación bancaria.

**2.1 — Headline**

"¿Cuánto podés pagar sin que duela?"

Bajada: "Una regla general — tu cuota no debería superar el 30% de tu ingreso mensual. Acá calculamos si eso aplica para vos."

**2.2 — Campos**

- ¿Cuánto ganás al mes? / Tooltip: "Ingreso neto — lo que cae a tu cuenta después de impuestos y deducciones."
- ¿Cuánto tenés ahorrado para el enganche? / Tooltip: "El pago inicial. Generalmente entre el 10% y el 20% del valor de la propiedad."
- ¿En cuántos años querés pagarlo? / Opciones: 10, 15, 20, 25 años.

**2.3 — Resultado: caso normal (cuota menor o igual al 30%)**

"Con estos datos, podés considerar propiedades de hasta $X MXN. Tu cuota mensual estimada sería de $X."

Mostrar comparación de cuotas por plazo: 10 / 15 / 20 años.

**2.4 — Resultado: caso ajustado (cuota mayor al 30%)**

"Esta cuota representa el X% de tu ingreso. Es posible, pero ajustado. ¿Querés ver qué pasa si extendés el plazo?"

Este mensaje diferencia a BBVA de la comunicación estándar de banca que nunca advierte sobre sobreendeudamiento.

**2.5 — Disclaimer obligatorio**

"Esta simulación se basa en los datos que declarás. No es una pre-aprobación bancaria ni garantiza que BBVA apruebe tu solicitud."

**2.6 — Transición al Módulo 3**

Mostrar resumen de una línea con los datos ingresados antes de pasar:

"Tu perfil: $X de ingreso · $X de enganche · 20 años · hasta $X MXN"

CTA: "Ver propiedades en mi rango"

---

### Módulo 3 — Buscá propiedades en tu rango

Grilla de propiedades mock filtradas por el monto máximo calculado en el Módulo 2. El perfil del usuario aparece como pills al inicio del módulo.

**3.1 — Headline**

"Propiedades en tu rango."

Bajada: "Resultados filtrados según tu simulación. Los precios son referenciales."

**3.2 — Pills de perfil**

Mostrar datos persistidos desde Módulo 2:
- Hasta $X MXN
- $X de enganche
- X años

**3.3 — Grilla de propiedades mock**

4 propiedades con: precio, colonia, recámaras, baños, m². Precios dentro del rango simulado.

Datos mock de ejemplo:
- $2,800,000 MXN · Narvarte, CDMX · 2 rec · 1 baño · 65 m²
- $3,100,000 MXN · Del Valle, CDMX · 2 rec · 2 baños · 72 m²
- $3,400,000 MXN · Coyoacán, CDMX · 3 rec · 2 baños · 85 m²
- $3,600,000 MXN · Benito Juárez, CDMX · 3 rec · 2 baños · 90 m²

**3.4 — Banner fase 2**

"¿Te interesa una de estas propiedades? En una próxima versión podrás pre-aprobar tu crédito directamente con BBVA y hacer match con la propiedad elegida. Por ahora, contactá a un asesor para continuar."

CTA: "Hablar con un asesor"

---

## Glosario — tooltips obligatorios

Todos los términos deben tener tooltip al primer uso en cualquier módulo.

- CAT — Costo Anual Total. Mide cuánto te cuesta realmente el crédito al año — incluye tasa, comisiones y seguros.
- Enganche — El pago inicial antes de que arranque el crédito. Generalmente entre el 10% y el 20% del valor de la propiedad.
- Aforo — El porcentaje del valor de la propiedad que el banco está dispuesto a financiar. A mayor aforo, menos enganche necesitás.
- Tasa fija — La tasa no cambia en toda la vida del crédito. Tu cuota es siempre la misma.
- Tasa variable — La tasa puede cambiar según indicadores económicos. Tu cuota puede subir o bajar.
- Tasa creciente — Arranca baja y sube gradualmente. Útil si esperás que tu ingreso crezca con el tiempo.
- INFONAVIT — Administra el ahorro para vivienda de trabajadores del sector privado. Se puede usar como enganche o en cofinanciamiento.
- FOVISSSTE — Lo mismo que INFONAVIT pero para trabajadores del gobierno.
- Subcuenta de vivienda — El dinero acumulado en INFONAVIT o FOVISSSTE disponible para el crédito.
- Amortización — El proceso de ir pagando el capital del crédito con cada cuota mensual.
- Valuación — Determina el valor oficial de la propiedad. Lo hace un perito certificado y lo paga el solicitante.
- UDI — Unidad de inversión. Si el crédito es en UDIs, el saldo puede crecer si hay inflación.
- Escrituración — El proceso legal y notarial de formalizar la compra. Costo adicional — generalmente entre el 4% y el 7% del valor de la propiedad.

---

## Criterios de diseño del prototipo

- Mobile first. Se siente como una web real con navegación.
- Wireframe limpio — blanco y negro, sin estilos de marca por ahora.
- Transiciones entre módulos.
- Barra de progreso visible en todo momento.
- Datos mock hardcodeados.
- Lógica del simulador: tasa referencial 10.5% anual, cuota calculada con fórmula de amortización estándar.

---

## Preguntas abiertas

- ¿Tú o usted? La Academy usa tú, el resto del sitio usa usted. Definir si se acepta el quiebre o se unifica.
- ¿El mini-check del Módulo 1 es obligatorio o saltable?
- ¿El progreso se guarda si el usuario cierra la página y vuelve?
- ¿El resumen de perfil del Módulo 2 persiste hacia el formulario de contacto con el asesor?
- Integración con API de Pulppo para propiedades reales — pendiente para fase siguiente.
- Pre-aprobación bancaria real con match de producto BBVA — fase 2, no en MVP.
