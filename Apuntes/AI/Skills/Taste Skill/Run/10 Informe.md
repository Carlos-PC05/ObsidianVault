---
aliases:
  - Taste Skill Paso 10
  - Informe final
  - Report
tags:
  - dev/ai
  - design-system
  - taste-skill
  - taste-skill/run
  - frontend
  - reporting
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# 10 — Informe

> [!abstract] Resumen Ejecutivo
> No se reporta hasta que todo lo anterior sea verdad. El informe cuenta qué dirección se repartió y qué se redibujó, la idea y la cosa viva, las puntuaciones finales y lo que sigue corto, cada imagen encontrada con su crédito y qué contenido de muestra hay que sustituir antes de publicar. No se declara una comprobación que no se ejecutó.

---

## 🎯 Objetivo Principal

* **Cerrar con evidencia:** solo se reporta cuando el gate y la mirada están hechos.
* **Dejar rastro:** dirección, redraws, idea, imágenes y créditos.
* **Decir la verdad:** lo que falta y lo que hay que sustituir antes de lanzar.

---

## ✅ Checklist antes de reportar

* [ ] `inspect.mjs` muestra **0 FAIL** a 1440, 768 y 390 en la versión final.
* [ ] Has abierto **todas** las capturas que listó después del último cambio, no solo la primera pantalla.
* [ ] Con scroll scenes: `scenes.mjs` corrió en la página final sin problemas y leíste cada hoja (desktop, teléfono, reduced motion).
* [ ] Un desconocido que lee la primera pantalla durante cinco segundos puede decir qué es y qué hacer.
* [ ] Cada sección tiene encabezado; los márgenes coinciden en ambos lados; ningún tramo de la página está vacío.
* [ ] La crítica de la última ronda está escrita en la forma de `references/critique.md` §4.

---

## 🗣️ Qué contarle al usuario

* La **dirección repartida** (nombre y seed) y los redraws con sus razones.
* La **frase de la idea** y la **cosa viva**.
* Las **puntuaciones finales** de la última ronda de crítica y lo que sigue quedando corto.
* Cada **imagen y clip encontrados** con título, creador, licencia y página de origen (`assets.mjs --credits` imprime la lista) y dónde está su crédito en la página.
* Qué **fotografías del bundle** y qué dibujos sustituyen a la fotografía real (para que se puedan reemplazar).
* Qué **contenido de muestra** hay que cambiar antes de lanzar.

---

## ⚠️ Regla final

> [!danger] No declares una comprobación que no ejecutaste
> El informe es parte del trabajo, no un adorno. Si el inspector no pudo correr, dilo. Si las colecciones abiertas no se pudieron buscar, dilo. Si una imagen es un dibujo, dilo.

---

## 🔗 Relacionado

* [[07 Gate]]
* [[08 Mirar]]
* [[09 Movimiento]]
* [[Casos Especiales]]
