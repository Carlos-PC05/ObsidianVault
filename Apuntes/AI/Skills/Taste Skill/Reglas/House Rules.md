---
aliases:
  - Taste Skill House Rules
  - Reglas de la casa
  - Reglas fijas
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - anti-slop
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# House Rules — Taste Skill

> [!abstract] Resumen Ejecutivo
> Las **house rules** se aplican en cada build, sea cual sea la dirección repartida. No son sugerencias: `inspect.mjs` las mide. Si una regla choca con una marca real, se conserva el activo de marca, se dice en el informe y se pasa `--allow <rule>` al inspector solo para esa regla.

---

## 🎯 Objetivo Principal

* **Un piso mínimo de calidad:** quitar los tells que hacen que una página generada parezca generada.
* **Medible:** cada regla tiene una comprobación en el inspector.
* **Sustituir, no solo prohibir:** cada "no" trae un "en su lugar".

---

## 📋 Tabla de reglas

| Regla | En su lugar |
| :--- | :--- |
| **Titulares de dos líneas o menos, en cualquier ancho.** Todo `h1`–`h6` y la línea principal de la primera pantalla, sea cual sea su etiqueta. | Corta palabras. Luego ensancha la medida. El tamaño al final. |
| **Un título claro en la primera pantalla**, en el único `h1` de la página, y un encabezado en cada sección. | Di qué es esto: el nombre con lo que hace, el producto con para qué sirve, el evento con fecha y lugar. |
| **Márgenes exteriores iguales; ninguna sección amontonada a un lado** con la otra mitad de la pantalla vacía. | Cierra cada vacío con algo al otro lado: la imagen de la sección, una segunda columna, una lista, una figura. |
| **Sin tramos en blanco** de más de media pantalla. | El gap normal entre secciones. Un descanso deliberado es una imagen colocada o un campo de color, una vez por página. |
| **Las declaraciones son párrafos.** Una frase de intro o una cita a tamaño grande se marca como `<p>` o `<blockquote>`, se queda a tamaño de declaración (36 px o menos en desktop, 24 px en teléfono) y corre unas 25 palabras, tres líneas. | Más grande que eso ya es un titular y aplica la regla de dos líneas. En pruebas ciegas, los jurados leían una frase a 42 px en cuatro líneas como un titular de cuatro líneas. |
| **Sin eyebrows.** Ninguna etiqueta pequeña encima de un encabezado, ninguna pill badge encima del titular, ningún chip "New ·". | Pon la información en el encabezado o en la primera frase, o quítala. |
| **Sin monoespaciada** fuera de bloques de código reales. | La tipografía de texto. Cifras tabulares donde los números se alinean. |
| **Sin numeración de índice.** Nada de 01 / 02 / 03, ni (001), ni "Step 2". | El orden lo muestran la posición, el tamaño o las palabras. Un `<ol>` real si el orden importa. |
| **Sin reglas finas.** Ni divisores hairline, ni cajas de 1 px, ni líneas de grid, cruces o marcas de corte. | Espacio. Un paso en el tono de fondo. Un cambio de escala. Una forma rellena. |
| **Sin degradados.** Ni de fondo, ni en botones, ni en texto. | Color plano o una fotografía real. Un scrim neutro bajo texto sobre foto es la única excepción. |
| **Sin sombras en cosas en reposo.** Sin glows. | Un paso de tono entre superficies. Sombras solo en lo que de verdad flota: un menú, un diálogo. |
| **Sin paleta de asistente.** Nada de fondo amarillo-crema con terracota o óxido; nada de violeta o índigo. | La paleta repartida. |
| **Sin etiquetas minúsculas en mayúsculas con tracking.** | Sentence case a un tamaño legible. |
| **Sin tarjetas de feature con icono encima, sin filas de tarjetas idénticas.** | El blueprint de sección repartido. |
| **Sin glass.** Ni paneles con backdrop blur, ni navegación flotante en pill. | Una barra plana: wordmark, unos enlaces, una acción. |
| **Sin emoji, sin blobs de degradado, sin formas decorativas, sin dashboards falsos de barras grises.** | Una imagen real, un fragmento de interfaz real o nada. |
| **Sin palabra acentuada con estilo** en un titular (itálica, serif, color, subrayado). | Una cara, un color. Las palabras llevan el énfasis. |
| **Dos tipografías como máximo**, normalmente una. | El sistema tipográfico repartido. |

---

## 🧱 Las tres excepciones

Estos lugares conservan sus convenciones y el inspector los deja en paz:

1. **Código real** en `<pre>` / `<code>`: la monoespaciada es correcta ahí.
2. **Tablas y campos de formulario reales:** pueden tener líneas.
3. **Un fragmento de interfaz real** cuyo root lleva el atributo `data-ui`: los bordes y menús del producto.

---

## 📊 Por qué estas reglas

Datos del estudio de 247 sitios:

| Patrón | Sitios con puntuación ≥ 7.5 | Sitios < 6.5 |
| :--- | ---: | ---: |
| Tarjetas de feature con icono | 1 % | 28 % |
| Pill badges | 14 % | 34 % |
| Degradados | 22 % | 52 % |
| Sombras | 24 % | 48 % |
| Etiquetas minúsculas con tracking | 16 % | 57 % |

> [!note] Las que van contra la moda de galería
> La monoespaciada decorativa, los números de índice y las grids hairline son **más** comunes en sitios premiados que en sitios débiles. Precisamente por eso los generadores las imitan y por eso ahora se leen como disfraz. Aquí están prohibidas sin excepción.

---

## 💡 La regla que resume todo

> [!important] Quitar ornamento deja espacio. No lo rellenes con otro ornamento.
> Rellénalo con escala (haz más grande lo principal), con contenido completo, con una imagen hecha para este sujeto o con un campo de color plano. Déjalo vacío a propósito una vez por página como máximo.

---

## 🔗 Relacionado

* [[Laws of the Craft]]
* [[Anti-Slop]]
* [[Locks y Hero]]
* [[07 Gate]]
