---
aliases:
  - Taste Skill Anti-Slop
  - Anti-Slop
  - Ban list
  - Lista de prohibiciones
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - anti-slop
source: https://www.tasteskill.dev/docs#anti-slop
---
# Anti-Slop — Taste Skill

> [!abstract] Resumen Ejecutivo
> La **Section 9** del `SKILL.md` trae una lista estricta de patrones que hacen que los frontends generados se parezcan todos entre sí. El agente las aplica en **cada** generación. No es una lista de sugerencias: es un sistema de prohibiciones con un reemplazo para cada una.

---

## 🎯 Objetivo Principal

* **Quitar los tells:** los patrones que delatan una página hecha por un modelo.
* **Sustituir, no solo prohibir:** cada prohibición tiene una alternativa mejor.
* **Aplicar siempre:** no depende de la dirección repartida.

---

## 🚫 Lista de prohibiciones

| Prohibido | Detalle | En su lugar |
| :--- | :--- | :--- |
| **Em-dashes y en-dashes** | Prohibidos en todo el copy de salida: titulares, eyebrows, pills, cuerpo, citas, atribución, captions, botones, alt text. | Un guion normal o reestructurar la frase. |
| **Eyebrows con numeración de sección** | `00 / INDEX`, `001 · Capabilities`, `06 · how it works`. | Un eyebrow que nombre el tema en lenguaje plano. |
| **Etiquetas de versión en el hero** | `V0.6`, `BETA`, `INVITE-ONLY`, salvo que el brief sea explícitamente un lanzamiento de producto. | Quitarlas o moverlas fuera del hero. |
| **Captions de crédito de foto como decoración** | `Field study no. 12 · Ines Caetano` bajo imágenes de stock. | Atribución real a fotógrafos reales, solo cuando corresponda. |
| **Tiras de texto decorativo en el hero** | `BRAND. MOTION. SPATIAL.`, `TYPE / FORM / MOTION` y otras tiras mono-caps al pie del hero. | Nada, o contenido real. |
| **Pills superpuestas sobre imágenes** | Etiquetas o tags flotando encima de fotos. | Una caption directamente debajo de la imagen. |
| **Footers de versión en páginas de marketing** | `v1.4.2`, `Build 0048`, `last sync 4s ago`. | Los fixtures de devtool no van en landing o portfolio. |
| **Tiras de locale, ciudad, hora o clima** | `Lisbon 14:23 · 18°C` y similares. | Prohibidas para el 99 % de los briefs. |
| **Scroll cues** | `Scroll`, iconos de flecha hacia abajo, `Scroll to explore`. | El usuario ya está en el hero; sabe cómo funciona el scroll. |
| **Puntos de estado decorativos** | Cero por defecto. | Solo cuando comunican estado semántico real, y como máximo uno por sección. |
| **Bordes superior e inferior en cada fila** | `border-t` + `border-b` prohibidos en listas largas y tablas de especificaciones. | Cards, tabs, scroll-snap pills, marquee o carrusel. |
| **UI de producto falsa hecha con divs** | Listas de tareas, terminales o dashboards falsos construidos con divs estilados. | Capturas reales o imágenes generadas. |
| **Filas de tres tarjetas iguales** | Prohibidas por defecto. | Zig-zag de dos columnas, grids asimétricos o alternativas con scroll pinned. |
| **Degradados púrpura de IA y mesh blobs** | Prohibidos por defecto. | Bases neutras con un accent de alto contraste. La regla LILA solo aplica si la marca es explícitamente púrpura. |
| **Ilustraciones SVG decorativas hechas a mano** | Muy desaconsejadas por defecto. | Solo una marca geométrica simple o cuando el brief lo pida explícitamente. |
| **Scroll listeners manuales** | `window.addEventListener('scroll')` prohibido en JS. | Motion `useScroll`, GSAP ScrollTrigger, IntersectionObserver o CSS scroll-driven animations. |

---

## 🧪 Auditorías que pide el prompt

El prompt de v2 incluye comprobaciones en escritura:

* **Em-dash audit:** cero em-dashes `U+2014` y en-dashes `U+2013` en cualquier parte.
* **Pre-Flight Check:** Section 14, cada casilla en Pass o Fail con justificación de una línea.
* **Section-Layout-Repetition audit:** lista la familia de layout de cada sección.
* **Hero discipline audit:** líneas del titular, palabras del subtexto, visibilidad del CTA.

> [!danger] Cualquier Fail bloquea la entrega.

---

## 💡 Por qué existe

> [!note] El problema no es que algo esté "mal"
> El problema es que nada fue **decidido**. La lista quita los defaults que el modelo elige sin pensar y obliga a que cada elemento tenga una razón.

---

## 🔗 Relacionado

* [[House Rules]]
* [[Locks y Hero]]
* [[Cómo piensa v2]]
* [[07 Gate]]
