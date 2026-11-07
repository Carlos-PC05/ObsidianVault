---
aliases:
  - Taste Skill
  - tasteskill
  - design-taste-frontend
  - Taste Skill v2
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - anti-slop
source: https://www.tasteskill.dev/docs
---
# Taste Skill — Anti-Slop Frontend Skill

> [!abstract] Resumen Ejecutivo
> **tasteskill v2** es una skill de dirección de arte para frontend que sustituye los defaults de los modelos generativos por **decisiones de diseño reales**. Está destilada de un estudio de **247 sitios de galería** (One Page Love, Awwwards Sites of the Day) y organiza el trabajo en un *run* de 10 pasos: leer el brief, construir un argumento, sortear una dirección, encontrar una idea propia del sujeto, escribir el copy, construir, pasar el *gate*, mirar capturas, añadir movimiento y reportar.

---

## 🎯 Objetivo Principal

* **Romper el aspecto "hecho por IA":** evita el titular centrado, la etiqueta pequeña encima, las tres tarjetas con iconos, el degradado suave y la letra monoespaciada decorativa.
* **Dirección investigada, no inventada:** un script reparte direcciones de una librería destilada de sitios reales; tú eliges la que puede llevar la idea del sujeto.
* **Estilo de casa medido:** las reglas no se confían al criterio del modelo; `inspect.mjs` las mide en un navegador real a 1440, 768 y 390 px.
* **Mirar lo construido:** capturas, rúbrica de jurado, corrección y otra mirada. El *gate* no sustituye al ojo.

---

## 🧠 Cómo funciona (las cuatro palancas)

1. **El script reparte, tú eliges.** `scripts/draw.mjs` reparte tres de veinte direcciones investigadas. Eliges la que puede llevar algo que solo tiene este cliente.
2. **La idea sale del sujeto.** Lo que separa los mejores sitios de los olvidables es un concepto tomado de la cosa misma, no del estilo.
3. **House style sin adornos.** Las reglas se comprueban en navegador con `scripts/inspect.mjs`, no se esperan.
4. **Mirar lo hecho.** Capturas a tres anchos, rúbrica, corregir, volver a mirar.

> [!warning] La advertencia del propio autor
> Quitar adornos es solo la mitad. Una página que perdió etiquetas, reglas y tarjetas y no ganó nada en su lugar se lee como vacía. La otra mitad es **sustancia**: tipografía más grande, campos de color completos, imágenes reales del mundo del sujeto y cada sección con su contenido completo. No cambies sustancia por contención.

---

## 🔀 Página o pantalla de app

| Tipo | Qué es | Qué run sigue |
| :--- | :--- | :--- |
| **Página** | Se lee y se hace scroll: landing, marketing, portfolio, evento, restaurante, one-pager. | El run de 10 pasos de esta carpeta. |
| **App screen** | Se trabaja en ella: dashboard, admin, analytics, settings, billing, inbox, wallet, lista con filtros, editor. | Su propio run en [[App Screens]], con `draw.mjs --app` e `inspect.mjs --app`. |

> [!info] Sitio de producto que muestra su app
> La página sigue el run de página; los fragmentos de interfaz que muestra siguen las reglas de app screen. Se reparte el estilo de app una vez, en su propia carpeta, manteniendo paleta y tipografía de la página.

---

## 🔄 El run

| Paso | Nota | Qué decide |
| :--- | :--- | :--- |
| 1 | [[01 Leer el brief]] | Qué es real: sujeto, audiencia, acción, stack, marca, imágenes. |
| 2 | [[02 Escribir el argumento]] | Qué secciones existen y en qué orden, como claims con prueba. |
| 3 | [[03 Sortear la dirección]] | Composición, tipo, paleta, hero y escenas. |
| 4 | [[04 Encontrar la idea]] | Qué hace que la página sea de este sujeto y de ningún otro. |
| 5 | [[05 Escribir el copy]] | Titulares, apoyo, acción y contenido completo antes del markup. |
| 6 | [[06 Construir]] | Tokens, hero, secciones, imágenes, live thing, escenas, frame, estados. |
| 7 | [[07 Gate]] | `lint.mjs`, `inspect.mjs`, `scenes.mjs`. Cero FAIL. |
| 8 | [[08 Mirar]] | Crítica de capturas, rúbrica, corregir los tres problemas principales. |
| 9 | [[09 Movimiento]] | Capa de movimiento real, no un fade. |
| 10 | [[10 Informe]] | Qué se repartió, qué se redibujó, qué se encontró y qué falta. |

---

## 📦 Skills del bundle

El repo incluye varios `SKILL.md`. El install por defecto trae **v2 experimental**; el resto se activa según el stack o el tipo de trabajo.

| Skill | Para qué |
| :--- | :--- |
| `design-taste-frontend` | v2 experimental, la skill principal. |
| `design-taste-frontend-v1` | v1 legacy, solo si v2 rompe un flujo existente. |
| `gpt-tasteskill` | Variante más estricta para GPT y Codex. |
| `redesign-skill` | Auditoría de sitios existentes en seis categorías. |
| `soft-skill` | UI calmada, más whitespace y movimiento tipo spring. |
| `minimalist-skill` | UI editorial de producto inspirada en Notion y Linear. |
| `brutalist-skill` | Tipografía suiza, estructura cruda y contraste duro. |
| `output-skill` | Anti-laziness: bloquea placeholders y secciones a medias. |
| `stitch-skill` | Puente con Google Stitch y export opcional de `DESIGN.md`. |
| `image-to-code-skill` | Pipeline image-first: referencias, análisis e implementación. |
| `imagegen-frontend-web` | Frames horizontales de secciones para handoff. |
| `imagegen-frontend-mobile` | Frames de pantallas móviles para handoff. |
| `brandkit` | Tableros de identidad de marca, solo imagen. |

Detalle completo en [[Skills del Bundle]].

---

## 🚀 Instalación rápida

```bash
# v2 experimental (install por defecto)
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"

# Bundle completo
npx skills add Leonxlnx/taste-skill

# Codex
npx skills add Leonxlnx/taste-skill -a codex
```

Requisitos de los scripts: **Node 22+**, Chrome/Edge/Chromium instalado y red para `assets.mjs`. No instalan dependencias ni usan API keys. Ver [[Instalación]].

---

## 📌 Chuleta rápida

| Quiero... | Ir a |
| :--- | :--- |
| Entender cómo lee el brief v2 | [[Cómo piensa v2]] |
| Empezar una web nueva | [[Prompts]] → prompt greenfield |
| Rediseñar una web existente | [[Prompts]] → prompt de rediseño |
| Saber qué está prohibido | [[Anti-Slop]] |
| Conocer las reglas fijas | [[House Rules]] y [[Laws of the Craft]] |
| Revisar locks y hero | [[Locks y Hero]] |
| Trabajar en un dashboard | [[App Screens]] |
| Resolver marca, legacy o componentes sueltos | [[Casos Especiales]] |

---

## ⚠️ Estabilidad

> [!warning] v2 sigue siendo experimental
> La instalación por defecto ya es v2 y se recomienda, pero aún itera. El nombre de instalación (`design-taste-frontend`) se mantiene estable; la numeración de secciones y la redacción de reglas pueden cambiar antes de v2.0.0 stable.

---

## 🔗 Fuentes

* Documentación: https://www.tasteskill.dev/docs
* Guía de prompts: https://www.tasteskill.dev/guide
* Changelog: https://www.tasteskill.dev/changelog
* Repo: https://github.com/Leonxlnx/taste-skill
* SKILL.md v2: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
