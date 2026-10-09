---
alias:
  - Skill /teach
  - AI Hero Teach
tags:
  - ai/skills
  - dev/learning
  - dev/mentorship
  - ai/productivity
source: https://www.aihero.dev/skills-teach
type: skill-note
---
> [!abstract] **Propósito**  
> Explica conceptos técnicos, fragmentos de código, patrones de diseño o arquitecturas complejas adoptando un **rol educativo y didáctico**. Se enfoca en la comprensión profunda del usuario mediante explicaciones progresivas, ejemplos claros y analogías sin realizar cambios directos en el código.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Usa `/teach` cuando encuentres un concepto desconocido en la base de código, no entiendas cómo funciona un patrón en particular o quieras aprender la teoría detrás de una implementación.
- **Enfoque pedagógico:** Prioriza el aprendizaje del desarrollador sobre la resolución automática de tareas.

| Situación | Skill recomendada |
| :--- | :--- |
| Quieres entender *por qué* o *cómo* funciona un concepto/código | `/teach` |
| Quieres estructurar un plan de orientación para abordar un repo | `/wayfinder` |
| Quieres que la IA escriba la solución en código con TDD | `/implement` |
| Quieres revisar si un cambio cumple con los estándares | `/code-review` |

---

## 💡 Estrategia de Enseñanza

1. **Desglose Progresivo:** Explica desde los fundamentos básicos hasta los detalles avanzados o casos de borde.
2. **Ejemplos Prácticos:** Acompaña la teoría con fragmentos de código mínimos y aislados (*sandboxes* o ejemplos simplificados).
3. **Verificación de Comprensión:** Realiza preguntas breves o plantea pequeños ejercicios para confirmar que el concepto quedó claro.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **Modo Mentor (No Executor)**  
> Esta skill **no aplica cambios ni modifica archivos** en el repositorio. Su único objetivo es transmitir conocimiento e instruir.

- **Adaptado al contexto:** Utiliza la base de código actual como material didáctico siempre que sea posible para que las explicaciones sean 100% relevantes al proyecto.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=teach
```