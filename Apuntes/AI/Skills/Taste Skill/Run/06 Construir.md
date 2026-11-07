---
aliases:
  - Taste Skill Paso 6
  - Construir la página
  - Build
tags:
  - dev/ai
  - design-system
  - taste-skill
  - taste-skill/run
  - frontend
  - css
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# 06 — Construir

> [!abstract] Resumen Ejecutivo
> Se construye con el movimiento apagado en la cabeza: una página que necesita animación para parecer terminada no está terminada. El orden es tokens, hero, secciones, imágenes, cosa viva, escenas, plenitud, frame y estados. La geometría de cada sección viene de la ficha; el contenido y el sujeto son de este proyecto.

---

## 🎯 Objetivo Principal

* **Construir sobre tokens:** color y tipo salen de la ficha como custom properties; todo lo demás referencia tokens.
* **Hero exacto:** el blueprint del hero, a desktop y a móvil, antes de que exista cualquier otra cosa.
* **Secciones con idea:** cada sección lleva la idea, incluidos pricing, FAQ y footer.
* **Imágenes reales:** al menos cuatro imágenes reales, normalmente seis a doce.

---

## 📋 Orden de trabajo

### 1. Tokens

* Color y roles tipográficos de la ficha como CSS custom properties.
* Todo lo que venga después referencia solo tokens.
* `<skill>/assets/base.css` es una base neutra opcional: reset, nombres de tokens, escalas fluidas, grid, focus, selection y helpers de movimiento. No tiene look propio.

### 2. Hero

* Exactamente como lo describe el blueprint.
* A desktop y a ancho de teléfono.
* Consigue que lo grande sea grande y lo pequeño sea pequeño antes de que exista nada más.

### 3. Secciones

* Cada sección desde su blueprint, con el contenido de este proyecto.
* Mantén la geometría, cambia el sujeto.
* Secciones vecinas no comparten layout.
* Lleva la idea a **todas** las secciones, pricing, FAQ y footer incluidos.

> [!warning] El fallo más común
> Una primera pantalla distintiva seguida de bloques stock. Incluso entre sitios buenos, es el fallo más repetido del estudio.

### 4. Imágenes

Orden de preferencia:

1. Las del proyecto.
2. Generadas, si hay herramienta.
3. **Imágenes reales del mundo del sujeto** en colecciones abiertas, cuando exista una honesta.
4. Dibujadas en código desde el sujeto, para lo que no existe imagen honesta.
5. Las fotografías repartidas en la ficha, solo donde encaje una imagen honestamente genérica.
6. Tipo y color solos.

Comandos:

```bash
# Buscar en Openverse, Wikimedia Commons, Art Institute of Chicago y Met
node <skill>/scripts/assets.mjs "<query>"

# Descargar una imagen con su crédito
node <skill>/scripts/assets.mjs --get <ref>

# Imprimir la línea de crédito para el footer
node <skill>/scripts/assets.mjs --credits

# Buscar vídeo
node <skill>/scripts/assets.mjs --video "<query>"
```

> [!important] El suelo que comprueba el inspector
> Al menos **4 imágenes reales** en la página, normalmente **6–12** (fotografías, imágenes de archivo, mapas, grabados, footage, artwork raster; en software, los fragmentos de interfaz recortados también cuentan). Los dibujos SVG **no** cuentan. Añade una imagen en movimiento donde exista un clip honesto.

* Busca el inventario, no la categoría: la calle en postales antiguas, un mapa viejo del barrio, el material de cerca, el oficio en grabados, un objeto de museo de su tipo, una lámina botánica, una carta estelar.
* Mira cada imagen antes de elegirla.
* Una imagen encontrada muestra el mundo alrededor del cliente; **nunca** se presenta como producto, habitación, plato, persona o equipo del cliente.
* Nunca escribas en la página de dónde vienen las fotos o qué no son. Una caption dice qué es la imagen; el crédito va pequeño en el footer.

### 5. La cosa viva

* La del paso 4, construida en la sección donde prueba el claim (a menudo el hero o la reserva).
* Contenido real, versión móvil para el pulgar y fallback plano.

### 6. Scroll scenes

Si la ficha las reparte:

* Copia el stage compartido y los archivos de la escena desde `<skill>/assets/scenes/`.
* Rellena la escena con las imágenes y palabras de este proyecto.
* Ajusta los tokens para que tome el look de la página.
* Una escena es composición: decide cuánto mide la página y qué muestra cada pantalla. Se construye ahora y se ve en cada ronda de mirada, no se añade al final.
* `references/scenes.md` explica cómo ponerla, rellenarla, encajarla y comprobarla; `assets/scenes/demo/<id>.html` es un ejemplo funcional de cada una.

### 7. Plenitud

* Mira cada pantalla de desktop como un póster: **una cosa dominante** y el contenido que lleva.
* Una pantalla mayormente vacía es una sección infraconstruida, no un descanso.
* Un descanso deliberado por página es el límite; el inspector falla una página con dos o más pantallas casi vacías.
* El texto corrido va en el ink completo a 16 px o más; el gris es para notas secundarias.

### 8. El frame

* La página tiene **un par de márgenes exteriores iguales** en todos los anchos.
* Cada sección usa el ancho que la página ha establecido: si su texto está en la mitad izquierda, la mitad derecha lleva su imagen, su segunda columna, su lista o su figura, nunca nada.
* La asimetría vive dentro del grid, nunca en el frame: un vacío solo se coloca cuando algo lo cierra al otro lado.
* Ningún tramo de más de media pantalla puede estar vacío.
* Secciones a 120–240 px en desktop.
* Cada sección por la que el visitante vino abre con un encabezado que la nombra.
* La primera pantalla lleva el título de la página en un `h1`.

> [!important] Lo que la gente nombra primero cuando una página se ve mal
> "No hay título claro", "espacio vacío aleatorio", "todo está empujado a un lado". El inspector mide cada una de estas cosas.

### 9. Estados y semántica

* Enlaces y botones reales.
* Focus visible.
* Texto alternativo.
* Layout de teléfono que sea su propia composición.
* Cada control, incluida la cosa viva, funciona solo con teclado en orden de lectura y tiene nombre accesible: su etiqueta visible o `aria-label` cuando solo muestra un icono.

---

## 📚 Referencias de detalle

* `references/type.md`
* `references/colour.md`
* `references/layout.md`
* `references/imagery.md`
* `references/scenes.md`

---

## 🔗 Relacionado

* [[03 Sortear la dirección]]
* [[04 Encontrar la idea]]
* [[05 Escribir el copy]]
* [[07 Gate]]
* [[Laws of the Craft]]
