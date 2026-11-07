---
aliases:
  - Taste Skill Bundle
  - Skills del bundle
  - Variantes de Taste Skill
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - skills
source: https://www.tasteskill.dev/docs#skills
---
# Skills del Bundle — Taste Skill

> [!abstract] Resumen Ejecutivo
> El repo de Taste Skill incluye varios archivos `SKILL.md`. El install por defecto trae **v2 experimental**; el resto se activa según el stack o el tipo de trabajo. Esta nota resume qué hace cada variante y cuándo elegirla.

---

## 🎯 Objetivo Principal

* **Elegir la skill correcta:** no todas las variantes sirven para el mismo trabajo.
* **Evitar duplicar reglas:** v2 ya cubre la mayoría de casos; las otras son especializaciones.
* **Saber qué es legacy:** v1 solo se usa si v2 rompe un flujo existente.

---

## 📦 Variantes

| Skill | Estado | Para qué |
| :--- | :--- | :--- |
| **taste-skill** | v2 experimental | Reescritura sustancial de 2026. Lee el brief, infiere la dirección, evita el aspecto templado. Brief inference, design-system map, prohibición completa de em-dash, esqueletos GSAP, protocolo de auditoría de rediseño y pre-flight estricto. Install: `design-taste-frontend`. |
| **taste-skill-v1** | Legacy | La v1 original, preservada para proyectos que dependen de su comportamiento exacto. Install: `design-taste-frontend-v1`. Solo si v2 rompe algo específico. |
| **gpt-tasteskill** | Stable | Variante más estricta para GPT y Codex: mayor varianza de layout, dirección de movimiento más fuerte y controles anti-slop más estrictos. |
| **redesign-skill** | Stable | Auditoría de sitios existentes en seis categorías: encuentra patrones genéricos y guía las correcciones. |
| **soft-skill** | Stable | UI calmada y pulida: contraste más suave, más whitespace, movimiento tipo spring. |
| **minimalist-skill** | Stable | UI editorial de producto inspirada en Notion y Linear: paleta contenida y jerarquía más ajustada. |
| **brutalist-skill** | Stable | Tipografía suiza, estructura cruda y contraste más duro. |
| **output-skill** | Stable | Reglas anti-laziness para agentes: bloquea comentarios placeholder, secciones saltadas y output a medias. |
| **stitch-skill** | Stable | Puente con Google Stitch y export opcional de `DESIGN.md`. |
| **image-to-code-skill** | Stable | Pipeline image-first: genera frames de referencia, los analiza y luego implementa el frontend para que coincida. |
| **imagegen-frontend-web** | Stable | Skill solo de imagen: frames horizontales de secciones de web para handoff a Codex, Cursor o Claude Code. |
| **imagegen-frontend-mobile** | Stable | Skill solo de imagen: frames de pantallas móviles para el mismo handoff. |
| **brandkit** | Stable | Skill solo de imagen: tableros de identidad de marca para el mismo handoff. |

---

## 🧭 Cómo elegir

| Situación | Skill |
| :--- | :--- |
| Web nueva, landing, portfolio, marketing | `taste-skill` (v2) |
| Proyecto que depende del comportamiento exacto de v1 | `taste-skill-v1` |
| GPT o Codex, quieres más varianza y checks más duros | `gpt-tasteskill` |
| Sitio existente que necesita auditoría y corrección | `redesign-skill` |
| UI calmada, más aire, movimiento spring | `soft-skill` |
| Producto editorial tipo Notion/Linear | `minimalist-skill` |
| Estética suiza, cruda, alto contraste | `brutalist-skill` |
| El agente deja placeholders o secciones a medias | `output-skill` |
| Trabajas con Google Stitch | `stitch-skill` |
| Tienes referencias visuales y quieres implementarlas | `image-to-code-skill` |
| Necesitas frames de web para handoff | `imagegen-frontend-web` |
| Necesitas frames móviles para handoff | `imagegen-frontend-mobile` |
| Necesitas un tablero de identidad de marca | `brandkit` |

---

## ⚠️ Estabilidad

> [!warning] v2 experimental
> El install por defecto es v2. El nombre de instalación (`design-taste-frontend`) se mantiene estable, pero la numeración de secciones y la redacción de reglas pueden cambiar antes de v2.0.0 stable.

---

## 🔗 Relacionado

* [[Instalación]]
* [[Taste Skill]]
* [[Prompts]]
