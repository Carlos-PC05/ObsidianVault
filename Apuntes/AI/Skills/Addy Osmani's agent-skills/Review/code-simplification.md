---
alias:
  - Skill code-simplification
  - Addy Osmani Code Simplification
  - Chesterton's Fence
tags:
  - ai/skills
  - addy-osmani
  - ai/review
  - dev/refactoring
  - dev/architecture
source: https://github.com/addyosmani/agent-skills/blob/main/skills/code-simplification/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Simplifica el código reduciendo complejidad y preservando el comportamiento exacto.** El objetivo no es menos líneas: es código más fácil de leer, entender, modificar y depurar. Cada simplificación debe pasar una prueba simple: *"¿un miembro nuevo del equipo lo entendería más rápido que el original?"*

---

## 🎯 ¿Cuándo usarla?

Tras una feature que funciona y cuyos tests pasan, pero cuya implementación se siente más pesada de lo necesario · durante code review cuando se marcan problemas de legibilidad o complejidad · lógica profundamente anidada, funciones largas o nombres unclear · código escrito bajo presión de tiempo · consolidar lógica relacionada dispersa por varios ficheros · tras mergear cambios que introdujeron duplicación.

**Cuándo NO:** el código ya está limpio y legible · **aún no entiendes qué hace el código** (comprende antes de simplificar) · el código es performance-critical y la versión "más simple" sería mediblemente más lenta · estás a punto de reescribir el módulo entero.

---

## 📐 Los 5 principios

**1. Preserva el comportamiento exactamente.** No cambies lo que el código hace — solo cómo lo expresa. Entradas, salidas, efectos laterales, comportamiento de error y casos idénticos. Pregunta antes de cada cambio: ¿misma salida para toda entrada? · ¿mismo comportamiento de error? · ¿mismos efectos laterales y orden? · ¿todos los tests existentes pasan sin modificarlos?

**2. Sigue las convenciones del proyecto.** La simplificación significa hacer el código más consistente con el codebase, no imponer preferencias externas. Lee `CLAUDE.md`, estudia cómo maneja el código vecino patrones similares, y casa con el estilo del proyecto en imports, estilo de declaración, naming, manejo de errores y profundidad de anotación de tipos. **Simplificar rompiendo consistencia no es simplificar: es churn.**

**3. Prefiere claridad sobre ingeniosidad.** Código explícito mejor que compacto cuando la versión compacta requiere una pausa mental para parsear. (Una cadena de ternarios anidados no es más simple que un `if/else` de 5 líneas; un `reduce` con lógica inline no es más simple que un `Map` con nombre.)

**4. Mantén el equilibrio.** La simplificación tiene un modo de fallo: la **sobre-simplificación**.
- **Inlining demasiado agresivo** — quitar un helper que daba nombre a un concepto hace el call site peor.
- **Combinar lógica no relacionada** — dos funciones simples fusionadas en una compleja no es más simple.
- **Quitar "abstracciones innecesarias"** — algunas existen por extensibilidad o testeabilidad, no por complejidad.
- **Optimizar por número de líneas** — menos líneas no es el objetivo; comprensión más rápida sí.

**5. Acota a lo que cambió.** Por defecto simplifica el código modificado recientemente. **Evita refactors oportunistas** de código no relacionado salvo que se pida explícitamente ampliar el alcance.

---

## 🔄 El proceso

**1. Entiende antes de tocar (Chesterton's Fence).** Si ves una valla cruzando un camino y no entiendes por qué está ahí, no la derribes. Antes de cambiar o quitar nada: ¿cuál es la responsabilidad de este código? · ¿qué lo llama, qué llama? · ¿casos límite y rutas de error? · ¿tests que definan el comportamiento esperado? · ¿por qué podría haberse escrito así (performance, constraint de plataforma, razón histórica)? · **revisa `git blame`: ¿cuál era el contexto original?**

**2. Identifica oportunidades.** Cada patrón es una señal concreta, no un "olor" vago:

| Complejidad estructural | Señal | Simplificación |
| :--- | :--- | :--- |
| Anidamiento profundo (3+ niveles) | Control de flujo difícil de seguir | Extrae condiciones a guard clauses o helpers |
| Funciones largas (50+ líneas) | Múltiples responsabilidades | Divide en funciones enfocadas con nombres descriptivos |
| Ternarios anidados | Requiere pila mental | Reemplaza por if/else, switch u objetos de lookup |
| Flags booleanos de parámetro | `doThing(true, false, true)` | Reemplaza por objetos de opciones o funciones separadas |
| Condicionales repetidos | Mismo `if` en varios sitios | Extrae a un predicado con buen nombre |

| Naming y legibilidad | Señal | Arreglo |
| :--- | :--- | :--- |
| Nombres genéricos | `data`, `result`, `temp`, `val` | Renombra describiendo el contenido |
| Abreviaturas | `usr`, `cfg`, `btn`, `evt` | Palabras completas salvo universal (`id`, `url`, `api`) |
| Nombres engañosos | Función `get` que además muta | Renombra al comportamiento real |
| Comentarios del "qué" | `// increment counter` sobre `count++` | **Bórralo** — el código ya es claro |
| Comentarios del "por qué" | `// Retry because the API is flaky under load` | **Consérvalo** — carga intención que el código no expresa |

| Redundancia | Señal | Simplificación |
| :--- | :--- | :--- |
| Lógica duplicada | Mismos 5+ líneas en varios sitios | Extrae a función compartida |
| Código muerto | Ramas inalcanzables, vars sin usar, bloques comentados | Quita (tras confirmar que es muerto) |
| Abstracciones innecesarias | Wrapper que no aporta valor | Inline: llama directamente a lo de debajo |
| Sobre-ingeniería | Factory para una factory, strategy con una strategy | Sustituye por el enfoque directo |
| Aserciones redundantes | Cast a un tipo ya inferido | Quita la aserción |

**3. Aplica incrementalmente.** Una simplificación a la vez. Corre los tests después de cada una. Si pasan → commit. Si fallan → revierte y reconsidera. **Envía los refactors en commits separados de feature o bug fix** — un PR que refactoriza y añade feature son dos PRs.

> [!tip] The Rule of 500
> Si un refactor tocaría más de **500 líneas**, invierte en **automatización** (codemods, scripts `sed`, transformaciones AST) en vez de editar a mano. Las ediciones manuales a esa escala son propensas a error y agotadoras de revisar.

**4. Verifica el resultado.** Compara antes/después: ¿es genuinamente más fácil de entender? ¿introdujiste patrones inconsistentes con el codebase? ¿el diff es limpio y revisable? **Si la versión "simplificada" es más difícil de entender o revisar, revierte. No todos los intentos de simplificación tienen éxito.**

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Funciona, no hace falta tocarlo" | Código que funciona pero es difícil de leer será difícil de arreglar cuando se rompa |
| "Menos líneas siempre es más simple" | Un ternario anidado de 1 línea no es más simple que un `if/else` de 5. La simplicidad es velocidad de comprensión |
| "También simplificaré ese código no relacionado" | La simplificación sin alcance crea diffs ruidosos y regresiones no intencionadas |
| "Los tipos hacen el código autodocumentado" | Los tipos documentan estructura, no intención. Una función bien nombrada explica el *por qué* mejor |
| "Esta abstracción podría ser útil luego" | No preserves abstracciones especulativas. Si no se usa ahora, es complejidad sin valor |
| "El autor original tenía sus razones" | Puede ser. Revisa `git blame` (Chesterton's Fence). Pero la complejidad acumulada suele no tener razón: es residuo de iteración bajo presión |
| "Voy a refactorizar mientras añado la feature" | Separa el refactor de la feature. Cambios mixtos son más difíciles de revisar, revertir y entender en el historial |

---

## 🚩 Red Flags

- Simplificar que **requiere modificar tests** para pasar (probablemente cambiaste comportamiento).
- Código "simplificado" más largo y difícil de seguir que el original.
- Renombrar cosas para encajar con tus preferencias en vez de las convenciones del proyecto.
- Quitar manejo de errores porque *"hace el código más limpio"*.
- Simplificar código que no entiendes del todo.
- Agrupar muchas simplificaciones en un commit grande y difícil de revisar.
- Refactorizar fuera del alcance de la tarea actual sin que se pidiera.

---

## 🔗 Relaciones

**En el flujo de Addy:** parte de REVIEW, siguiendo a [[code-review-and-quality]]. Sus remediaciones estructurales son las mismas; esta skill las ejecuta de formaFocused e incremental. Lo que simplifica termina en [[git-workflow-and-versioning]] como commits separados.

**Equivalencia Matt Pocock:** corresponde a [[improve-codebase-architecture]], pero **con un objetivo distinto**: Mattattacks la **deuda estructural a nivel de repo** (límites de módulo, acoplamiento); Addy simplifica **código legible** preservando comportamiento exacto. Addy aporta además lo que Matt no tiene: **Chesterton's Fence**, la **Rule of 500** (automatizar por encima de 500 líneas) y la guía explícita de **cuándo NO simplificar**.

```bash
npx skills add addyosmani/agent-skills --skill code-simplification
```
