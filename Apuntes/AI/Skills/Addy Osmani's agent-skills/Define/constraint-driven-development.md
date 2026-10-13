---
alias:
  - Skill constraint-driven-development
  - CDD
  - CONSTRAINTS.md
tags:
  - ai/skills
  - addy-osmani
  - ai/define
  - dev/quality
  - dev/tools
source: https://github.com/addyosmani/agent-skills/blob/main/skills/constraint-driven-development/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Convierte la barra de calidad del proyecto en un **contrato escrito con números** (`CONSTRAINTS.md`) que sobrevive a la conversación y se puede **comprobar mecánicamente**. Además vigila el diff para detectar al agente **bajando la barra en silencio**: nuevos `@ts-ignore`, tests saltados, aserciones borradas, stubs sin implementar, umbrales reducidos.

> [!important] Por qué esto y no otra skill más de "qué es bueno"
> Las demás skills dan **prosa** que el agente lee y puede seguir o no, y nada de eso sobrevive al final de la sesión. Cuando tú escribías el código, leerlo te decía si estaba bien. Un agente escribe más en una tarde de lo que tú leerás esa semana: el juicio tiene que salirse de tu cabeza y pasar a checks que rodean el bucle.

---

## 🎯 ¿Cuándo usarla?

- Al arrancar un proyecto o feature grande sin barra de calidad escrita.
- El usuario pide *"pon unas constraints"*, *"define nuestros estándares"*, *"deja de mandar basura"*.
- Un agente produce volumen que nadie lee línea a línea.
- Hay checks en CI pero nadie sabe **cuáles bloquean un merge** y cuáles son decoración.
- Los números de cobertura, performance o accesibilidad se discuten **en cada PR** en vez de decidirse una vez.
- Estás a punto de lanzar `/build auto` y lo único entre el agente y `main` es una suite de tests que **escribió el propio agente**.

**Cuándo NO:** ya existe `CONSTRAINTS.md` y no está cambiando · scripts de un uso, spikes, prototipos desechables · quieres un code review ahora ([[code-review-and-quality]]) o montar el pipeline ([[ci-cd-and-automation]]) · código pre-PMF con esperanza de vida de dos semanas.

> [!warning] Restricción de carga
> La entrevista necesita un usuario vivo. **No la ejecutes en CI, `/loop` ni runs autónomos**. Si faltan constraints ahí, aplica el *Floor*, anótalo y marca el resto para un humano.

---

## 🔄 El proceso

**1. Detecta antes de preguntar.** Nunca preguntes lo que puedes leer: `package.json`/`pyproject.toml`/`go.mod`/`Cargo.toml`, dev dependencies, linters existentes, cobertura actual, `.github/workflows/`, `.claude/`, `AGENTS.md`. Reporta en dos líneas y pregunta solo lo que falta.

**2. Cuatro preguntas, cada una con default.** Disciplina de [[interview-me]], con un cambio: *cada pregunta tiene default*, así que *"no lo sé"* es una respuesta completa que igual produce una config funcional. Las cuatro: **qué dimensiones querem Besides el floor** (cobertura, seguridad, performance, accesibilidad, límites de arquitectura) · **¿bloquean o avisan?** · **¿números objetivo o medimos dónde estamos?** · **¿qué check lento toleras antes de que el agente devuelva el trabajo?** (default: 90 s). **Para en cuatro.** Un intake de doce preguntas produce una config que nadie entiende.

**3. Escribe `CONSTRAINTS.md`** en la raíz del repo, con tres bloques:
- **Floor** (siempre aplicado, sin setup): sin nuevas supresiones (`@ts-ignore`, `eslint-disable`, `# noqa`, `# type: ignore`) · sin stubs sin implementar · sin tests saltados o borrados sin razón en el commit · sin secretos · **este archivo no se debilita para hacer pasar un cambio**.
- **Enforced with numbers**: tabla `Dimensión | Regla | Checked by | Runs at`. **Cada fila nombra el comando que produce el veredicto.** Una dimensión con número y sin comando es una aspiración, no una constraint.
- **Measured, not yet enforced** + tabla de **Exceptions** con `ID | Regla | Ruta | Razón | Owner | Expires`.

Luego una línea en `AGENTS.md` y `CLAUDE.md`: *"Read CONSTRAINTS.md before writing code. Do not weaken it to make a change pass."*

**4. Instala lo que cada dimensión necesita.** Cada dimensión elegida significa instalar una herramienta. **No inventes tu propio checker cuando ya existe uno *de facto*** — se listan porque sus formatos de regla y umbrales son los que todo el ecosistema tiene en cuenta, para que la config existente del equipo siga funcionando.

| Dimensión | Herramienta | Gate |
| :--- | :--- | :--- |
| Tipos (TS / Python) | `tsc --noEmit` / `mypy` | cualquier error |
| Lint | tu config (`eslint`/`biome`/`ruff`) | cualquier error |
| Cobertura | tu runner / `pytest-cov` | cobertura de líneas cambiadas |
| Seguridad: código | Semgrep (`p/default`, `p/owasp-top-ten`) | cualquier hallazgo alto |
| Seguridad: secretos | gitleaks | cualquier hallazgo |
| Seguridad: deps | osv-scanner | alto o superior |
| Performance: página | Lighthouse | LCP, CLS, score |
| Performance: bundle | size-limit | presupuesto por entry |
| Accesibilidad | axe-core | cero críticos o serios |
| Arquitectura | dependency-cruiser | cualquier violación |
| Calidad de aserciones | Stryker | *mutation score* |

> [!warning] Cinco cosas que te morderán si las saltas
> 1. **`--redact` en gitleaks no es opcional** — sin él, el secreto filtrado aterriza en la transcripción del agente, que es como una clave acaba en un log o un mensaje de commit.
> 2. **Lighthouse y axe necesitan una URL** — solo funcionan contra una app en marcha (preview deploy o servidor local). Si el proyecto no tiene URL (CLI, librería, escritorio), **declara esa dimensión en vez de inventar un check que no puede correr**.
> 3. **Acota las caras al diff.** Stryker sobre todo el repo tarda horas y termina desactivado; sobre los archivos que tocó el cambio, tarda menos de un minuto.
> 4. **La cobertura no necesita segunda pasada.** Lee el lcov que tu suite ya escribe e intésectalo con `git diff`.
> 5. **Las reglas del registro de Semgrep son gratuitas; comprueba la licencia** antes de redistribuirlas.

Mapea todo a scripts del proyecto: `check:fast` (por edición) · `check:task` (cuando el agente cree que acabó) · `check:full` (en CI). `CONSTRAINTS.md` es la **fuente canónica**; los scripts son envoltorios que deben reflejarlo.

**5. Conéctalo al ciclo de vida.**

| Fase | Qué corre | Presupuesto |
| :--- | :--- | :--- |
| BUILD | Tipos, lint, secretos, el floor | < 5 s, solo archivo cambiado |
| VERIFY | Tests relacionados, cobertura de líneas cambiadas | < 90 s |
| REVIEW | Todo, más los guards | minutos |
| SHIP | Chequeos direccionales, sin regresiones | CI |

Dos reglas: **acota al diff** (cobertura de líneas cambiadas es un número que el agente *puede mover*; la cobertura del proyecto es heredada) y **el coste decide la colocación** (nada que pase de unos segundos se queda en el bucle de edición).

**6. Guarda la barra en sí misma.** Alguien va a argumentar que si el agente escribe el código *y* los checks, los checks no prueban nada. Tiene razón a medias. Los agentes **no forjan buclesConformistas**: se topan con un check rojo y toman el camino más barato al verde. Vigila estos cinco movimientos en el diff, en revisión:
1. **El umbral se movió** (presupuesto bajado, severidad bajada, check sacado de la etapa rápida).
2. **Un test se puso más fácil** (`.skip`, archivo borrado, aserciones extraídas).
3. **Un checker silenciado** (nuevo `@ts-ignore`/`eslint-disable`;atención especial a `istanbul ignore`, `Stryker disable`, `nosemgrep`, `gitleaks:allow`).
4. **Trabajo sin terminar** (stub que lanza, `catch` vacío, `TODO` donde iba la implementación).
5. **Apareció una excepción** nadie discutió.

> [!note] No todos los checks son igual de circulares
> **Externos** (axe-core codifica WCAG, osv-scanner lee una BD de vulnerabilidades, Lighthouse mide un navegador real) — el agente no puede argumentar. **De proyecto** (tus reglas de lint, tus límites de capa) — un humano posee el archivo. **De suite** (tus propios tests) — los más útiles y **los únicos realmente circulares**. Una barra hecha solo del tercer tipo vale menos que una con una opinión externa. Asegúrate de al menos una externa.

**7. Ratchets, cuando no tienes número.** Poner 80 % de cobertura sobre un código al 62 % produce un build rojo para siempre, y un equipo que aprende a ignorar los builds rojos. La alternativa no pide ninguna decisión: **registra dónde estás y refuses empeorar**. Entrégalo en la tabla *"Measured, not yet enforced"* con el valor de hoy y una dirección. Cada check compara contra el valor registrado, no contra una aspiración. Cuando un número mejora, actualízalo; cuando baja, **ese es el hallazgo**.

---

## 📊 Defaults sensatos

| Constraint | Default | Por qué ese número |
| :--- | :--- | :--- |
| Cobertura de líneas cambiadas | ≥ 80 % | Alto suficiente para forzar un test, bajo para permitir una línea de config |
| Cobertura del proyecto | valor de hoy, no debe bajar | No requiere argumento para adoptarse |
| Mutation score (si se usa) | ≥ 60 % al empezar | Típico de una suite nunca mutada; 80 % es maduro |
| Vulnerabilidades en deps | nada en alto o superior | Por debajo es mayormente ruido |
| LCP | ≤ 2500 ms | Umbral "good" de Core Web Vitals |
| CLS | ≤ 0.1 | Ídem |
| Accesibilidad | cero violaciones críticas o serias de axe | Las moderadas y menores suelen ser debatibles |
| Vida de una excepción | 90 días | Suficiente para planear, lo bastante corto para recordar |
| Tolerancia del ratchet | 0.5 % | Absorbe la deriva cuando un archivo no relacionado mueve el número |

> [!important] Di siempre el número y la razón juntos
> Un umbral sin su *racional* lo borra la siguiente persona que se topa con él.

---

## 🔺 Ruta de escalamiento (dientes)

Empieza en el primero:
1. **Solo escrito.** `CONSTRAINTS.md` existe y los agentes lo leen. No cuesta nada.
2. **Con scripts.** Un `npm run check` que corre los checks rápidos, en el hook post-edición y en CI. Determinista, sin dependencias nuevas.
3. **Con herramienta.** Un runner dedicado que gestiona acotado al diff, presupuestos, ratchets y los guard-checks.

**La mayoría de proyectos deberían quedarse en el 2.** Pasa al 3 cuando mantengas más de ~30 líneas de shell corriendo checks.

**Una primera pasada puede ser solo el Floor:** el floor-guard es solo-diff y no necesita instalaciones, así que puedes proteger el día uno y añadir dimensiones numeradas conforme instales cada herramienta.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Añadimos constraints cuando el código se asiente" | El código se asienta alrededor de lo que se permitió mientras se movía |
| "Los tests son las constraints" | Los tests que escribiste prueban que estás de acuerdo contigo mismo |
| "No podemos llegar al 80 %" | Entonces no pongas 80 %. Pon el número de hoy y mantenlo |
| "Esto va a ralentizar al agente" | Solo si pones checks lentos en el bucle rápido. Eso es un error de colocación |
| "Me recordaré nuestros estándares" | El agente no, y es quien escribe casi todo el código |
| "Las constraints van a bloquear el shipping" | Una excepción con owner y fecha desbloquea. Borrar la constraint desbloquea a todos para siempre |

---

## 🚩 Red Flags

- La entrevista pasó de cuatro preguntas, o produjo una config que el usuario no puede explicar.
- Un presupuesto que el codebase falla hoy, sin plan para alcanzarlo.
- Una dimensión escrita con número pero **sin herramienta detrás**.
- Un checker hecho a mano cuando existía uno *de facto*.
- Todas las constraints juzgadas por la suite del propio proyecto, sin opinión externa.
- `CONSTRAINTS.md` cambiado en el mismo commit que la feature que fallaba.
- Una excepción sin owner, o con expiración a más de un año.
- El agente propuso relajar un umbral en vez de arreglar el código.
- Checks lentos en el bucle de edición y alguien ya pasa `--no-verify`.

---

## 🔗 Relaciones

**En el flujo de Addy:** define la barra que luego exigirán [[code-review-and-quality]], [[test-driven-development]], [[security-and-hardening]] y [[performance-optimization]]. Es el **cuarto** paso natural de Define, después de la spec. Corre en paralelo con [[git-workflow-and-versioning]].

**Equivalencia Matt Pocock:** es la pieza que **no tiene equivalente** en el pack de Matt. Lo más cercano es [[domain-modeling]] (escribir `CONTEXT.md` / ADRs), pero ahí el objetivo es **vocabulario y límites de dominio**; aquí es **números y comandos verificables**. Matt cubre el *qué* del dominio; Addy cubre el *cuánto* de la calidad.

```bash
npx skills add addyosmani/agent-skills --skill constraint-driven-development
```
