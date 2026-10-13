---
alias:
  - Skill planning-and-task-breakdown
  - Addy Osmani Planning and Task Breakdown
tags:
  - ai/skills
  - addy-osmani
  - ai/plan
  - dev/planning
  - dev/workflow
source: https://github.com/addyosmani/agent-skills/blob/main/skills/planning-and-task-breakdown/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Descompone el trabajo en tareas ordenadas, pequeñas y verificables**, con criterios de aceptación explícitos. La diferencia entre un agente que completa trabajo de forma fiable y uno que produce un enredo es, en buena parte, esta descomposición. Cada tarea debe ser implementable, testeable y verificable **en una única sesión enfocada**.

---

## 🎯 ¿Cuándo usarla?

- Tienes una spec y necesitas unidades implementables.
- Una tarea se siente demasiado grande o vaga para empezar.
- El trabajo necesita **paralelizarse** entre varios agentes o sesiones.
- Necesitas comunicar alcance a una persona.
- El orden de implementación no es obvio.

**Cuándo NO:** cambios de un solo archivo con alcance obvio, o cuando la spec ya contiene tareas bien definidas.

---

## 🔄 El proceso

**1. Entra en modo plan (read-only).** Lee la spec y las secciones relevantes del código, identifica patrones y convenciones existentes, mapea dependencias y anota riesgos. **NO escribas código durante la planificación** — la salida es un documento de plan, no implementación.

**2. Identifica el grafo de dependencias.**

```
Esquema de base de datos
    │
    ├── Modelos/tipos de la API
    │       │
    │       ├── Endpoints de la API
    │       │       │
    │       │       └── Cliente API del frontend
    │       │               │
    │       │               └── Componentes UI
    │       │
    │       └── Lógica de validación
    │
    └── Datos seed / migraciones
```

El orden de implementación sigue el grafo **de abajo arriba**: primero las cimientos.

**3. Corta en vertical.**

| ❌ Horizontal (malo) | ✅ Vertical (bien) |
| :--- | :--- |
| T1: todo el esquema de BD | T1: el usuario puede crear una cuenta (esquema + API + UI) |
| T2: todos los endpoints | T2: el usuario puede iniciar sesión (auth + API + UI) |
| T3: todos los componentes UI | T3: el usuario puede crear una tarea |
| T4: conectar todo | T4: el usuario puede ver la lista de tareas |

Cada rebanada vertical entrega **funcionalidad completa, testeable y end-to-end**.

**4. Escribe las tareas.** Cada una con: descripción de un párrafo · **criterios de aceptación** · **verificación** (tests, build, check manual) · **dependencias** · **archivos probablemente tocados** · **tamaño estimado**.

**5. Ordena y pon checkpoints.** Dependencias satisfechas primero · cada tarea deja el sistema en estado funcional · **checkpoint cada 2–3 tareas** · **tareas de alto riesgo al principio** (fail fast).

```markdown
## Checkpoint: Después de Tareas 1-3
- [ ] Todos los tests pasan
- [ ] La app compila sin errores
- [ ] El flujo principal funciona end-to-end
- [ ] Revisión con una persona antes de continuar
```

---

## 📏 Guía de tamaño de tareas

| Tamaño | Archivos | Alcance | Ejemplo |
| :--- | :--- | :--- | :--- |
| **XS** | 1 | Una función o cambio de config | Añadir una regla de validación |
| **S** | 1–2 | Un componente o endpoint | Añadir un endpoint nuevo |
| **M** | 3–5 | Una rebanada de feature | Flujo de registro de usuario |
| **L** | 5–8 | Feature multi-componente | Búsqueda con filtros y paginación |
| **XL** | 8+ | **Demasiado grande — divídela** | — |

**Un agente rinde mejor en tareas S y M.** Divídela si: tardaría más de una sesión (~2 h) · no puedes describir los criterios de aceptación en 3 bullets o menos · toca dos subsistemas independientes · **te das cuenta de que estás escribiendo "y" en el título** (señal de que son dos tareas).

---

## 📄 Archivos de salida

- **Plan:** `tasks/plan.md` — siempre markdown, porque las decisiones de diseño, riesgos y preguntas abiertas no mapean limpiamente a issues sueltos.
- **Lista de tareas:** el **task list target**. Por defecto `tasks/todo.md` (la convención que espera `/build`). Opcionalmente un **tracker externo** (GitHub Issues, Jira, Linear, `bd`/beads): un item por tarea, criterios y verificación en el cuerpo, dependencias vía el mecanismo de linking del tracker.

> [!warning] Nunca sobrescribas un plan incompleto
> Si `tasks/plan.md` o `tasks/todo.md` ya existen **y todavía tienen tareas sin marcar**: si es el mismo trabajo (revisar o extender el plan) → actualiza en sitio. Si es **otro trabajo → PARA y pregunta**. Esas tareas pueden estar a medio construir en otra sesión. No borres ni renombres por tu cuenta; **presenta el conflicto y deja decidir a la persona**. La misma regla aplica a un tracker externo: nunca cierres en bloque items abiertos de otro plan para hacer sitio.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Ya lo iré resolviendo sobre la marcha" | Así se llega al enredo y al retrabajo. 10 min de planificación ahorran horas |
| "Las tareas son obvias" | Escríbelas igual. Hacerlas explícitas saca dependencias ocultas y casos límite olvidados |
| "Planificar es overhead" | Planificar *es* el trabajo. Implementar sin plan es solo teclear |
| "Puedo sostenerlo todo en la cabeza" | Las ventanas de contexto son finitas. Los planes escritos sobreviven a los límites de sesión y a la compactación |

---

## 🚩 Red Flags

- Empezar a implementar sin lista de tareas escrita.
- Sobrescribir un `plan.md` con tareas sin marcar de otro trabajo, sin preguntar.
- Escribir `tasks/todo.md` cuando el proyecto designó un tracker externo (o dispersar tareas entre ambos).
- Tareas que dicen *"implementa la feature"* sin criterios de aceptación.
- Ningún paso de verificación en el plan.
- Todas las tareas de tamaño XL.
- Ningún checkpoint entre tareas.
- El orden por dependencias no está considerado.

---

## 🔗 Relaciones

**En el flujo de Addy:** es la Fase 2/3 de [[spec-driven-development]] y el proveedor de tareas de [[incremental-implementation]]. Las tareas que produce son las que se consumen en Build y Verify.

**Equivalencia Matt Pocock:** cubre el mismo hueco que el par `to-ticket` / `implement-spec` de Matt, con dos diferencias: Addy **no** publica en un gestor de issues por defecto (usa `tasks/todo.md` salvo que el proyecto designe uno), y Matt acopla su planificación al **setup wizard** que escribe `docs/agents/issue-tracker.md`.

```bash
npx skills add addyosmani/agent-skills --skill planning-and-task-breakdown
```
