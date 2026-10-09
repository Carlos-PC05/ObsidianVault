---
alias:
  - Skill /to-questionnaire
  - AI Hero To Questionnaire
tags:
  - ai/skills
  - dev/discovery
  - dev/requirements
  - ai/productivity
source: https://www.aihero.dev/skills-to-questionnaire
type: skill-note
---
> [!abstract] **Propósito**  
> Convierte una propuesta, idea o borrador de requerimientos en un **cuestionario estructurado y asíncrono**. Permite recopilar respuestas claras de clientes, *product managers* o stakeholders antes de avanzar al diseño técnico.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Usa `/to-questionnaire` cuando tengas un documento o descripción de funcionalidad que contenga lagunas, pero prefieras enviar una **lista de preguntas clara e independizable** en lugar de hacer un interrogatorio en tiempo real.
- **Formato asíncrono:** Ideal cuando no puedes realizar una sesión interactiva de preguntas y respuestas (`/grill-me`) y necesitas enviar las dudas por email, issue o formulario.

| Situación | Skill recomendada |
| :--- | :--- |
| Necesitas enviar las dudas de forma asíncrona a un stakeholder | `/to-questionnaire` |
| Quieres un interrogatorio interactivo en vivo dentro del chat | `/grill-me` |
| Quieres contrastar la propuesta contra el código existente del repo | `/grill-with-docs` |
| Ya tienes todas las respuestas y quieres escribir el documento técnico | `/to-spec` |

---

## 🔄 Proceso de Generación

1. **Análisis de Requerimientos:** Revisa el texto, propuesta o idea inicial para detectar vacíos de lógica, restricciones no definidas o casos de borde (*edge cases*).
2. **Categorización de Preguntas:** Organiza las dudas por temas (Lógica de Negocio, UX/UI, Integraciones/APIs, Permisos/Seguridad).
3. **Formulación de Opciones:** Acompaña las preguntas ambiguas con opciones sugeridas o alternativas (A, B, C) para facilitar una respuesta rápida y precisa por parte del usuario.
4. **Formato Limpio:** Exporta una plantilla de cuestionario lista para copiar, responder y devolver.

---

## 💡 Conceptos Clave

- **Preguntas con Opciones Recomendadas:** No se limita a preguntar *"¿cómo debe funcionar X?"*, sino que propone alternativas razonables para reducir la carga cognitiva de quien responde.
- **Preparación para `/to-spec`:** Las respuestas del cuestionario completado sirven como insumo directo para generar la especificación técnica.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **No es un interrogatorio interactivo**  
> Diseñada para **generar un documento de preguntas cerrado**, no para entablar un diálogo de ida y vuelta paso a paso.

- **Evita la sobrecarga:** Prioriza solo las preguntas bloqueantes que impidan avanzar con el diseño técnico o los tickets.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=to-questionnaire
```