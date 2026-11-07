---
aliases:
  - Taste Skill Paso 7
  - Gate
  - inspect.mjs
  - lint.mjs
  - scenes.mjs
tags:
  - dev/ai
  - design-system
  - taste-skill
  - taste-skill/run
  - frontend
  - testing
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# 07 — Gate

> [!abstract] Resumen Ejecutivo
> El *gate* comprueba que no se haya medido nada prohibido. `lint.mjs` escanea el código fuente, `inspect.mjs` abre la página en un navegador real a 1440, 768 y 390 px y mide la página, y `scenes.mjs` fotografía cada scroll scene. Se corrige **cada FAIL**; un WARN se trata como error salvo que puedas defenderlo ante un diseñador en una frase.

---

## 🎯 Objetivo Principal

* **Medir, no esperar:** las reglas de la casa se comprueban en navegador real.
* **Cero FAIL:** es la condición de entrega.
* **Inspeccionar lo que recibe el visitante:** con build, el último gate se corre sobre producción, no solo sobre el dev server.

---

## 🚀 Comandos

```bash
# 1. Escanear el código fuente
node <skill>/scripts/lint.mjs <source files or folder>

# 2. Abrir la página en navegador real y medirla
node <skill>/scripts/inspect.mjs <url or path/to/index.html>

# 3. Fotografiar las scroll scenes (solo si la página las tiene)
node <skill>/scripts/scenes.mjs <url or path/to/index.html>
```

---

## 🔍 Qué hace cada uno

### `lint.mjs`

* Escanea el código fuente en busca de patrones prohibidos y frases stock.
* Node 18+.

### `inspect.mjs`

* Abre la página a **1440, 768 y 390 px**.
* Mide: número de líneas del titular, tamaños de tipo, fuentes realmente cargadas, colores, reglas, sombras, degradados, contraste, overflow y el frame (márgenes iguales, secciones amontonadas a un lado, tramos en blanco, título, secciones sin título, texto contra el borde).
* Guarda capturas en `.taste-skill/inspect/`.
* Con scroll scenes, también filma el scroll completo (`desktop-film-NN.jpg`).

### `scenes.mjs`

* Fotografía cada escena al 0, 10 … 100 % de su hold, en desktop, en teléfono y con reduced motion.
* Una escena que nunca arrancó o que descansa en desktop es un **FAIL**.

---

## ✅ Reglas del gate

* **Corrige cada FAIL.**
* Trata cada **WARN** como incorrecto salvo que puedas defenderlo en una frase.
* Repite hasta que no haya FAIL.
* No discutas con el medidor ni le escondas cosas.
* El inspector sirve archivos locales con un servidor temporal; si la página también debe abrirse directamente desde el sistema de archivos, abre `index.html` así una vez antes de terminar.
* Con build, corre el último gate sobre la build de producción (`vite build && vite preview`, `next build && next start`, o el `index.html` construido). Los minificadores y bundlers cambian lo que se envía.
* El inspector avisa cuando está mirando un dev server.

---

## ⚠️ Qué no significa

> [!warning] Pasar el gate no significa que la página sea buena
> Significa que no se midió nada prohibido. La calidad se juzga en el paso 8, mirando las capturas.

---

## 🔗 Relacionado

* [[06 Construir]]
* [[08 Mirar]]
* [[House Rules]]
* [[Anti-Slop]]
