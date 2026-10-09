---
alias:
  - Skill /research
  - AI Hero Research
tags:
  - ai/skills
  - dev/research
  - dev/discovery
  - matt-pocock
source: https://www.aihero.dev/skills-research
type: skill-note
---
> [!abstract] **Propósito**  
> Investiga **librerías externas, APIs, patrones o posibilidades técnicas** desconocidas antes de diseñar o implementar una solución. Genera un **documento de investigación con hallazgos concretos y código de prueba funcional** en un entorno aislado, eliminando las alucinaciones del modelo sobre dependencias de terceros.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Llama a `/research` cuando necesites integrar una API externa, elegir entre varias librerías, o validar si una tecnología permite resolver un problema concreto sin adivinar.
- **Ideal para:** Reducir el riesgo técnico en decisiones de dependencias o integraciones antes de escribir la especificación (`/to-spec`).

| Situación | Skill recomendada |
| :--- | :--- |
| Incertidumbre sobre librerías externas o APIs de terceros | `/research` |
| Incertidumbre sobre tu propia base de código existente | `/wayfinder` |
| Dudas de UX o modelo de estado interno que quieres validar rápido | `/prototype` |
| Requisitos funcionales de producto ambiguos | `/grill-with-docs` / `/grill-me` |

---

## 🔍 ¿Cómo funciona el proceso de Investigación?

1. **Definición de Pregunta e Hipótesis:** Acota la duda técnica a responder (ej. *"¿Soporta la API X streaming con WebSockets?"* o *"¿Cuál librería de tablas rinde mejor con 10k filas?"*).
2. **Experimentación en Aislamiento:**
   - Crea un entorno de pruebas aislado (sandbox, carpeta temporal o rama de investigación) sin contaminar el proyecto real.
   - Instala paquetes y ejecuta pequeños scripts reales para comprobar que los métodos y tipos de las librerías realmente existen y funcionan.
3. **Informe de Hallazgos:** Redacta un documento con la recomendación técnica, limitaciones encontradas, trampas/edge cases de la API y un snippet de código validado.

---

## 💡 Conceptos Clave y Flujo

```text
/research ──► /wayfinder ──► /to-spec ──► /to-tickets ──► /implement
```
