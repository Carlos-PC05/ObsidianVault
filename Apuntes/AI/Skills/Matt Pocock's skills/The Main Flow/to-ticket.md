---
alias:
  - /to-tickets, To Tickets
tags:
  - ai/skills
  - dev/tools
  - workflow
  - ai/the-main-flow
source: https://www.aihero.dev/skills-to-tickets
---
> **Comando:** `/to-tickets`

---

## 📌 ¿Qué hace?

Coge un plan, un spec o la conversación actual y lo **divide en tickets pequeños** en tu gestor de tareas. Cada ticket declara sus **bloqueos** (qué otros tickets deben terminar antes de poder empezarlo).

> [!IMPORTANT] Cada ticket es una **"tracer bullet"**: un camino estrecho pero _completo_ por todas las capas del cambio (schema, API, UI, tests), demostrable por sí solo en cuanto se integra. Rompe con el reflejo habitual de trocear por capas. Además, cada ticket se dimensiona para caber en **una sola ventana de contexto nueva**, porque quien lo recogerá es una sesión que nunca ha visto el spec.

---

## ⏱️ ¿Cuándo usarla?

Se invoca manualmente (`/to-tickets`); el agente no la usa por iniciativa propia.

|Tu situación|Qué ejecutar|
|:--|:--|
|Hay un spec y el build abarca varias sesiones|`/to-tickets` o `/to-tickets #<spec_issue>`|
|El plan solo existe en la conversación, nunca se escribió|`/to-tickets` lee el hilo directamente|
|El cambio entero cabe en una sola ventana de contexto|Saltar tickets, usar `/implement`|
|Nada está decidido todavía|`/grill-with-docs`, luego `/to-spec`|
|Un mapa de `/wayfinder` ya se cerró|`/to-spec` primero (para colapsar el mapa), luego `/to-tickets`|

> [!TIP] Los tickets que produce ya vienen listos para agentes por construcción. **No pases `/triage` sobre ellos** — triage es para trabajo que llega de fuera.

---

## 🧱 Prerrequisitos

`to-tickets` publica en un tracker, así que `setup-matt-pocock-skills` debe haber configurado uno para el repo (GitHub, Linear, o markdown local en `.scratch/`), junto con el vocabulario de etiquetas de triage.

---

## 🔀 Tracer bullets, no capas

- **Slice horizontal:** un ticket = una capa. Nada funciona hasta que todas las capas están listas; los criterios de aceptación de un ticket dependen de trabajo que posee otro ticket.
- **Slice vertical (tracer bullet):** un ticket = un camino fino por _todas_ las capas a la vez. Verificable de forma aislada.

> [!WARNING] Es el error más común. Un equipo citado en el artículo troceó un stack de 26 tickets por capas (corpus, producer, aggregator, selector) y obtuvo ~20 ejecuciones de agente por ticket cerrado, un 75% de ellas retrabajo. Su propio post-mortem atribuyó todos los fallos al slicing horizontal, no a las implementaciones.

Antes de publicar nada, la skill:

1. Detecta trabajo de **prefactoring** ("haz el cambio fácil, luego haz el cambio fácil") y lo ordena primero.
2. Presenta la lista numerada y **pregunta** si la granularidad y los bloqueos son correctos. Nada llega al tracker sin tu aprobación — ese es el momento de objetar.

---

## 🔗 Bloqueos (_blocking edges_)

Los edges son el punto central del artefacto.

|Tracker|Dónde viven los bloqueos|Cómo se trabajan|
|:--|:--|:--|
|Markdown local|Texto en `.scratch/<feature>/issues/<NN>-<slug>.md`, numerado con bloqueadores primero|A mano, de arriba abajo|
|Real (GitHub, Linear)|Enlaces nativos de bloqueo, o sub-issues|Cualquier ticket sin bloqueos pendientes está en la **frontera** y se puede coger|

> [!NOTE] `to-tickets` produce el artefacto; ejecutarlo (una sesión a la vez, o una flota en paralelo) es trabajo tuyo, no de la skill.

---

## 🧩 La excepción del refactor amplio

Un **refactor amplio** (_wide refactor_) es un cambio mecánico único —renombrar una columna, retipar un símbolo compartido— cuyo radio de impacto abarca todo el código, así que ningún slice vertical puede quedar en verde.

`to-tickets` lo secuencia como **expand–contract**:

- **Expand:** añade la forma nueva junto a la vieja, sin romper nada.
- **Migrate:** mueve los call sites por lotes según el radio de impacto (por paquete, por directorio), un ticket por lote, bloqueado por el expand. El CI sigue en verde porque la forma vieja aún existe.
- **Contract:** borra la forma vieja cuando no queda ningún caller, en un ticket bloqueado por todos los lotes de migración.

> Si ni los lotes pueden quedar en verde por separado, comparten una rama de integración y todos bloquean un ticket final de integrar-y-verificar. El verde solo se promete ahí.

---

## ⚠️ Problemas Frecuentes y Advertencias

- **Demasiados tickets para un cambio pequeño:** la fricción más reportada; el modelo tiende a atomizar y pierde la agrupación con sentido. Pídele en el paso de quiz que fusione. Si todo cabe en un contexto, no necesitas esta skill — ve directo a `/implement`.
- **Tickets divididos por capa (todo el schema en uno, toda la API en otro):** sigue pasando a veces. En el quiz, pregunta por cada ticket "¿qué puedo demostrar cuando esté listo?" — si no hay respuesta, es un slice horizontal. Añadir una línea de "demo path" por ticket ayuda a empujar hacia lo vertical.
- **En GitHub los tickets no se crean como sub-issues del spec:** bug conocido y sin arreglar, peor en Codex que en Claude. Solución manual: `gh issue create --parent <n>` y `gh issue edit <parent> --add-sub-issue <n>` después.
- **"Blocked by" escrito en el cuerpo en vez de como enlace real de bloqueo:** usar `gh issue create --blocked-by 12,15` — GitHub sí soporta bloqueos nativos.
- **Tickets locales, ¿dónde van?:** ahora un archivo por ticket en `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, en orden de dependencia (antes era un único `tickets.md` compartido, que causaba condiciones de carrera con agentes en paralelo). El prefijo `NN` es un ID real: `/implement 03` funciona directo.
- **Truncado al leer specs muy grandes:** no hay copia local de respaldo si el tracker trunca. No hagas `clear` ni `compact` entre `/to-spec` y `/to-tickets` — ejecútalas en la misma ventana de contexto.
- **Criterios de aceptación que no comprueban nada (pasan antes de hacer el trabajo):** ocurre porque la plantilla no exige que puedan fallar. Revisa a mano: para cada criterio, nombra la observación que lo haría fallar y confirma que falla en el commit base.
- **¿Cómo se ejecutan los tickets publicados?:** no hay auto-dispatch. Dispatch manual: mira el tablero, cuenta los tickets sin bloqueos abiertos, abre esa cantidad de sesiones de agente (un ticket por contexto limpio). `/implement` no cierra ni marca el ticket al terminar — actualizar el estado es tarea tuya.

---

## ✅ ¿Cómo saber si funcionó?

- Cada ticket responde a "¿qué puedo demostrar cuando esté listo?" con comportamiento, no con una capa.
- La lista vuelve numerada, con línea "Blocked by" en cada ticket, antes de publicarse.
- El ticket de arriba no tiene bloqueos y se puede empezar de inmediato.
- Nada en el cuerpo de un ticket es una ruta de archivo o número de línea (salvo un snippet que produjo un prototipo).
- Cada ticket se lee como algo que una sesión nueva podría terminar sin ti presente.
- El prefactoring detectado va al frente del orden, no mezclado con los tickets de feature.

---

## 🔗 Dónde encaja en el Flujo Principal

`/grill-with-docs` ➔ `/to-spec` ➔ **`/to-tickets`** ➔ `/implement` ➔ `/code-review`

> Aguas arriba está `/to-spec`, que le entrega un spec ya cerrado — mantenlos en una sola ventana de contexto sin interrupciones. Aguas abajo está `/implement`, que construye un ticket por sesión nueva, aplicando `/tdd` para los tests y cerrando con `/code-review`.