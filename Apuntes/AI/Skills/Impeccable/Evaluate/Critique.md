---
aliases:
  - Impeccable Critique
  - Crítica de UI
  - Feedback Visual
tags:
  - dev/ai
  - design-system
  - impeccable
  - ui-ux
  - critique
source: https://impeccable.style/docs/critique/
---

# `/impeccable critique` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable critique` es el comando diseñado para **ofrecer una evaluación experta y constructiva enfocada en la UX, la jerarquía visual y la estética** del diseño actual. A diferencia de una auditoría técnica (`/audit`), la crítica actúa como el feedback de un Principal UI/UX Designer: analiza la usabilidad, la claridad del mensaje y la calidad perceptiva para proponer mejoras conceptuales y de interacción antes o después de la implementación.

---

## 🎯 Objetivo Principal

* **Feedback de Nivel Senior:** Aporta una perspectiva crítica sobre la experiencia de usuario, la claridad de la llamada a la acción (CTA) y el flujo de información.
* **Evaluación de Intención y Tono:** Revisa si la interfaz transmite la personalidad y valores definidos en `PRODUCT.md` y `DESIGN.md`.
* **Propuestas de Mejora Concretas:** Va más allá de señalar errores; sugiere alternativas concretas de maquetación, composición y comportamiento de UI.

---

## 🔍 ¿Cuándo utilizarlo?

* **Fase de Concepto o Wireframe:** Para validar la estructura de una nueva pantalla o flujo antes de escribir código definitivo.
* **Rediseño de Vistas Clave:** Cuando una pantalla funciona técnicamente pero se siente "plana", confusa o sobrecargada para el usuario.
* **Segunda Opinión:** Para obtener retroalimentación estética y de UX sobre un componente complejo antes de pasarlo a producción.

---

## 📋 Flujo de Trabajo

1. **Lectura Estratégica:** Examina el código o la estructura del componente a la luz de las metas del producto (`PRODUCT.md`).
2. **Análisis de UX/UI:** Evalúa la carga cognitiva, el flujo focal, la alineación visual, la legibilidad y el ritmo de la página.
3. **Presentación de Observaciones:** Proporciona un desglose estructurado con observaciones sobre lo que funciona, lo que no y recomendaciones accionables.

---

## 💡 ¿En qué se diferencia de `/impeccable audit`?

| Comando | Enfoque | Resultado |
| :--- | :--- | :--- |
| `/impeccable audit` | **Técnico y Normativo:** Revisa sintaxis, tokens, accesibilidad (WCAG) y cumplimiento estricto del código. | Lista de errores, advertencias y correcciones de código. |
| `/impeccable critique` | **Conceptual y de UX:** Revisa composición, jerarquía visual, flujo de usuario y valor estético. | Feedback cualitativo, alternativas de diseño y mejoras de usabilidad. |

---

## 🚀 Uso Rápido

```bash
# Solicitar una crítica de diseño para un archivo o pantalla
/impeccable critique [componente o vista]
```
