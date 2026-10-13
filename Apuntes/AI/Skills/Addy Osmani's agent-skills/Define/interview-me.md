---
alias:
  - Skill interview-me
  - Addy Osmani Interview Me
tags:
  - ai/skills
  - addy-osmani
  - ai/define
  - dev/discovery
  - dev/requirements
source: https://github.com/addyosmani/agent-skills/blob/main/skills/interview-me/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Extrae lo que el usuario realmente quiere**, no lo que cree que debería querer. Lo hace con un **interrogatorio de una pregunta a la vez**, cada una con la hipótesis del agente pegada, hasta alcanzar ~95 % de confianza sobre la intención real. El momento más barato para encontrar esa brecha es **antes de que exista plan, spec o código**.

---

## 🎯 ¿Cuándo usarla?

Aplícala cuando:
- A la petición le falta **quién** es el usuario, **por qué** la quiere, qué es el **éxito**, o cuál es la **constraint** limitante.
- La petición es **convencional en vez de específica** (*"hazme un dashboard"*, *"hazlo más rápido"*) y no puedes desempaquetarla sin adivinar.
- Tienes la tentación de arrancar con supuestos que no has declarado.
- Dos valores razonables están en tensión (simplicidad vs. flexibilidad, coste vs. velocidad) y el usuario no ha dicho cuál optimiza.
- El usuario la invoca explícitamente: *"interview me"*, *"grill me"*, *"¿estamos seguros?"*, *"stress-test my thinking"*.

**Cuándo NO usarla:**
- Peticiones inequívocas y autocontenidas (*"renombra esta variable"*).
- El usuario pidió explícitamente velocidad sobre verificación.
- Peticiones puramente informativas (*"¿cómo funciona X?"*).
- Operaciones mecánicas (renombrados, formateo, mover archivos).
- Ya tienes ≥95 % de confianza.

> [!warning] Restricción de carga
> Necesita un **usuario vivo y reactivo**. **No la invoques en contextos no interactivos** (CI, `/loop`, runs autónomos). Si estás en uno de esos y la petición es ambigua, marca el bloqueo para el usuario en vez de adivinar.

---

## 🔄 El proceso (6 pasos)

1. **Hipotetiza, con número de confianza.** Antes de preguntar nada, escribe tu mejor lectura en **una frase** + un número honesto (0–100 %). El número te obliga a ser honesto. Por debajo de ~70 %, añade la razón en la misma línea: qué sigue sin resolverse.
2. **Una pregunta a la vez, cada una con tuguess.** Formato `Q: <pregunta>` / `GUESS: <tu hipótesis y el razonamiento que la produjo>`. Espera la reacción antes de la siguiente. *Por qué una a una:* el usuario no puede reaccionar si entierras las hipótesis en una lista; los lotes invitan a leer por encima y responder en superficie; la tercera pregunta suele depender de la primera; y su energía para pensar con cuidado es finita.
3. **Escucha el "quiero vs. debería querer".** Las respuestas peligrosas son las que suenan a *best practice* sin sustancia (*"escalable"*, *"arquitectura limpia"*), las que delegan en la convención (*"como se hace en la mayoría de apps"*) y las marcas de sofisticación (*"debería probablemente…"*). Cuando las detectes: *"Si no tuvieras que justificar esto ante nadie, ¿qué querrías realmente?"* — esa sola pregunta suele hacer más trabajo que las cinco anteriores.
4. **Reformula la intención en sus propias palabras.** 5–8 líneas con su léxico, estructuradas para que confirme o corrija línea a línea: `Resultado` · `Usuario` · `Por qué ahora` · `Éxito` · `Constraint` · `Fuera de alcance`.
5. **Confirma — un "sí" explícito, no un "lo que tú veas".** **No cuentan como sí:** *"lo que tú creas"* (es delegación, y si delegas tampoco tienes 95 %), *"suena bien"* (pregunta *"¿algo que refinar?"*), *"venga, vamos"* (salida cortés), o el silencio seguido de *"empecemos"*.
6. **Turno terminal y parada (CRÍTICO).** Al recibir el sí explícito: entrega la intención confirmada, **ofrece** rutas siguientes (guardar en `docs/intent/[topic].md`, redactar spec con [[spec-driven-development]], o pasar a planificación) y **DETIENE EL TURNO**. No invoquemos herramientas ni empieces trabajo aguas abajo en ese turno.

---

## 💡 La condición de parada del 95 %

Se acaba cuando puedes responder **sí** a esto:

> *¿Puedo predecir la reacción del usuario a las próximas tres preguntas que haría?*

Es un test verificable, no una corazonada. Y tiene un suelo: si llevas varias rondas y sigues sin poder predecir, eso es información sobre la petición, no una razón para seguir insistir. Para y di: *"Te he hecho X preguntas y sigo sin poder predecir tus reacciones. Falta algo de fondo. ¿Damos un paso atrás?"*

> [!tip] La salida es una *Statement of Intent* confirmada
> Ese es el entregable. Las specs, los planes y las listas de tareas son **aguas abajo**: consumen la intención que esta skill produce.

---

## ⚠️ Puntos de atención

|[Racionalización | Realidad |
| :--- | :--- |
| "La petición ya está clara" | Si no puedes escribir el resultado deseado en una frase ahora mismo, no está clara. Ejecuta el paso 1 antes de decidir. |
| "Hacer muchas preguntas desperdicia su tiempo" | 4–6 preguntas dirigidas cuestan poco. Construir lo equivocado cuesta muchísimo, y lo paga el usuario. |
| "Ya lo averiguaré sobre la marcha" | Los costes de cambio tras existir código son 10× los actuales. Descubrir durante la implementación es retrabajo. |
| "Dijo 'lo que tú veas', así que decido" | Eso es delegación, no decisión. Pregunta con dos opciones concretas. |
| "Debería darle varias opciones para elegir" | Las opciones funcionan cuando el usuario ya sabe qué quiere. Listar opciones **amplía** la búsqueda; preguntar la **narrow**a. |
| "Si pongo mi guess lo estoy condicionando" | Condicionar es el punto. Reaccionar es más rápido que generar desde cero. El riesgo es la adulación, no el guiado. |
| "El usuario dijo sí, terminamos" | Si el sí siguió a un reformulado vago, es hueco. Reformula en concreto y vuelve a confirmar. |

---

## 🚩 Red Flags

- Tres o más preguntas en un solo mensaje: eso es **batching**, no interrogatorio.
- Una pregunta sin hipótesis pegada: eso es **encuesta**, no compromiso.
- Aceptar *"lo que tú creas"* como respuesta terminal.
- Producir spec, plan o lista de tareas antes de la confirmación explícita.
- Invocar herramientas o arrancar trabajo aguas abajo al confirmar, en vez de parar el turno.
- Aceptar una respuesta de señalización de sofisticación (*"escalable"*, *"limpio"*, *"moderno"*) sin sondear.
- Tres o más rondas sin que tu confianza suba visiblemente: estás preguntando lo que no es.

---

## 🔗 Relaciones

**En el flujo de Addy:** downstream de esta vienen [[idea-refine]] y [[spec-driven-development]]. Es el extremo opuesto a [[doubt-driven-development]]: `interview-me` extrae intención *antes* de decidir; `doubt-driven` revisa artefactos *después* de decidirlos. Orogonal a [[source-driven-development]]: una aclara qué quiere el usuario, la otra verifica hechos de frameworks.

**Equivalencia Matt Pocock:** es el gemelo de [[grill-me]] y [[grilling]], con dos diferencias: Matt pone el foco en el *design tree* (resolver dependencias en orden) y en leer el código antes de preguntar; Addy pone el foco en el **número de confianza** y en distinguir *"quiero"* de *"debería querer"*.

```bash
npx skills add addyosmani/agent-skills --skill interview-me
```
