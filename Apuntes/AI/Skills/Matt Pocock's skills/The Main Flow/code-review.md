---
alias:
  - Skill /code-review
  - AI Hero Code Review
tags:
  - ai/skills
  - dev/code-review
  - dev/quality
  - ai/the-main-flow
source: https://www.aihero.dev/skills-code-review
type: skill-note
---
> [!abstract] **Propósito**  
> Evalúa el `diff` de Git entre `HEAD` y un punto fijo (rama, commit, tag) utilizando **dos subagentes independientes** que analizan en paralelo: la adherencia a los **estándares de código del repo** y el **cumplimiento de la especificación/ticket original**.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Puedes llamarla manualmente con `/code-review` o dejar que la invoque automáticamente `/implement` al finalizar una tarea.
- **Requiere un punto de referencia fijo:** Siempre debes indicar la referencia base (ej. `main`, `HEAD~3`, `v1.0`).

| Situación | Skill recomendada |
| :--- | :--- |
| Tienes un `diff` y quieres evaluar si está bien construido y hace lo correcto | `/code-review` |
| Quieres buscar bugs específicos (race conditions, null pointer, etc.) | La función nativa de revisión de Claude Code |
| Quieres construir una spec con TDD | `/implement` (que ya llama internamente a esta skill) |
| Todo el código ha sufrido "drift" o degradación arquitectónica | `/improve-codebase-architecture` |

---

## ⚖️ Los Dos Ejes de Revisión

El análisis se separa en dos subagentes para evitar sesgos cruzados (el reporte final **nunca los mezcla ni elige un "ganador"**):

| Eje | Pregunta Clave | Fuentes que lee | Qué reporta |
| :--- | :--- | :--- | :--- |
| **`Standards`** *(¿Está bien construido?)* | ¿Sigue las convenciones y arquitectura del proyecto? | `CODING_STANDARDS.md`, `CONTRIBUTING.md` + 12 *code smells* de Fowler. | Incumplimientos duros de estándares y avisos de mala práctica (*smells*). |
| **`Spec`** *(¿Es lo que se pidió?)* | ¿Cumple exactamente con el ticket/especificación sin scope creep? | Issues vinculadas (`#123`), archivos en `docs/` o `specs/`, o la spec indicada. | Requisitos faltantes o mal implementados, y código extra no solicitado. |

---

## 🛠️ Prerrequisitos y Configuración

1. **Un punto de referencia válido:**
   El diff se calcula como `git diff <punto-fijo>...HEAD` (excluye cambios sin commit / en staging). **Haz commit antes de ejecutar.**
2. **Fuentes para Spec (Búsqueda en orden):**
   - Referencias en los mensajes de commit (`#123`, `Closes #45`).
   - Argumento/Ruta pasado manualmente en el comando.
   - Archivos en `docs/`, `specs/` o `.scratch/` con el nombre de la feature.
   - *Si no encuentra nada:* Omite el eje de `Spec` e informa "sin spec disponible".

---

## ⚠️ Puntos de Atención y Bugs Conocidos

> [!warning] **Recomendación: Ejecutar en una sesión limpia**
> No ejecutes la revisión en el mismo contexto de conversación que acaba de escribir el código; el modelo sufrirá de **sesgo de confirmación**. Abre un chat nuevo para una evaluación objetiva.

- **Conflicto de nombres en Claude Code:** Claude Code incluye un `/code-review` nativo (para buscar bugs). Si instalas esta skill, se solapan. Solución: renombrar la skill en local o desactivar los built-ins de Claude Code.
- **Bucle infinito de subagentes (Bug conocido):** Los subagentes de revisión a veces vuelven a llamar a `/code-review` dentro de su contexto. Se soluciona editando el prompt local añadiendo: *"Do not invoke /code-review or spawn additional agents"*.
- **No es determinista:** Cada ejecución puede sugerir pequeños cambios nuevos. No te obsesiones con ejecutarlo en bucle hasta lograr "cero hallazgos"; úsalo como una guía de pistas y aplica los arreglos que tengan sentido.