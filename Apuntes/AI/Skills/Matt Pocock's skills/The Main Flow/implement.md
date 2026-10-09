---
alias:
  - Skill /implement
  - AI Hero Implement
tags:
  - ai/skills
  - dev/tdd
  - dev/workflow
  - ai/the-main-flow
source: https://www.aihero.dev/skills-implement
type: skill-note
---
> [!abstract] **Propósito**  
> Convierte una especificación o *ticket* cerrado directamente en código funcional **test-first (TDD)**, pasa el typecheck, ejecuta `code-review` y realiza un `commit` en la rama actual. **No reabre discusiones ni rediseña la solución**.

---

## 🎯 ¿Cuándo usarla?

- **Invocación manual:** El agente **nunca** la llama por su cuenta (`disable-model-invocation: true`). Debes invocarla explícitamente con `/implement`.
- **Régimen de 1 ticket por sesión:** La regla de oro es **1 sesión = 1 ticket**. Tras terminar, limpia el contexto antes del siguiente ticket.

| Estado del Trabajo | Skill a usar |
| :--- | :--- |
| Ticket en el tracker (GitHub Issues, etc.) | `/implement #42` (una sesión por ticket) |
| Spec grande sin dividir | `/to-tickets` primero $\rightarrow$ luego `/implement` por ticket |
| Spec pequeña / Plan en la conversación | `/implement` directamente contra la spec/chat |
| Sin definir / Idea previa | `/grill-with-docs` o `/grill-me` |
| Solo comportamiento TDD puntual | `/tdd` directo |

---

## 🔄 Flujo de Ejecución (5 Pasos)

1. **Lectura:** Lee el ticket/spec e identifica las **costuras (*seams*)**.
2. **TDD:** Ejecuta ciclo *red-green* TDD en las costuras preacordadas.
3. **Typecheck & Tests unitarios:** Comprueba tipos y ejecuta test unitarios aislados durante el proceso.
4. **Suite completa:** Corre la suite completa de tests al finalizar.
5. **Cierre:** Ejecuta `code-review` y hace `commit` en la rama actual.

---

## 💡 Conceptos Clave

### 1. Costuras Preacordadas (*Pre-agreed Seams*)
- Las pruebas se escriben en los **límites públicos (seams)** de la aplicación, no dentro de la implementación interna.
- Esto evita que los tests sean frágiles cuando cambia la implementación interna.
- *Atención:* Si las costuras no están definidas en la spec o al inicio del chat, el flujo de TDD puede degradarse a "escribir código normal".

### 2. Flujo en la Cadena Principal
```text
/grill-with-docs ──► /to-spec ──► /to-tickets ──► /implement ──► /code-review
```

