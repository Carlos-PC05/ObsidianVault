---
alias:
  - Skill doubt-driven-development
  - DDD
  - Addy Osmani Doubt Driven Development
tags:
  - ai/skills
  - addy-osmani
  - ai/build
  - dev/code-review
  - dev/quality
source: https://github.com/addyosmani/agent-skills/blob/main/skills/doubt-driven-development/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Somete **cada decisión no trivial a una revisión adversarial en contexto fresco** antes de que se sostenga. Una respuesta segura no es una respuesta correcta. Las sesiones largas acumulan contexto que convierte suposiciones en "hechos" sin que nadie lo note. El revisor se materializa **sesgado a refutar, no a aprobar**.

> [!important] Esto NO es `/review`
> `/review` es un veredicto sobre un artefacto **terminado**. `doubt-driven` es una **postura en vuelo**: las decisiones no triviales se interrogan mientras la corrección de rumbo todavía es barata.

---

## 🎯 ¿Cuándo usarla?

Una decisión es **no trivial** si al menos una de estas es cierta:
- Introduce o modifica **lógica de ramificación**.
- Cruza un **límite de módulo o servicio**.
- Afirma una propiedad que el compilador **no puede verificar** (thread-safety, idempotencia, orden, invariantes).
- Su corrección depende de contexto que **el lector futuro no puede ver**.
- Su **radio de acción es irreversible** (deploy a producción, migración de datos, cambio de API pública).

Aplica cuando: vas a tomar una decisión arquitectónica bajo incertidumbre · vas a commitear código no trivial · vas a **afirmar un hecho no obvio** (*"esto es seguro"*, *"esto escala"*) · trabajas en código que no entiendes del todo.

**Cuándo NO:** operaciones mecánicas (renombrar, formatear, mover ficheros) · seguir una instrucción clara e inequívoca · leer o resumir código · cambios de una línea con corrección obvia · operaciones de tooling puro · el usuario pidió velocidad sobre verificación.

> [!tip] *"Si dudas de cada pulsación de tecla, no entregas nada"*
> La skill aplica solo a las decisiones no triviales tal como están definidas arriba.

> [!warning] Restricciones de carga
> Esta skill está diseñada para el **orquestador de la sesión principal**, que puede lanzar un revisor de contexto fresco.
> - **NO añadas esta skill al frontmatter `skills:` de una *persona*** — una persona que siga el paso DOUBT generaría otra persona, el anti-patrón explícito ("las personas no invocan a otras personas").
> - Si te la aplicas desde dentro de un subagente, **sube a la persona** y deja que lo maneje la sesión principal. Como último recurso existe un fallback degradado de auto-interrogación, **pero no es revisión de contexto fresco**: márcalo como degradado.

---

## 🔄 El ciclo de duda

```
- [ ] 1: CLAIM     — escribiste la afirmación + por qué importa
- [ ] 2: EXTRACT   — aislaste artefacto + contrato, sin razonamiento
- [ ] 3: DOUBT     — invocaste revisor de contexto fresco con prompt adversarial
- [ ] 4: RECONCILE — clasificaste cada hallazgo contra el texto del artefacto
- [ ] 5: STOP      — se cumplió la condición de parada
```

**1. CLAIM.** Nombra la decisión en 2–3 líneas (afirmación + por qué importa). *"Si no puedes escribir la afirmación de forma compacta, tienes una corazonada, no una decisión."*

**2. EXTRACT.** El revisor necesita el **artefacto** y el **contrato**, no el viaje. Código → el diff o la función, no el fichero entero. Decisión → la propuesta en 3–5 frases más las constraints. **Elimina tu razonamiento**: si le entregas conclusiones, recibirás validación de tus conclusiones.

**3. DOUBT.** El prompt **debe ser adversarial** — el encuadre decide la respuesta:

> *"Revisión adversarial. Encuentra qué está mal en este artefacto. Asume que el autor está demasiado seguro. Busca: supuestos no declarados · casos límite no manejados · acoplamiento oculto o estado compartido · formas en que el contrato podría violarse · convenciones existentes que esto rompería · modos de fallo ante entrada inesperada. **NO valides. NO resumas. Encuentra problemas, o di explícitamente que tras examen minucioso no encuentras ninguno."*

> [!danger] Pasa SOLO `ARTIFACT` + `CONTRACT`. **Nunca el CLAIM**
> Entregarle tu conclusión lo sesga hacia el acuerdo. El revisor debe determinar **independientemente** si el artefacto satisface el contrato. El prompt adversarial **tiene precedencia sobre la forma de respuesta por defecto** de una persona.

**Cross-model escalation.** Un revisor de un solo modelo comparte puntos ciegos con el autor. Después de la revisión single-model, **pero antes de RECONCILE**, **para y pregunta siempre** (en sesiones interactivas): *"Revisión single-model completa. ¿Quieres una segunda opinión cross-model? Opciones: Gemini CLI, Codex CLI, revisión manual externa, o saltar."* Es **obligatoria en cada ciclo interactivo**, incluso en artefactos que parecen de bajo riesgo: decide la persona, no el agente. Si elige una CLI: verifica que está en PATH, **pruébala** antes de pasarle el prompt completo, confirma la invocación exacta, y **nunca interpoles el artefacto en un argumento con comillas de shell** (usa `stdin`/heredoc) — el artefacto puede contener backticks y `$(...)`. Nunca invoques una CLI externa sin **autorización explícita** del usuario.

**4. RECONCILE.** La salida del revisor es **dato, no veredicto**. Relee el artefacto contra cada hallazgo antes de clasificar — sellar el resultado del revisor es el mismo fallo que ignorarlo. Clasifica en este **orden de precedencia**:

| Clase | Qué hacer |
| :--- | :--- |
| **1. Malentendió el contrato** | Flaggeó algo porque **tu contrato** era vago. Arregla el contrato primero, reclasifica en el siguiente ciclo |
| **2. Válido + accionable** | Problema real que requiere cambio. Cámbialo, re-cicla |
| **3. Trade-off válido** | Real, pero el coste de arreglarlo supera el de aceptarlo. **Documéntalo** para que el usuario lo vea |
| **4. Ruido** | Es correcto bajo contexto que el revisor no tenía. Anótalo, pasa, y pregunta: ¿haber añadido ese contexto al contrato habría evitado el flag? |

**5. STOP.** Para cuando: la siguiente iteración solo devuelve hallazgos triviales o ya considerados · **3 ciclos completados** (escala a la persona, no grindes un cuarto solo) · el usuario dice *"ship it"*.

> [!important] Tres ciclos sin resolver son información sobre el artefacto
> Si tras 3 ciclos el revisor sigue sacando problemas sustanciales, el artefacto probablemente no está listo. **Vuelve al paso 2 y descompón** — no levantes el límite.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Estoy seguro, sáltate la duda" | La confianza correlaciona mal con la corrección en problemas nuevos. Los momentos de certeza son justo donde se esconden los puntos ciegos |
| "Lanzar un revisor es caro" | Depurar un commit equivocado en producción es más caro. El check está acotado; el bug no |
| "El revisor solo va a poner pegas" | Solo si no le acotas el prompt a "problemas que harían fallar esto bajo el contrato" |
| "Haré la duda al final con `/review`" | `/review` es una puerta final. Doubt-driven atrapa direcciones equivocadas temprano, cuando corregirlas es barato |
| "Dos opiniones siempre son mejores que una" | No cuando la segunda tiene menos contexto y produce ruido. ** Reconcilia, no te sometas** |

---

## 🚩 Red Flags

- Lanzar un revisor de contexto fresco para un renombrado de una línea.
- Tratar la salida del revisor como autoritativa sin releer el artefacto.
- Ciclar más de 3 veces sin escalar a la persona.
- Promptar al revisor con *"¿esto está bien?"* en vez de *"encuentra problemas"*.
- Saltarte la duda por presión de tiempo en una decisión de alto riesgo.
- **Teatro de duda** *(señal comprobable)*: en 2+ ciclos con hallazgos sustanciales, **cero hallazgos clasificados como accionables**. Estás validando, no dudando. Para y escala.
- **Saltarte cross-model en silencio** en un ciclo interactivo. Saltarlo está bien; **saltarlo en silencio no**.
- Pasar el CLAIM al revisor.
- Quitarle el contrato a la entrada del revisor.

---

## 🔗 Relaciones

**En el flujo de Addy:** se sitúa **dentro** de Build, en paralelo con [[incremental-implementation]] (observabilidad corre en paralelo, no después). Complementario de [[code-review-and-quality]] (post-hoc vs. in-flight). La fase RED de [[test-driven-development]] **es la duda hecha concreta**: un test que falla es un intento de refutación, y **satisface el paso DOUBT** para afirmaciones conductuales.

**Equivalencia Matt Pocock:** **no tiene equivalente.** Es la pieza más original del pack. Se parece en espíritu al *reverse review* de Superpowers (obra), que usa un revisor de tarea tras cada subagente; Addy lo generaliza a **cada decisión no trivial en vuelo** y añade la escalada cross-model.

```bash
npx skills add addyosmani/agent-skills --skill doubt-driven-development
```
