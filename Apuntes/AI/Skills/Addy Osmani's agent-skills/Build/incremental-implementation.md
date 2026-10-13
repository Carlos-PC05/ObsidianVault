---
alias:
  - Skill incremental-implementation
  - Addy Osmani Incremental Implementation
tags:
  - ai/skills
  - addy-osmani
  - ai/build
  - dev/workflow
  - dev/tdd
source: https://github.com/addyosmani/agent-skills/blob/main/skills/incremental-implementation/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Entrega los cambios en **rebanadas verticales finas y verificables**: implementa una pieza, la testea, verifícala, y entonces expande. Cada incremento debe dejar el sistema en un estado **funcionando y testeable**. Es la disciplina de ejecución que hace manejables las features grandes.

---

## 🎯 ¿Cuándo usarla?

- Cualquier cambio que toque **más de un archivo**.
- Construyendo una feature nueva desde un desglose de tareas.
- Refactorizando código existente.
- **Cualquier momento en el que estés a punto de escribir más de ~100 líneas antes de testear.**

**Cuándo NO:** cambios de un solo archivo/función donde el alcance ya es mínimo.

---

## 🔄 El ciclo de incremento

```
┌──────────────────────────────────────┐
│   Implement ──→ Test ──→ Verify ──┐  │
│       ▲                           │  │
│       └───── Commit ◄─────────────┘  │
│              ▼                       │
│          Next slice                  │
└──────────────────────────────────────┘
```

Por cada rebanada: **Implementa** la pieza completa más pequeña → **Testea** (corre la suite, o escribe un test si no hay) → **Verifica** (tests en verde, build OK, check manual) → **Commitea** con mensaje descriptivo → **Siguiente rebanada**, arrastrando, sin reiniciar.

### Tres estrategias de corte

| Estrategia | Cuándo | Cómo |
| :--- | :--- | :--- |
| **Vertical** (preferida) | Siempre que puedas | Un camino completo por el stack: *Slice 1: crear tarea (BD + API + UI básica)* → *Slice 2: listar tareas* → *Slice 3: editar* → *Slice 4: borrar* |
| **Contract-First** | Backend y frontend en paralelo | Slice 0: define el contrato (tipos, interfaces, OpenAPI) → 1a: backend contra el contrato + tests de API → 1b: frontend contra datos mock que casen → 2: integrar y testear E2E |
| **Risk-First** | Cuando hay incertidumbre alta | Ataca lo más arriesgado primero: *Slice 1: probar que la conexión WebSocket funciona* → 2: actualizaciones en tiempo real sobre conexión probada → 3: offline y reconexión |

Con Risk-First, si la Slice 1 falla, **descubres el problema antes de invertir en las slices 2 y 3**.

---

## 📏 Las reglas de implementación

- **Regla 0 — Simplicidad primero.** *"¿Cuál es la cosa más simple que podría funcionar?"* Tres líneas parecidas de código **mejor** que una abstracción prematura. Implementa la versión naïve y obviamente correcta; optimiza solo tras probar la corrección con tests.
- **Regla 0.5 — Disciplina de alcance.** Toca solo lo que la tarea requiere. **No** "limpies" código adyacente, **no** refactorices imports en archivos que no modificas, **no** borres comentarios que no entiendes del todo, **no** modernices sintaxis en archivos que solo lees. Si ves algo mejorable fuera del alcance, **anótalo sin arreglarlo** (`NOTICED BUT NOT TOUCHING:`) y pregunta si quieres tareas para eso.
- **Regla 1 — Una cosa a la vez.** Cada incremento cambia una sola cosa lógica. No mezcles añadir componente + refactorizar otro + tocar la config de build.
- **Regla 2 — Mantenlo compilable.** Tras cada incremento el proyecto debe compilar y los tests existentes pasar. **No dejes el código roto entre rebanadas.**
- **Regla 3 — Feature flags para features incompletas.** Si no está listo para usuarios pero necesitas mergear, enciérralo tras un flag. Esto te permite mergear incrementos pequeños sin exponer trabajo incompleto.
- **Regla 4 — Defaults seguros.** El código nuevo debe por defecto a comportamiento seguro y conservador (ej. `notify ?? false`, desactivado y opt-in).
- **Regla 5 — Rollback-friendly.** Cada incremento debe ser **revertible independientemente**: cambios aditivos fáciles de revertir, modificaciones mínimas, migraciones con su rollback, y nunca borres algo en un commit para reemplazarlo en ese mismo commit.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Lo testearé todo al final" | Los bugs se compound. Un bug en la Slice 1 hace mal las Slices 2–5 |
| "Es más rápido hacerlo todo de una vez" | *Parece* más rápido hasta que algo se rompe y no encuentras cuál de 500 líneas lo causó |
| "Estos cambios son muy pequeños para commitearlos separados" | Los commits pequeños son gratis. Los grandes esconden bugs |
| "Ya añadiré el feature flag luego" | Si la feature no está completa, no debería ser visible para el usuario. Pon el flag ahora |
| "Este refactor es pequeño, lo incluyo" | Refactors mezclados con features hacen ambos más difíciles de revisar y depurar |

---

## 🚩 Red Flags

- Más de 100 líneas escritas sin correr tests.
- Múltiples cambios no relacionados en un solo incremento.
- *"Añado esto rápido también"* — expansión de alcance.
- Saltarse el paso de test/verificar para ir más rápido.
- Build o tests rotos entre incrementos.
- Construir abstracciones antes de que el tercer caso de uso las exija.
- Tocar archivos fuera del alcance *"ya que estaba aquí"*.

---

## 🔗 Relaciones

**En el flujo de Addy:** es el motor de la Fase 4 de [[spec-driven-development]] y de las tareas de [[planning-and-task-breakdown]]. Cada incremento se commitea según [[git-workflow-and-versioning]] y se prueba con [[test-driven-development]].

**Equivalencia Matt Pocock:** se solapa con [[implement-spec]] y [[implement]] (el flujo "The Main Flow" de Matt). **Diferencia clave:** Matt integra el TDD **dentro** del ciclo de build como una sub-skill; aquí TDD y los incrementos son **skills hermanas** que se coordinan pero se invocan por separado.

```bash
npx skills add addyosmani/agent-skills --skill incremental-implementation
```
