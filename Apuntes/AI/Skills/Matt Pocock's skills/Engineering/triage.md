---
alias:
  - Skill /triage
  - AI Hero Triage
tags:
  - ai/skills
  - dev/triage
  - dev/workflow
  - ai/unkeep
source: https://www.aihero.dev/skills-triage
type: skill-note
---
> [!abstract] **Propósito**  
> Evalúa, clasifica y prioriza **problemas entrantes, bugs reportados, solicitudes de features o items del backlog**. Determina rápidamente qué requiere atención inmediata, qué necesita más información y cuál es la skill adecuada para abordar cada elemento.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Usa `/triage` cuando tengas una lista de tareas descifradas sin ordenar, nuevos reportes de bugs en el backlog o cuando necesites priorizar el trabajo entrante antes de planificar.
- **Punto de entrada:** Funciona como un primer filtro para categorizar problemas antes de pasarlos a refinamiento profundo o implementación.

| Situación | Skill recomendada |
| :--- | :--- |
| Tienes un reporte o ítem sin clasificar y no sabes qué hacer con él | `/triage` |
| Quieres indagar la causa raíz de un bug confirmado | `/diagnosing-bugs` |
| Quieres convertir un problema priorizado en un spec técnico | `/to-spec` |
| Quieres dividir una propuesta clara en tickets ejecutables | `/to-tickets` |

---

## 🔄 Proceso de Triaje

1. **Análisis del Ítem:** Lee el reporte, issue o solicitud entrante para entender el problema o la necesidad real.
2. **Evaluación de Completes y Claridad:** Determina si la solicitud tiene suficiente información o si faltan pasos de reproducción / contexto.
3. **Clasificación y Priorización:** Asigna una categoría (Bug, Feature, Deuda técnica, Documentación) y un nivel de severidad/prioridad.
4. **Enrutamiento (*Routing*):** Recomienda la siguiente acción o la skill adecuada del ecosistema para procesarlo (ej. mandar a `/diagnosing-bugs` o `/grill-me`).

---

## 💡 Conceptos Clave

- **Filtro de Entrada:** Evita meter trabajo ambiguo o irrelevante directamente en las fases de especificación e implementación.
- **Separación de Responsabilidades:** Decidir *qué vale la pena hacer y con qué prioridad* es un paso distinto a *diseñar la solución* o *escribir código*.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **No diseña ni resuelve**  
> Su objetivo es **organizar, evaluar y enrutar**, no redactar arquitecturas ni solucionar problemas en código.

- **Petición de Claridad:** Si un reporte es demasiado vago, detiene el proceso solicitando los datos faltantes antes de clasificarlo a ciegas.
- **Gobernanza del Backlog:** Mantiene el foco en responder *"¿Es esto un problema real, qué tan urgente es y cuál es el siguiente paso?"*.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=triage
```

