---
alias:
  - Skill code-review-and-quality
  - Addy Osmani Code Review
  - Five Axis Review
tags:
  - ai/skills
  - addy-osmani
  - ai/review
  - dev/code-review
  - dev/quality
source: https://github.com/addyosmani/agent-skills/blob/main/skills/code-review-and-quality/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Revisión de código **multi-eje con puertas de calidad**. Cada cambio se revisa antes de merge — **sin excepciones**, sea lo haya escrito tú, otro agente o una persona. Cubre cinco ejes: **corrección, legibilidad, arquitectura, seguridad y performance**.

> [!important] El estándar de aprobación
> Aprueba un cambio cuando **definitivamente mejora la salud global del código**, aunque no sea perfecto. El código perfecto no existe — el objetivo es la mejora continua. **No bloquees un cambio porque no sea exactamente como tú lo habrías escrito.** Si mejora el codebase y sigue las convenciones del proyecto, apruébalo.

---

## 🎯 ¿Cuándo usarla?

Antes de mergear cualquier PR o cambio · tras completar la implementación de una feature · cuando otro agente/modelo produjo código que debes evaluar · al refactorizar · **después de cualquier bug fix** (revisa tanto el fix como el test de regresión).

---

## 🔍 La revisión de 5 ejes

**1. Corrección.** ¿El código hace lo que dice hacer? ¿Coincide con la spec? ¿Casos límite (null, vacío, valores frontera)? ¿Rutas de error, no solo el happy path? ¿Pasa los tests — y testean los tests lo correcto? ¿Off-by-one, condiciones de carrera, inconsistencias de estado?

**2. Legibilidad y simplicidad.** ¿ otro ingeniero (o agente) lo entiende sin que el autor se lo explique? Nombres descriptivos y consistentes · control de flujo directo · organización lógica · sin trucos "elegantes" que deberían simplificarse. Preguntas duras:
- **¿Se puede en menos líneas?** 1.000 donde bastan 100 es un fallo.
- **¿Se ganan las abstracciones su complejidad?** No generalices hasta el tercer caso de uso.
- ¿Hay artefactos de código muerto (variables `_unused`, shims de retrocompatibilidad, comentarios `// removed`)?
- **¿Hay un condicional nuevo atornillado a un flujo no relacionado?** Eso es un *design smell*, no un nit: empuja la lógica a su propio helper, estado o política.
- **¿Se repiten condicionales sobre la misma forma?** Señalan un modelo o dispatcher que falta. Una rama "temporal" suele ser deuda permanente.

**3. Arquitectura.** ¿Encaja con el diseño del sistema? ¿Patrones existentes o uno nuevo justificado? ¿Límites de módulo limpios? ¿Dependencias fluyendo en la dirección correcta (sin ciclos)? **¿El refactor reduce complejidad o solo la reubica?** Cuenta los conceptos que un lector debe sostener para seguir el cambio; si una versión "más limpia" deja ese número igual, no es más limpia. **¿Se filtra lógica específica de feature en un módulo compartido?** ¿Los límites de tipo son explícitos, o hay `any`/`unknown`/casts que tapan un invariante poco claro?

**4. Seguridad.** ¿Input validado y saneado? ¿Secretos fuera de código, logs y VCS? ¿AuthN/AuthZ donde hace falta? ¿Queries parametrizadas (sin concatenación)? ¿Salidas codificadas contra XSS? ¿Dependencias sin vulnerabilidades conocidas? **¿Los datos de fuentes externas (APIs, logs, contenido de usuario, ficheros de config) se tratan como no confiables?**

**5. Performance.** ¿Patrones N+1? ¿Bucles sin cota o fetch sin restricción? ¿Operaciones síncronas que deberían ser async? ¿Re-renders innecesarios en UI? ¿Endpoints de lista sin paginación? ¿Objetos grandes creados en hot paths?

---

## 🔧 Remedios estructurales

Cuando marques un problema estructural, **propón el movimiento, no solo el problema**. Una review que solo dice *"esto es complejo"* deja al autor adivinando. Nombra una reestructuración concreta: reemplaza una cadena de condicionales por un modelo tipado o un dispatcher explícito · colapsa ramas duplicadas en un flujo más claro · separa orquestación de lógica de negocio · mueve lógica de feature a su módulo propietario · **reutiliza el helper canónico** en vez de un casi-duplicado a medida · hace explícito un límite de tipo para que desaparezca el ramaje aguas abajo · **borra un wrapper pasivo** que añade indirección sin clarificar la API · extrae un helper o divide un fichero grande.

> [!tip] Prefiere el remedio que elimina piezas móviles
> Antes que uno que reparte la misma complejidad alrededor.

---

## 📏 Dimensionado de cambios

```
~100 líneas cambiadas   → Bien. Revisable de una sentada.
~300 líneas cambiadas   → Aceptable si es un solo cambio lógico.
~1000 líneas cambiadas  → Demasiado grande. Divídelo.
```

**Vigila el tamaño del fichero, no solo el del diff.** Un diff pequeño todavía puede empujar un fichero más allá de un límite sano — alrededor de **1.000 líneas totales** en un fichero es una señal de inspección, no un tope duro. Descompón primero, luego añade.

**Estrategias de división:** *Stack* (un cambio pequeño, el siguiente sobre él) · *Por grupo de ficheros* (revisores distintos) · *Horizontal* (código compartido primero, luego los consumidores) · *Vertical* (rebanadas full-stack de la feature).

**Separa refactor de feature.** Un cambio que refactoriza y añade comportamiento **son dos cambios**. Las limpiezas pequeñas (renombrar una variable) pueden incluirse a discreción del revisor.

## 📝 Descripciones de cambio
Cada cambio necesita una descripción que se sostenga sola en el historial. **Primera línea:** corta, imperativa, autónoma. **Cuerpo:** qué cambia y por qué, con el contexto y decisiones que no se ven en el código. **Anti-patrones:** *"Fix bug"*, *"Add patch"*, *"Moving code from A to B"*, *"Phase 1"*.

---

## 🔄 El proceso de revisión

1. **Entiende el contexto** antes de mirar código: ¿qué intenta lograr? ¿qué spec/tarea implementa? ¿qué cambio de comportamiento se espera?
2. **Revisa los tests primero** — revelan intención y cobertura. *Responde a la última pregunta por experimento, no por lectura:* invierte una condición que el cambio añade, corre la suite, restaura el fichero. **Una mutación que sigue verde es un hallazgo**: nombra el caso de test que falta.
3. **Revisa la implementación** con los 5 ejes.
4. **Categoriza los hallazgos con severidad:**

| Prefijo | Significado | Acción del autor |
| :--- | :--- | :--- |
| *(sin prefijo)* | Cambio **requerido** | Debe atenderse antes del merge |
| **Critical:** | **Bloquea** el merge | Vulnerabilidad, pérdida de datos, funcionalidad rota |
| **Nit:** | Menor, opcional | Puede ignorarse — formato, preferencias de estilo |
| **Optional:** / **Consider:** | Sugerencia | Vale la pena, no requerido |
| **FYI** | Informativo | Sin acción — contexto futuro |

> [!important] Empieza por lo que importa
> Ordena los hallazgos por **palanca**: corrección y seguridad primero, luego regresiones estructurales y simplificaciones perdidas, luego el resto. **No entierres un problema real bajo nits cosméticos** — unos pocos comentarios de alta convicción batan una lista larga. Si tienes un problema estructural y diez nits, **el problema estructural *es* la review**.

5. **Verifica la verificación** — ¿qué tests se corrieron? ¿pasó el build? ¿se probó manual? ¿hay screenshots para cambios de UI?

### Patrón de revisión multi-modelo
Modelo A escribe el código → **Modelo B revisa** corrección y arquitectura → Modelo A atiende el feedback → la persona decide. Distintos modelos tienen **puntos ciegos distintos**.

---

## ⏱️ Velocidad de revisión

Las revisiones lentas bloquean a equipos enteros. **Responde en un día hábil — ese es el máximo, no el objetivo.** Prioriza respuestas individuales rápidas sobre el visto-bueno final rápido. Para cambios grandes, **pide al autor que los divida** en vez de revisar un changeset masivo.

## ⚖️ Desacuerdos
Jerarquía para resolver disputas: **los hechos técnicos y los datos** prevalecen sobre opiniones y preferencias → **las guías de estilo** son la autoridad absoluta en materia de estilo → el diseño de software se evalúa por principios de ingeniería, no por preferencia personal → la consistencia con el codebase es aceptable si no degrada la salud global.

> [!warning] No aceptes *"ya lo limpio luego"*
> La experiencia dice que la limpieza diferida rara vez ocurre. **Exige limpieza antes de entregar**, salvo emergencia genuina. Si los problemas circundantes no se pueden atacar en este cambio, exige **reportar un bug con autoasignación**.

## 🧠 Honestidad en la revisión
**No rubber-stamp** ("LGTM" sin evidencia no ayuda a nadie) · **no suavices problemas reales** ("esto podría ser una preocupación menor" sobre un bug que llegará a producción es deshonesto) · **cuantifica cuando puedas** ("esta query N+1 añade ~50 ms por item" es mejor que "podría ser lento") · **empuja atrás** ante enfoques con problemas claros (la adulación es un modo de fallo) · **acepta el override con elegancia** si el autor tiene el contexto completo. Comenta sobre el código, no sobre las personas.

---

## 📦 Disciplina de dependencias

**Antes de añadir cualquier dependencia:** ¿el stack existente ya lo resuelve? · ¿qué tamaño tiene (impacto en bundle)? · ¿se mantiene activamente? · ¿vulnerabilidades conocidas (`npm audit`)? · ¿licencia compatible? **Regla: prefiere la librería estándar y las utilidades existentes. Cada dependencia es un pasivo.**

**Actualizar una dependencia** es un cambio de código como cualquier otro, y **los upgrades más riesgosos son los que se mergean en bloque con un mensaje tipo *"bump deps"***:
1. **Lee el changelog, no solo el número de versión.** Semver es una promesa que el mantenedor puede no haber cumplido — un *"patch"* puede traer cambio de comportamiento.
2. **Una dependencia por cambio.** Si un bump masivo rompe el build, has perdido cuál lo hizo.
3. **Deja que los tests decidan.** Verificado por una suite verde antes *y* después, no por "instaló bien".
4. **Cuidado con el grafo transitivo.** Revisa el diff del **lockfile**, no solo de `package.json`.
5. **Mantén el lockfile honesto.** Commitéalo, revisa su diff, **nunca lo edites a mano**.

---

## 🚩 Red Flags

- PRs mergeados sin review.
- Review que solo comprueba si los tests pasan.
- *"LGTM"* sin evidencia de review real.
- Cambios sensibles a seguridad sin review enfocada en seguridad.
- PRs grandes que son "demasiado grandes para revisar bien".
- Sin test de regresión en PRs de bug fix.
- Comentarios de review **sin etiqueta de severidad**.
- Aceptar *"ya lo arreglo luego"*.
- Un refactor que mueve código sin reducir los conceptos que un lector debe sostener.
- Bulk *"bump dependencies"* sin revisar changelog ni aislar por paquete.
- Lockfile editado a mano, sin commitear, o mergeado sin revisar su diff.

---

## 🔗 Relaciones

**En el flujo de Addy:** puerta REVIEW. Sus ejes 4 y 5 delegan en detalle en [[security-and-hardening]] y [[performance-optimization]]. Su regla de remedios estructurales es casi idéntica a [[code-simplification]]. La ejecuta la persona [[code-reviewer]] (que es esta misma skill en formato de checklist), y lo ejecuta [[shipping-and-launch]] como puerta final, con las *personas* [[code-reviewer]] + [[security-auditor]] + [[test-engineer]] en paralelo.

**Equivalencia Matt Pocock:** corresponde a [[code-review]]. **Diferencias clave:**
- Addy tiene **5 ejes explícitos y ponderados**; Matt audita en dos ejes (estándares documentados del repo vs. spec) usando **subagentes en paralelo**.
- Addy añade **etiquetas de severidad** (Critical / requerido / Nit / Optional / FYI) y **tamaño de cambio** (~100 líneas) como norma explícita; Matt no pone números.
- Addy gobierna **dependencias** en detalle (changelogs, lockfiles, una por cambio); Matt lo delega a `code-reviewer`.
- Ambos insisten en **revisar los tests primero**.

```bash
npx skills add addyosmani/agent-skills --skill code-review-and-quality
```
