---
aliases:
  - Taste Skill Laws
  - Laws of the Craft
  - Leyes del oficio
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - typography
  - layout
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# Laws of the Craft — Taste Skill

> [!abstract] Resumen Ejecutivo
> Las leyes cortas del oficio: tipografía, espacio, color, imágenes y componentes. Los números vienen del estudio de 247 sitios salvo que se indique que son house style. Son la forma breve de las referencias; el detalle está en `references/type.md`, `colour.md`, `layout.md` e `imagery.md`.

---

## 🔤 Tipo

* **Dos o tres tamaños hacen el trabajo visible:** uno muy grande, uno pequeño, nada en medio.
* La mediana de ratio titular-cuerpo en los sitios estudiados es **4,3**; los mejores van mucho más allá (tipo más grande: mediana **8,4vw** en los top contra **5,0vw** en los débiles).
* **Display:** tracking de **-0,02 a -0,05em**, leading **0,85–1,05**.
* **Texto:** **16–20 px**, leading **1,45–1,6**, **45–75 caracteres** por línea.
* **Cinco tamaños** en una página es el techo.
* **Una familia** es lo normal (un tercio de los sitios estudiados); **dos** es el límite.
* **Alineado a la izquierda** por defecto. Centrar como máximo un momento.

---

## 📐 Espacio

* **Un grid, un par de márgenes ajustados (16–32 px)** usado en todas partes; las cosas grandes corren hasta los bordes.
* Los bloques de texto comparten **dos o tres bordes izquierdos** en toda la página.
* **Asimetría dentro del grid, nunca en el frame:** los dos márgenes exteriores son iguales, y un titular descentrado se responde con algo al otro lado del grid.
* El gap entre secciones es claramente mayor que cualquier gap interno: **120–240 px** en desktop (la mediana del estudio ronda **240**).
* Cada sección tiene una composición distinta de sus vecinas; ninguna es una card grid.

---

## 🎨 Color

* **Un ground, un ink, un accent.** El accent aparece en la acción y quizá en una cosa más.
* **Texto corrido en el ink completo.** Una página en gris suave se lee sin terminar.
* Las superficies se separan con un **pequeño paso de tono**, nunca con una línea o una sombra.
* Una sección puede cambiar **todo el campo de color**. Nada se desvanece hacia otra cosa.

---

## 🖼️ Imágenes

* **Una imagen dominante gana a seis pequeñas.** En los sitios mejor puntuados, la imagen del hero cubre al menos la mitad de la primera pantalla el **68 %** de las veces.
* **Mismos ratios de recorte, mismo tratamiento, esquinas cuadradas** salvo que la dirección diga otra cosa (**73 %** de los sitios estudiados dejan la imagen cuadrada).
* **Ninguna imagen es mejor que un placeholder, un icono o una forma abstracta.** Una imagen dibujada para este sujeto, en sus colores y con cuidado, es mejor que cualquiera de las tres. Una imagen real de su mundo, encontrada y acreditada, es mejor todavía.
* **Cada imagen encontrada se descarga al proyecto, se acredita en la página y se lista en el informe.** Ninguna se presenta como del cliente.
* **Nada se pone encima de un titular o una declaración** (el inspector lo falla).

---

## 🧩 Componentes

* **Botones:** planos, un color de relleno, o cuadrados o completamente redondos, decidido una vez. En los top: **52 % cuadrados**, **23 % pill**; el radio "suave" de 9–16 px es el default de los sitios débiles. La mayoría de los heroes fuertes no tienen botón o tienen uno.
* **Navegación:** wordmark, **tres a cinco enlaces planos**, una acción. Sin chrome de barra.
* **Un control explica cómo se usa por su forma, su etiqueta y su lugar.** Cuando parece necesitar una frase que explique cómo operarlo, rediseña el control en lugar de añadir la frase. Lo que acepta un campo es distinto: primero elimina el límite donde puedas (un date picker o un parser que acepte formatos comunes). Un límite que permanece, como reglas de contraseña o tipo y tamaño de archivo, queda visible junto al campo y ligado con `aria-describedby`.
* **El footer es un cierre diseñado, no un sitemap:** en la mayoría de los sitios fuertes, el nombre vuelve a ancho completo.

---

## 💡 Cómo usar estas leyes

> [!important] Son la forma corta
> Cuando necesites el detalle detrás de una decisión, lee la referencia correspondiente. No las uses como receta rígida: la dirección repartida manda, y estas leyes evitan que la dirección se degrade.

---

## 🔗 Relacionado

* [[House Rules]]
* [[03 Sortear la dirección]]
* [[06 Construir]]
* [[08 Mirar]]
