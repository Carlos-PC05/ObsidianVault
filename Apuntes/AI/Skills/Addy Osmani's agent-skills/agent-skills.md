---
alias:
  - Addy Osmani agent-skills
  - agent-skills
  - Addy Osmani Skills
tags:
  - ai/skills
  - addy-osmani
  - dev/workflow
  - ai/index
source: https://github.com/addyosmani/agent-skills
type: index-note
---
> [!abstract] **Propósito**
> **Paquete de 25 skills de ingeniería "production-grade"** que cubren el **ciclo de vida completo** del desarrollo de software, de *Definir* a *Desplegar*. No son una caja de comandos sueltos: cada skill es un **flujo de trabajo con pasos, puertas de verificación y una tabla de "anti-racionalización"** que impide que el agente se salte etapas justificándose.

> [!info] Ficha rápida
> - **Repo:** [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) · MIT · [skills.addy.ie](https://skills.addy.ie)
> - **Autores:** Addy Osmani (creador), Federico Bartoli, Joan León
> - **Tamaño:** 25 skills · 9 comandos · 4 *personas* de revisión · 7 checklists de referencia
> - **Plataformas:** Claude Code, Cursor, Codex, Gemini, Antigravity, OpenCode, Windsurf, Copilot, Kiro y +70 con el CLI `npx skills`

---

## 🗺️ El ciclo de vida completo

```
  DEFINE          PLAN           BUILD          VERIFY         REVIEW          SHIP
 ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐
 │ Idea │ ───▶ │ Spec │ ───▶ │ Code │ ───▶ │ Test │ ───▶ │  QA  │ ───▶ │  Go  │
 │Refine│      │  PRD │      │ Impl │      │Debug │      │ Gate │      │ Live │
 └──────┘      └──────┘      └──────┘      └──────┘      └──────┘      └──────┘
  /spec          /plan          /build        /test         /review       /ship
```

Cada comando **activa las skills necesarias automáticamente**. Además, las skills **se activan solas** según lo que estés haciendo: diseñar una API dispara `api-and-interface-design`, construir UI dispara `frontend-ui-engineering`, etc.

> [!TIP] `/build auto`
> Ejecuta `/build auto` para generar el plan **e implementar todas las tareas en una sola pasada aprobada**. Elimina el paso humano *entre* tareas, **no** la verificación: cada tarea sigue siendo TDD y con commit individual, y se detiene ante fallos o pasos riesgosos.

---

## ⌨️ Los 9 comandos

| Qué haces | Comando | Principio |
| :--- | :--- | :--- |
| Definir qué construir | `/spec` | Spec antes que código |
| Planear cómo construirlo | `/plan` | Tareas pequeñas y atómicas |
| Construir incrementalmente | `/build` | Una rebanada a la vez |
| Probar que funciona | `/test` | Los tests son la prueba |
| Fijar la barra de calidad | `/constraints` | Decide una vez, exige siempre |
| Revisar antes de merge | `/review` | Mejora la salud del código |
| Auditar performance web | `/webperf` | Mide antes de optimizar |
| Simplificar el código | `/code-simplify` | Claridad sobre ingeniosidad |
| Desplegar a producción | `/ship` | Más rápido es más seguro |

---

## 🗂️ Las 25 skills por fase

| Fase | Skills |
| :--- | :--- |
| **Meta** | [[using-agent-skills]] |
| **Define** | [[interview-me]] · [[idea-refine]] · [[spec-driven-development]] · [[constraint-driven-development]] |
| **Plan** | [[planning-and-task-breakdown]] |
| **Build** | [[incremental-implementation]] · [[test-driven-development]] · [[context-engineering]] · [[source-driven-development]] · [[doubt-driven-development]] · [[frontend-ui-engineering]] · [[api-and-interface-design]] |
| **Verify** | [[test-driven-development]] · [[browser-testing-with-devtools]] · [[debugging-and-error-recovery]] |
| **Review** | [[code-review-and-quality]] · [[code-simplification]] · [[security-and-hardening]] · [[performance-optimization]] |
| **Ship** | [[git-workflow-and-versioning]] · [[ci-cd-and-automation]] · [[deprecation-and-migration]] · [[documentation-and-adrs]] · [[observability-and-instrumentation]] · [[shipping-and-launch]] |

> [!note] Fuera de estas notas
> El repo también trae **7 checklists de referencia** (`definition-of-done`, `testing-patterns`, `security-checklist`, `performance-checklist`, `accessibility-checklist`, `observability-checklist`, `orchestration-patterns`), un **framework de evals de 3 niveles** en CI y una librería de 13 scripts.

---

## 👥 Las 4 *personas* de revisión

Personas = **roles con una perspectiva y un formato de salida**, que el harness consume como *system prompt*. No son skills: una skill es el *cómo*, una persona es el *quién*, un comando slash es el *cuándo*.

| Persona | Rol | Para qué |
| :--- | :--- | :--- |
| [[code-reviewer]] | Staff Engineer senior | Revisión de 5 ejes antes del merge |
| [[security-auditor]] | Ingeniero de seguridad | Detección de vulnerabilidades, auditoría estilo OWASP |
| [[test-engineer]] | Ingeniero QA | Estrategia de tests, análisis de cobertura, patrón Prove-It |
| [[web-performance-auditor]] | Ingeniero de performance web | Auditoría de Core Web Vitals, carga/renderizado/red |

### 🧩 Las tres capas

| Capa | Qué es | Ejemplo | Rol |
| :--- | :--- | :--- | :--- |
| **Skill** | Un flujo con pasos y criterios de salida | `code-review-and-quality` | El **cómo** |
| **Persona** | Un rol con una perspectiva y un formato | `code-reviewer` | El **quién** |
| **Comando** | Punto de entrada para el usuario | `/review`, `/ship` | El **cuándo** |

> [!warning] Regla dura: las personas NO llaman a otras personas
> Si una persona quiere delegar en otra, **debe plantearlo como recomendación en su informe**. La orquestación es trabajo de los slash commands o del usuario. En Claude Code es además una restricción de plataforma (*"los subagentes no pueden generar otros subagentes"*).

### El único fan-out que endorsa el repo

`/ship` es el **único patrón de orquestación por fan-out** que recomienda el repo:

```
/ship
  ├── (paralelo) code-reviewer     → informe de revisión
  ├── (paralelo) security-auditor  → informe de auditoría
  └── (paralelo) test-engineer     → informe de cobertura
                  ↓
        fase de merge (agente principal)
                  ↓
        decisión go/no-go + plan de rollback
```

> [!tip] Por qué funciona
> Cada subagente opera sobre el mismo diff pero produce una **perspectiva distinta** · no tienen dependencias entre sí → **paralelismo genuino** · cada uno corre en una **ventana de contexto fresca** → la sesión principal no se ensucia · el paso de merge es pequeño y se beneficia del contexto completo, así que se queda en el agente principal.

> [!danger] Lo que NO debes construir: un meta-orquestador
> Una persona cuyo trabajo sea "decidir a qué otra persona llamar" es una capa de enrutamiento puro sin valor de dominio: añade **dos saltos de paráfrasis** (pérdida de información + 2× el coste en tokens) y replica trabajo que los slash commands y el mapeo de intenciones de `AGENTS.md` ya hacen. **El usuario ya sabe que quiere una review; que llame a `/review`.**

> [!note] Matriz de decisión
> ```
> ¿Es el trabajo una perspectiva única sobre un artefacto único?
> ├── Sí → Invocación directa de la persona
> └── No  → ¿Son las subtareas independientes (sin estado mutable compartido, sin orden)?
>          ├── Sí → Comando slash con fan-out paralelo (ej. /ship)
>          └── No  → Comandos slash secuenciales ejecutados por el usuario
> ```
> Y una excepción deliberada: [[web-performance-auditor]] **no** entra en `/ship`, porque los audits de performance no aplican a librerías ni CLI y meterlo en un pre-lanzamiento global crearía ruido en proyectos no-web.

---

## ⚖️ Cómo se compara con Matt Pocock

> [!question] Las tres colecciones
> El repo incluye `docs/comparison.md` con una comparación honesta frente a **Superpowers** (obra) y las **skills de Matt Pocock** — estas últimas son las que ya tienes anotadas en [[../Matt Pocock's skills/setup-matt-pocock-skills|Matt Pocock's skills]]. Resumen:

| | **agent-skills** (Addy) | **Matt Pocock's skills** |
| :--- | :--- | :--- |
| **Idea central** | Codificar el ciclo de vida completo de ingeniería senior | El flujo de trabajo diario de un experto, abierto y en evolución |
| **Principio organizador** | **Fases del SDLC** detrás de una meta-skill que enruta | Una caja de herramientas curada de comandos focalizados y componibles |
| **Tamaño** | 25 skills, cubren todo el ciclo | ~30 skills, agrupadas en ingeniería / productividad / in-progress / deprecated |
| **Cobertura del ciclo** | **Amplia**: refinamiento de idea, API y UI, seguridad, performance, CI/CD, observabilidad, deprecación, ADRs, lanzamiento | Profunda pero estrecha: definir y construir; cobertura tras *Build* es fina |
| **Mecanismo distintivo** | Tablas de **anti-racionalización** y *Red Flags* en cada skill; **personas** de revisión en paralelo; **evals de 3 niveles** en CI | El bucle de **grilling** (una pregunta a la vez), TDD basado en *seams*, split explícito user-invoked vs model-invoked |
| **Medición de calidad** | Trigger, routing y evals de comportamiento sobre todo el catálogo | Ninguna publicada en el repo |
| **Mejor para** | Llevar una feature de principio a fin con un checkpoint humano en cada fase | Bucle diario rápido y afilado, fuerte en requisitos y TDD |

> [!tip] Cómo leer los enlaces de esta tabla
> Todos los enlaces a la derecha apuntan a tus notas de la carpeta hermana `Matt Pocock's skills` (subcarpetas *The Main Flow*, *Reference Skills*, *Shaping*, *Productivity Skills*, *Engineering*). Obsidian resuelve los enlaces por nombre de archivo, así que funcionan sin escribir la ruta completa.

**Conclusión práctica del propio repo:** no son excluyentes. Funciona bien tomar skills *sueltos* de distintos packs. Lo que **no** funciona es tener **dos meta-skills activos a la vez** — se pelean por los nombres de comando (`/tdd` definido en dos sitios) y compiten por la lógica de enrutamiento.

---

## 🔀 Cómo se solapan con tus notas de Matt

Estas son las equivalencias más directas, pensadas para que las compares:

| Tema | Addy Osmani | Matt Pocock (*Matt Pocock's skills*) |
| :--- | :--- | :--- |
| Bucle TDD | [[test-driven-development]] | [[tdd]] |
| Bugs / causa raíz | [[debugging-and-error-recovery]] | [[diagnosis-bugs]] |
| Code review | [[code-review-and-quality]] | [[code-review]] |
| Simplificar / refactorizar | [[code-simplification]] | [[improve-codebase-architecture]] |
| Interrogatorio de requisitos | [[interview-me]] | [[grill-me]] · [[grilling]] |
| Especificación / PRD | [[spec-driven-development]] | [[to-spec]] |
| Explorar antes de decidir | [[idea-refine]] | [[wayfinder]] |
| Prototipos / exploración | [[idea-refine]] | [[prototype]] |
| Diseño de módulos | [[api-and-interface-design]] | [[codebase-design]] |
| Modelo de dominio | [[constraint-driven-development]] | [[domain-modeling]] |
| Escritura para agentes | [[context-engineering]] | [[writing-for-agents]] |
| Handoff entre sesiones | [[context-engineering]] | [[handoff]] |
| Conflictos de merge | — | [[resolving-merge-conflicts]] |
| UI / diseño visual | [[frontend-ui-engineering]] | Notas de `Impeccable` |

> [!warning] Las dos，TDD
> Hay una diferencia **doctrinaria** real: la nota de Matt dice literalmente que *"la refactorización no es parte del bucle"* (pertenece al code review), mientras que `test-driven-development` de Addy sí incluye **RED → GREEN → REFACTOR** como tercer paso del ciclo. No son contradictorias, pero no son intercambiables: decide cuál adoptas como router principal.

---

## 📦 Instalación

```bash
# Todas las skills (25) — funciona con 70+ agentes
npx skills add addyosmani/agent-skills

# Ver el catálogo antes de instalar
npx skills add addyosmani/agent-skills --list

# Skills individuales
npx skills add addyosmani/agent-skills --skill code-review-and-quality
npx skills add addyosmani/agent-skills --skill interview-me
npx skills add addyosmani/agent-skills --skill test-driven-development
```

> [!warning] Trampa conocida al instalar una sola skill
> Un `npx` de una skill individual copia solo `skills/<nombre>/`, **no** el directorio `references/` del repo. La skill sigue funcionando, pero las rutas a checklists compartidos quedan inaccesibles. Solución: usa una integración de repo completo, clona el repo, o copia el checklist que necesites dentro de un `references/` local. ([issue #361](https://github.com/addyosmani/agent-skills/issues/361))

---

## 🧬 Anatomía de una skill

```
┌─────────────────────────────────────────────────┐
│  SKILL.md                                       │
│  Frontmatter → name + description ("Use when…")│
│  Overview         → Qué hace esta skill         │
│  When to Use      → Condiciones de activación   │
│  Process          → Flujo paso a paso            │
│  Rationalizations → Excusas + réplicas          │
│  Red Flags        → Señales de que algo va mal  │
│  Verification     → Qué evidencia se exige      │
└─────────────────────────────────────────────────┘
```

**Cuatro decisiones de diseño que explican el resto del pack:**
- **Proceso, no prosa.** Las skills son flujos que el agente *sigue*, no documentación que lee.
- **Anti-racionalización.** Cada skill incluye una tabla de las excusas que el agente usa para saltarse pasos (*"añadiré los tests después"*) con su contrapunto documentado.
- **La verificación no es negociable.** Toda skill termina con requisitos de evidencia. *"Parece correcto"* nunca es suficiente.
- **Divulgación progresiva.** El `SKILL.md` es el punto de entrada; las referencias se cargan solo cuando hacen falta.

---

## ⚠️ Limitaciones honestas

- **Evaluación de la propia calidad:** Addy es el único de los tres packs con evals de catálogo en CI (3 niveles: estructura, routing/descripciones, y *grading* de la traza real de ejecución). Matt y Superpowers no publican ninguno. Esto es su ventaja medible.
- **En contra:** tiene menos de una "corrida" única y opinada que Superpowers, y **ninguno de los tres ha resuelto aún la memoria duradera entre sesiones** — lo que aprendiste en una sesión rara vez llega limpia a la siguiente.
- **Cobertura de plataforma:** Matt es feliz en Claude Code; Addy y Superpowers corren en casi todas partes.
