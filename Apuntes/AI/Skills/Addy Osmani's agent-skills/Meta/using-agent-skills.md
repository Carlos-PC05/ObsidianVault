---
alias:
  - Skill using-agent-skills
  - Addy Osmani Using Agent Skills
tags:
  - ai/skills
  - addy-osmani
  - ai/meta
  - dev/context
  - dev/workflow
source: https://github.com/addyosmani/agent-skills/blob/main/skills/using-agent-skills/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Meta-skill que enruta**: decide qué skill del pack aplica a cada trabajo y define las **reglas operativas transversales** (declarar supuestos, gestionar confusión, empujar atrás, simplicidad, disciplina de alcance, verificación) que aplican a *todas* las demás skills.

---

## 🎯 ¿Cuándo usarla?

- **Al empezar una sesión**, para saber qué skill encaja con lo que vas a hacer.
- **Cuando dudes entre dos skills** del pack.
- **Como referencia de reglas** que nunca se negocian, sin importar la skill activa.

> [!warning] Router único
> Este es el equivalente del meta-router de Matt Pocock. Como la propia documentación de Addy advierte: **no actives dos meta-skills a la vez** (esta y la de Matt compiten por los nombres de comando y por la lógica de enrutamiento). Elige un router principal y toma skills sueltas del otro pack *a la carta*.

---

## 🗺️ Árbol de decisión

```
Llega una tarea
    │
    ├── ¿No sabes qué quieres aún? ────────→ interview-me
    ├── ¿Idea vaga, quieres variantes? ────→ idea-refine
    ├── ¿Proyecto/feature/cambio nuevo? ──→ spec-driven-development
    ├── ¿No hay barra de calidad escrita? → constraint-driven-development
    ├── ¿Tienes spec, necesitas tareas? ──→ planning-and-task-breakdown
    ├── ¿Implementando código? ────────────→ incremental-implementation
    │   ├── ¿UI? ────────────────────────→ frontend-ui-engineering
    │   ├── ¿API? ───────────────────────→ api-and-interface-design
    │   ├── ¿Mejorar contexto? ──────────→ context-engineering
    │   ├── ¿Código verificado en docs? ──→ source-driven-development
    │   └── ¿Apuestas altas / código nuevo? → doubt-driven-development
    ├── ¿Escribiendo/ejecutando tests? ───→ test-driven-development
    │   └── ¿En navegador? ───────────────→ browser-testing-with-devtools
    ├── ¿Se rompió algo? ─────────────────→ debugging-and-error-recovery
    ├── ¿Revisando código? ───────────────→ code-review-and-quality
    │   ├── ¿Muy complejo? ───────────────→ code-simplification
    │   ├── ¿Preocupación de seguridad? ──→ security-and-hardening
    │   └── ¿Preocupación de rendimiento? → performance-optimization
    ├── ¿Commit/branch? ──────────────────→ git-workflow-and-versioning
    ├── ¿Pipeline CI/CD? ─────────────────→ ci-cd-and-automation
    ├── ¿Deprecar/migrar? ─────────────────→ deprecation-and-migration
    ├── ¿Docs/ADRs? ──────────────────────→ documentation-and-adrs
    ├── ¿Logs/métricas/alertas? ──────────→ observability-and-instrumentation
    └── ¿Desplegando? ────────────────────→ shipping-and-launch
```

---

## 🔄 Los 6 comportamientos operativos

Se aplican **siempre**, tienen la misma prioridad que cualquier skill concreta:

1. **Surface Assumptions.** Antes de implementar nada no trivial, declara explícitamente tus supuestos (`ASSUMPTIONS I'M MAKING: 1... 2... → Corrígeme ahora o procedo`). No rellenes requisitos ambiguos en silencio: el modo de fallo más común es asumir mal y seguir sin comprobarlo.
2. **Gestiona la confusión activamente.** Ante requisitos contradictorios: **PARA**, nombra la contradicción concreta, presenta el trade-off y **espera**. Nunca elijas una interpretación a la espera de que acierte.
3. **Empuja atrás cuando toca.** No eres una máquina de decir "sí". Señala el problema, cuantifica el coste ("esto añade ~200 ms", no "podría ser más lento"), propone alternativa y **acepta la decisión humana si te la da con información completa**. La adulación es un modo de fallo.
4. **Impón simplicidad.** Tu tendencia natural es sobrecomplicar. Antes de terminar: ¿se puede en menos líneas? ¿Las abstracciones se ganan su complejidad?
5. **Disciplina de alcance.** Toca solo lo que se te pide. **No** borres comentarios que no entiendes, **no** refactorices de paso, **no** añadas features "porque parecen útiles".
6. **Verifica, no supongas.** Una tarea no está completa hasta que la verificación pasa. *"Parece correcto"* nunca es suficiente: tests en verde, build, datos de runtime.

> [!note] La barra global: *Definition of Done*
> La verificación por skill es el chequeo local. La **barra que aplica a todos los cambios** del proyecto es el `definition-of-done.md`: tests en verde, sin regresiones, comportamiento verificado en runtime, documentación actualizada. Complementa a los criterios de aceptación de cada tarea, no los reemplaza.

---

## ⚠️ Los 10 modos de fallo a evitar

1. Asumir mal sin comprobar
2. No gestionar la propia confusión — avanzar a ciegas
3. No señalar las inconsistencias que detectas
4. No presentar trade-offs en decisiones no obvias
5. Ser adulador (*"¡Claro!"*)
6. Sobrecomplicar código y APIs
7. Modificar código o comentarios ortogonales a la tarea
8. Borrar cosas que no entiendes del todo
9. Construir sin spec *"porque es obvio"*
10. Saltarse la verificación *"porque parece bien"*

---

## 🔗 Relaciones

**Relacionado:** [[interview-me]] · [[idea-refine]] · [[spec-driven-development]] · [[constraint-driven-development]]

**Equivalencia conceptual:** las skills de *contexto* de este pack ([[context-engineering]]) cubren en gran medida lo que Matt llama *escritura para agentes* ([[writing-for-agents]]).
