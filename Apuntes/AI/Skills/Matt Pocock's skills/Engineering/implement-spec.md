---
alias:
  - Skill /implement-spec
  - AI Hero Implement Spec
tags:
  - ai/skills
  - dev/tdd
  - dev/workflow
  - ai/engineering
source: /home/carlos/.agents/skills/implement-spec/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**  
> Implementa una **spec completa con sus tickets** mediante subagentes y worktrees independientes. Integra todo en una única **rama de integración**, revisa el resultado y cierra el trabajo según el gestor de tareas.

---

## 🎯 ¿Cuándo usarla?

- **Invocación manual:** Debes llamarla explícitamente con `/implement-spec` (`disable-model-invocation: true`).
- **Entrada:** Una especificación, sus tickets y el gestor de tareas configurado.

| Estado del trabajo | Skill a usar |
| :--- | :--- |
| Spec dividida en tickets con dependencias | `/implement-spec` |
| Un único ticket o cambio pequeño | [[implement]] |
| Spec pendiente de dividir | `/to-tickets` primero |
| Gestor de tareas no indicado | `/setup-matt-pocock-skills` |

---

## 🔄 Flujo de Ejecución (9 Pasos)

1. **Leer spec y tickets:** Entiende el grafo de tareas y sus bloqueos.
2. **Explorar si hace falta:** Un subagente puede investigar código o documentación y guardar notas fuera del repo, accesibles para los demás.
3. **Crear la rama de integración:** Si el gestor cierra mediante PRs o el usuario pide una, abre un **draft PR tras la primera integración**, con referencias de cierre a la spec y tickets.
4. **Lanzar implementadores:** Cada ticket se trabaja en su propio worktree y rama, basados en la rama de integración. Cada subagente usa [[tdd]] e incorpora la última versión de integración antes de dar su trabajo por terminado.
5. **Integrar cada resultado:** Un **subagente merger** fusiona el trabajo terminado en la rama de integración.
6. **Actualizar la frontera:** Lanza más implementadores cuando las fusiones desbloqueen nuevos tickets.
7. **Revisar el conjunto:** Ejecuta [[code-review]] sobre la rama de integración y corrige todos los hallazgos con un único subagente implementador.
8. **Cerrar el trabajo:** Si existe draft PR, lo marca listo para revisión. Si no, resuelve los tickets según el gestor e informa de la rama de integración.
9. **Limpiar:** Elimina los worktrees de los implementadores.

---

## 💡 Conceptos Clave

### 1. Grafo de tareas y frontera

Los tickets **no son una lista de pasos**. Sus dependencias determinan qué tareas están listas para empezar: esa es la **frontera**.

```text
Ticket A ──► Ticket C
Ticket B ──► Ticket D

A y B pueden empezar en paralelo.
C y D esperan a que sus dependencias se integren.
```

### 2. Una integración, varios worktrees

- **Rama de integración:** Reúne la implementación completa de la spec.
- **Worktree por ticket:** Aísla el trabajo de cada implementador.
- **Sincronización:** Antes de comenzar, verifica la base de integración y se reajusta si es incorrecta. Antes de terminar, fusiona el estado más reciente de integración en su rama.

### 3. Comunicación mediante referencias

Los subagentes reciben **punteros de contexto** a la spec, tickets, notas de investigación y commits anteriores. Se evita duplicar información ya disponible y se ejecutan implementadores en segundo plano cuando sea posible.

---

## 🔗 Dónde encaja en el Flujo Principal

```text
/to-spec ──► /to-tickets ──► /implement-spec ──► /code-review
                                  │
                                  └──► rama de integración + cierre del trabajo
```

---

## ⚠️ Puntos de Atención

- **Dependencias primero:** Solo se lanzan tickets desbloqueados; el paralelismo depende del grafo.
- **Draft PR después de integrar:** Una rama sin commits por delante de la principal no permite abrirlo.
- **Cierre según el tracker:** Marcar el PR listo para revisión no equivale necesariamente a fusionarlo ni a cerrar ya los issues.
- **Limpieza final:** Los worktrees temporales se eliminan al terminar.
