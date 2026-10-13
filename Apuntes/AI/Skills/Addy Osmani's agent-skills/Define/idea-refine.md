---
alias:
  - Skill idea-refine
  - Addy Osmani Idea Refine
tags:
  - ai/skills
  - addy-osmani
  - ai/define
  - dev/design
  - dev/discovery
source: https://github.com/addyosmani/agent-skills/blob/main/skills/idea-refine/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Refina ideas crudas en conceptos nítidos y accionables** mediante pensamiento **divergente y convergente estructurado**. Genera variaciones con lentes explícitos, las evalúa y colisiona contra tres criterios, y **saca a la luz los supuestos ocultos** antes de que se conviertan en un plan.

---

## 🎯 ¿Cuándo usarla?

- Tienes un concepto o idea todavía vaga y quieres **expandir opciones antes de converger**.
- Necesitas **test de estrés** sobre los supuestos de un plan antes de comprometerte.
- Se dispara con: *"ayúdame a refinar esta idea"*, *"ideemos sobre [concepto]"*, *"stress-test my plan"*.

> [!note] Encaje con `interview-me`
> Es la fase siguiente. `interview-me` extrae la intención real; `idea-refine` genera **variaciones contra esa intención ya explícita**. Si la petición sigue sin dueño ni criterio de éxito, vuelve antes a `interview-me`.

---

## 🔄 Las 3 fases

### Fase 1 — Entender y expandir (divergente)
1. **Reformula** la idea como un *"How Might We"* claro. Esto fuerza claridad sobre qué se está resolviendo realmente.
2. **Haz 3–5 preguntas de afilado — ni una más.** ¿Para quién, específicamente? ¿Cómo se ve el éxito? ¿Constraints reales (tiempo, tech, recursos)? ¿Qué se intentó antes? ¿Por qué ahora? **No avances hasta entender para quién es y qué es el éxito.**
3. **Genera 5–8 variaciones** con estas lentes: **Inversión** (*"¿y si hiciéramos lo contrario?"*) · **Quitar constraint** · **Cambio de audiencia** · **Combinación** · **Simplificación** (*"¿cuál es la versión 10× más simple?"*) · **Versión 10×** · **Lente de experto** (*"¿qué es obvio para un *[dominio]* que un outsider no vería?"*).

> [!tip] Dentro de un codebase
> Usa `Glob`, `Grep` y `Read` para escanear arquitectura, patrones y *prior art*. **Ancla las variaciones en lo que existe de verdad** y referencia archivos concretos.

### Fase 2 — Evaluar y converger
1. **Agrupa** las ideas que resonaron en **2–3 direcciones distintas** (que se sientan realmente diferentes, no variaciones del mismo tema).
2. **Test de estrés** contra tres criterios: **Valor de usuario** (¿quién se beneficia y cuánto? ¿pastilla o vitamina?) · **Viabilidad** (coste técnico y de recursos, ¿cuál es la parte más difícil?) · **Diferenciación** (¿qué lo hace genuinamente distinto? ¿alguien cambiaría desde su solución actual?).
3. **Saca a la luz los supuestos ocultos.** Para cada dirección, nombra explícitamente: **qué estás apuesta que es cierto pero no has validado** · **qué podría matar esta idea** · **qué estás eligiendo ignorar** (y por qué está bien por ahora). *Aquí es donde falla la mayoría de ideación. No te lo saltes.*

### Fase 3 — Afilar y entregar
Produce un **one-pager en markdown** (el output es un artefacto, no una conversación):

```markdown
# [Nombre de la idea]
## Problema               → un "How Might We" de una frase
## Dirección recomendada  → la elegida y por qué (2–3 párrafos máx.)
## Supuestos clave a validar  → [ ] cada uno con cómo testearlo
## Alcance MVP            → la versión mínima que testea el supuesto central
## No vamos a hacer (y por qué)
## Preguntas abiertas
```

> [!important] La lista "No Doing" es probablemente la parte más valiosa
> El foco consiste en **decir que no a buenas ideas**. Hacer explícitos los trade-offs es el trabajo.

---

## ⚠️ Anti-patrones

- **No generar 20+ ideas.** Calidad sobre cantidad: 5–8 variaciones bien consideradas baten a 20 superficiales.
- **No ser una máquina de decir sí.** Empuja atrás sobre ideas débiles con especificidad y amabilidad.
- **No saltarse "¿para quién es esto?"** Toda buena idea empieza por una persona y su problema.
- **No producir un plan sin supuestos a la luz.**
- **No sobre-ingenierizar el proceso.** Tres fases, cada una haciendo bien una cosa.
- **No listar ideas — contar una historia.** Cada variación debe tener un motivo para existir.
- **No ignorar el codebase.**

---

## 💡 Filosofía de la skill

- *La simplicidad es la sofisticación definitiva.* Empuja hacia la versión más simple que aún resuelve el problema real.
- *Empieza por la experiencia de usuario y trabaja hacia atrás hasta la tecnología.*
- *Dile que no a 1.000 cosas.* El foco gana a la amplitud.
- *Cuestiona cada supuesto.* *"Así es como se hace"* no es una razón.
- *Muestra el futuro — no des solo caballos mejores.*

**Tono:** directo, pensado, algo provocador. Eres un partner de pensamiento con criterio, no un facilitador leyendo un guion.

---

## 🔗 Relaciones

**En el flujo de Addy:** upstream de [[interview-me]], downstream de [[spec-driven-development]].

**Equivalencia Matt Pocock:** solapa con [[wayfinder]] (explorar el espacio de decisión antes de commitear) y [[prototype]] (probar una dirección antes de construirla). Addy aporta algo que Matt no tiene explícito: **la fase divergente con lentes nombradas y la evaluación de supuestos con estrategia de validación**.

```bash
npx skills add addyosmani/agent-skills --skill idea-refine
```
