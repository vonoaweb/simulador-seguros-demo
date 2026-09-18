# Rubén Alcántara — sitio de agente de seguros (demo)

Demostración de **Vonoa Web**: cómo se vería el sitio de un agente de seguros independiente en México, con un simulador educativo integrado.

> ⚠️ **Persona ficticia.** "Rubén Alcántara" no existe; su trayectoria, credenciales y contacto son ilustrativos. El simulador entrega **estimaciones** con parámetros propios que **no provienen de la tarifa de ninguna aseguradora**, y no son oferta, cotización ni asesoría. Las compañías aparecen como perfiles anónimos (A, B, C). Vonoa no es aseguradora ni agente de seguros.

## Estructura

| # | Sección | Qué hace |
|---|---|---|
| — | Hero | Nombre a escala de viewport + ficha de 4 datos |
| 01 | Perfil | Posicionamiento (independiente vs cautivo), credenciales, 4 cifras con contador |
| 02 | Proceso | Diagnóstico → comparativo → suscripción → acompañamiento |
| 03 | Ramos | GMM, PPR, Auto, Vida — los tres primeros enlazan al simulador |
| 04 | Con quién cotizo | 24 aseguradoras reales con su razón social del registro CNSF |
| 05 | Anatomía de una prima | Sección con *pin* y *scrub*: la prima se descompone en sus 4 conceptos conforme bajas |
| 06 | Simulador | El motor completo (GMM / PPR / Auto) |
| 07 | FAQ | Seis dudas reales, con los números del simulador de respaldo |
| 08 | Contacto | Datos configurables |
| — | Pie | Fuentes citadas + aviso legal |

## Diseño

**Editorial suizo con datos.** La tesis: el estilo suizo lee como confiable *porque su lógica visual es verificable* — es el código de Stripe, Linear y el NYT. Encaja con una página cuyo contenido ya separa `Oficial` de `Estimado`.

- **Tipografía:** Archivo 800 para display a escala de viewport · Inter para prosa · **IBM Plex Mono para cada cifra, etiqueta y número de sección**. La jerarquía tipográfica refuerza qué es dato y qué es prosa.
- **Color:** papel cálido `#f2efe8` (no el dark-SaaS genérico), tinta `#14110f`, un solo acento verde `#1f4d3d`. Ámbar y rojo quedan reservados para señalar `Estimado` y alertas.
- **Retícula visible:** hairlines entre secciones y bordes de rejilla (stats, ramos, hero-meta, tablas). Hubo líneas verticales fijas de fondo; se retiraron porque estorbaban la lectura.
- **Sin esquinas redondeadas ni sombras.** Bordes de 1px, thumb del slider en rombo.

### Fotografía

Dos imágenes de banco de [Pexels](https://www.pexels.com/license/), en `img/`:

| Archivo | Uso | Autor |
|---|---|---|
| `ruben-retrato.jpg` | Retrato, sección 01 | Moisés Sánchez |
| `asesoria.jpg` | Banda, sección 02 | Mikhail Nilov |

Ambas llevan un rótulo visible dentro de la propia página que las declara como foto de stock, y crédito al pie. **No son personas del sector asegurador.** Es la misma lógica que el resto del sitio: si un dato es estimado se dice, y si una foto es de banco también.

Al sustituirlas por fotos reales del agente hay que quitar esos rótulos (`.ph-note` y el `figcaption` de `.photo-band`) y el párrafo de créditos del pie.

> Nota de implementación: el atributo `height` de un `<img>` se aplica como *presentation hint* y anula `aspect-ratio`. La banda necesita `height: auto` explícito para respetar su 16/7.

### Animación

Deliberadamente **sin cursor custom, sin partículas, sin kinetic typography** — los clichés de página generada por IA. Sólo tres efectos, los tres atados al contenido:

1. **Reveal escalonado** por sección (GSAP + ScrollTrigger) para ordenar la lectura.
2. **Contadores** en las cifras del perfil.
3. **Scrub con pin en la sección 04**: la barra se llena concepto por concepto mientras el total sube de $0 a $33,048. La animación *es* la explicación de cómo se arma una prima. En móvil no hace pin: se revela al entrar.

Si GSAP no carga o el usuario tiene `prefers-reduced-motion`, la página se queda estática y completamente legible (`body.no-anim`).

## Datos: Oficial vs Estimado

Cada cifra va etiquetada. **`Oficial`** = fuente pública citada al pie. **`Estimado`** = parámetro construido para la demo.

| Dato | Valor | Fuente |
|---|---|---|
| UMA 2026 | $117.31 diaria · $42,794.64 anual | INEGI, comunicado 1/26, DOF 09-ene-2026 |
| Tope 5 UMAs | $213,973.20 | 5 × UMA anual |
| Tarifa anual ISR 2026 | 11 renglones, 1.92%–35% | Anexo 8 RMF 2026, DOF 28-dic-2025 (Art. 152 LISR) |
| Art. 151 fracc. V | 10% de ingresos acumulables, tope 5 UMAs | LISR |
| Art. 185 | $152,000 anuales, ISR **diferido**, permanencia 5 años | LISR |
| Art. 93 fracc. XXI | Exención al cobro: 60 años + 5 de vigencia | LISR |
| IVA | 16% en GMM y auto; vida y rentas vitalicias exentas | Art. 15 fracc. IX LIVA |
| Clasificación IMC | <18.5 / 18.5–24.9 / 25–29.9 / ≥30 | OMS |

**Estimado:** tarifas base por edad, factores de zona, deducible, coaseguro, suma asegurada, tabaquismo, IMC, precios de vehículos, curva de depreciación, recargos por pago fraccionado, derechos de póliza, rendimientos y la dispersión entre A, B y C.

## Nombres reales vs. precios inventados

La sección 04 nombra **24 aseguradoras reales** con su razón social exacta, tomada del [registro público de la CNSF](https://listainstituciones.cnsf.gob.mx/) (CSV oficial, corte **30/06/2026**: 113 instituciones — 85 Seguros, 11 Salud, 9 Fianzas, 6 Pensiones, 2 Vivienda).

El **simulador también compara por nombre**, con compañías apropiadas a cada ramo:

| Ramo | Columnas |
|---|---|
| GMM | GNP · AXA · Monterrey NYL |
| Auto | Quálitas · HDI · Chubb |
| PPR | No aplica — las columnas son escenarios de rendimiento (6 / 7.5 / 9%) |

La tarjeta de resultado muestra el nombre corto y, debajo en monoespaciada, la razón social inscrita.

**Lo que la página deja explícito en pantalla, junto al comparativo:** los importes **no son las tarifas de esas compañías** — los calcula la demo con parámetros propios — y la diferencia entre las tres columnas es un supuesto que **no indica cuál es realmente más barata**. El factor de dispersión (`INSURER_FACTOR`, A 1.00 / B 1.05 / C 0.96) es arbitrario y está comentado como tal en el código.

Aviso incluido: las compañías se muestran como panorama del mercado; **no hay relación comercial, nombramiento ni patrocinio** con la demo.

Detalle que da credibilidad y suele desconocerse: el **ramo Salud tiene autorización distinta a Seguros** bajo la LISF. Por eso AXA y Quálitas aparecen dos veces — son entidades separadas con autorizaciones separadas.

## El simulador

**GMM.** Tarificación por edad y sexo, IMC en vivo, zona por CP, deducible, coaseguro y suma asegurada. Todos los controles afectan el resultado.

**PPR.** Ahorro fiscal como **diferencia real de ISR** con y sin deducción (no una tasa marginal a secas). Tope real: el menor entre 10% del ingreso y 5 UMAs; ingreso, topes y tarifa se indexan por inflación. Rendimiento **neto de comisión**. Fondo en **pesos de hoy** además del nominal. Renta mensual como anualidad de poder adquisitivo constante — **no es renta vitalicia**. Interruptor de si reinviertes o no la devolución del SAT. Exporta a CSV con los supuestos en el encabezado.

**Auto.** Depreciación sobre el valor comercial (15% el primer año, 10% cada siguiente, piso 20%), factores de zona, edad y uso, tres paquetes con límites coherentes en toda la interfaz.

## Configuración

Constantes al inicio del `<script>` del motor. Vacías muestran un aviso en lugar de un enlace roto:

```js
const WHATSAPP = '';   // ej. '5213312345678'
const TELEFONO = '';   // ej. '+523312345678'
const CORREO   = '';   // ej. 'ruben@ejemplo.mx'
```

Para un agente real hay que sustituir además: nombre, trayectoria, número de compañías, folio de cédula CNSF verificable y las dos fotografías (ver *Fotografía* arriba).

## Desarrollo

Un `index.html` más la carpeta `img/`. Dependencias externas: GSAP 3.12.5 + ScrollTrigger por CDN y Google Fonts.

```bash
python -m http.server 8055
```

## Aviso legal

Demostración con fines educativos. No intermedia la contratación de pólizas ni brinda asesoría financiera, fiscal o de inversiones. La contratación de seguros en México debe hacerse a través de instituciones autorizadas y agentes con cédula vigente ante la **Comisión Nacional de Seguros y Fianzas (CNSF)**. Antes de tomar decisiones fiscales, consulta a un contador público.

Marco normativo vigente a septiembre de 2026. En ejercicios posteriores hay que actualizar `UMA_2026_ANUAL` y `TARIFA_ISR_2026` en el script.
