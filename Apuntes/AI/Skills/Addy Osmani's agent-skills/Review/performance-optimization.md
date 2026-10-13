---
alias:
  - Skill performance-optimization
  - Addy Osmani Performance Optimization
  - Core Web Vitals
tags:
  - ai/skills
  - addy-osmani
  - ai/review
  - dev/performance
  - ui-ux
source: https://github.com/addyosmani/agent-skills/blob/main/skills/performance-optimization/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Optimiza el rendimiento en frontend, backend, queries y bases de datos. **Mide antes de optimizar.** El trabajo de performance sin medición es adivinar — y adivinar lleva a la optimización prematura que añade complejidad sin mejorar lo que importa.

> [!tip] `/webperf`
> Este comando es la puerta de entrada al pack: lanza la *persona* `web-performance-auditor` con dos modos (Quick y Deep) y una **regla de honestidad de métricas**.

---

## 🎯 ¿Cuándo usarla?

- Existen requisitos de performance en la spec (presupuestos de carga, SLAs de tiempo de respuesta).
- Usuarios o monitoring reportan lentitud.
- Core Web Vitals por debajo de los umbrales.
- Sospechas que un cambio introdujo una regresión.
- Features que manejan datasets grandes o tráfico alto.

**Cuándo NO:** no optimices antes de tener evidencia de un problema. La optimización prematura añade complejidad que cuesta más que la performance que gana.

---

## 📊 Objetivos de Core Web Vitals

| Métrica | Bueno | Necesita mejora | Pobre |
| :--- | :--- | :--- | :--- |
| **LCP** (Largest Contentful Paint) | ≤ 2,5 s | ≤ 4,0 s | > 4,0 s |
| **INP** (Interaction to Next Paint) | ≤ 200 ms | ≤ 500 ms | > 500 ms |
| **CLS** (Cumulative Layout Shift) | ≤ 0,1 | ≤ 0,25 | > 0,25 |

---

## 🔄 El flujo de optimización

```
1. MEDIR    → Establecer baseline con datos reales
2. IDENTIFICAR → Hallar el cuello de botella real (no el supuesto)
3. ARREGLAR  → Atacar ese cuello de botella concreto
4. VERIFICAR → Medir de nuevo; mantener o revertir
5. PROTEGER  → Añadir monitoring o tests contra regresión
```

### 1. Medir — dos enfoques complementarios, usa ambos
- **Sintético** (Lighthouse, pestaña Performance de DevTools): condiciones controladas y reproducibles. Ideal para detección de regresión en CI y para aislar issues concretos.
- **RUM** (librería `web-vitals`, CrUX): datos reales de usuario en condiciones reales. **Requerido para validar que un fix mejoró de verdad la experiencia.**

### Por dónde empezar a medir
Usa el **síntoma** para decidir qué medir: primera carga (bundle grande? TTFB? render-blocking? DNS/TCP/TLS?), interacción sluggish (tareas largas >50 ms? re-renders?), tras navegación (data fetching en cascada?), o backend (un endpoint lento → queries e índices; todos lentos → pool/CPU; intermitente → contención de locks, pausas de GC, deps externas).

### 2. Identificar el cuello de botella
Los sospechosos habituales: **N+1 queries** · **fetch de datos sin cota** · **queries que ignoran su índice** · **agotamiento del pool de conexiones** · **caché ausente**. En frontend: **optimización de imágenes** · **re-renders innecesarios** · **bundle grande**.

### 3. Arreglar
Los puntos clave que la skill_uid explica con detalle:
- **"Añade un índice" es la conjetura; `EXPLAIN ANALYZE` es la medición.** Un `Seq Scan` donde esperabas índice, un `rows=` estimado off por un orden de magnitud, y un nodo `Sort` encima del escaneo piden cada uno un arreglo distinto; un `rows=` malo significa **estadísticas obsoletas → `ANALYZE`, no un índice**. Un índice no ayuda con un valor dominante de baja selectividad (usa índice parcial), un wildcard inicial (trigram o full-text), o una función aplicada a la columna (indexa la expresión). **Cada índice grava todas las escrituras — re-corre el plan; un índice que no cambió el plan es un revert.**
- **Pool de conexiones.** La firma es: *todos* los endpoints se ralentizan a la vez, el tiempo se pasa esperando conexión en vez de ejecutar, y la BD muestra sesiones mayormente idle. **Un pool más grande no es más rápido: mueve la cola a la base de datos, donde es más difícil de ver.** Con instancias sin cota (serverless), multiplexa con un proxy.
- **Caché.** Cachea lo **caro de producir y leído muchas más veces de lo que cambia**; cachear una query ya rápida añade un salto de red y un bug de obsolescencia a cambio de nada. **Toda entrada que cambia la respuesta va en la clave** (tenant, locale, permisos, flags): una clave que omite el visor es cómo los datos de un usuario se sirven a otro. Elige **una** estrategia de invalidación y declara la ventana de obsolescencia aceptable.

### 4. Verificar — *keep or revert*
Un fix es una hipótesis hasta que lo remedes. **Re-mide igual que mediste el baseline**: mismo comando, mismas condiciones, mismo presupuesto fijo. Un baseline en caché fría contra un resultado en caché caliente **mide la caché, no tu cambio**.

| Resultado vs. baseline | Acción |
| :--- | :--- |
| Pasó el umbral, tests verdes | **Mantener.** Commitea con los números antes/después |
| Dentro del ruido (sin cambio medible) | **Revertir** |
| Peor | **Revertir** |
| Mejor, pero un test se puso rojo | **Revertir.** Una regresión vistiendo ropa de victoria |

> [!important] *"Neutral" es un revert, no un keep*
> Este es el paso que los equipos se saltan: el cambio ya está escrito, tirarlo se siente como desperdicio, así que aterriza sin medirse, y el codebase acumula complejidad que nunca compró nada. **El código que conservas lo mantienes para siempre. Que se pague.**

- **Cambia una cosa a la vez.** Tres optimizaciones juntas producen un número que no puedes atribuir.
- **Gana al ruido, no solo a la media.** Una ganancia del 3 % dentro de ±5 % de varianza no es una ganancia; es otra muestra.
- **La corrección condiciona a la métrica.** Una "optimización" que gana saltándose trabajo que el producto necesitaba (quitar una validación, cachear lo que debe estar fresco, borrar un `await` que sostenía peso) es una regresión.

> [!tip] Registra cada intento, **incluidos los revertidos**
> El trabajo revertido no deja rastro en git, y por eso la misma idea muerta se reintenta el trimestre siguiente. Mantén un ledger corto (`PERF.md` o una sección en la descripción del PR):

| Idea | Baseline → Resultado | Veredicto | Por qué |
| :--- | :--- | :--- | :--- |
| Memoizar el componente fila | INP 240ms → 235ms | revertido | Dentro del ruido (±15ms). Las filas no eran el cuello |
| Virtualizar la lista | INP 240ms → 90ms | **mantenido** | Las tareas largas desaparecieron de la traza |

### 5. Proteger contra regresión
Guarda **la métrica que el usuario realmente siente**, con dos capas: **gate sintético en CI** (presupuesto de performance; repite mediciones ruidosas o compara mediana/tendencia para que la varianza no convierta el gate en un check flaky) y **monitorización de campo** (alerta ante un movimiento p75 significativo en RUM; usa datos atribuidos de `web-vitals` para localizar la causa, y trata la ventana rodante de CrUX como confirmación, no como alerta inmediata).

Presupuestos típicos: JS bundle < 200 KB gzip · CSS < 50 KB · imágenes < 200 KB sobre el fold · fuentes < 100 KB · respuesta de API < 200 ms (p95) · TTI < 3,5 s en 4G · Lighthouse Performance ≥ 90.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Optimizamos luego" | La deuda de performance se acumula. Arregla los anti-patrones obvios ahora |
| "En mi máquina va rápido" | Tu máquina no es la del usuario. Perfila en hardware y red representativos |
| "Esta optimización es obvia" | Si no mediste, no lo sabes |
| "Los usuarios no notan 100 ms" | La investigación muestra que los retrasos de 100 ms impactan la conversión. Notan más de lo que crees |
| "El query está lento, añade un índice" | **Lee el plan primero.** El índice puede ya existir y ser inutilizable |
| "Solo cacheémoslo" | Cachear algo ya barato no compra nada y añade un bug de obsolescencia |
| "Subamos el pool, nos quedamos sin conexiones" | Un pool mayor que lo que la BD puede servir mueve la cola a un sitio menos visible |
| "No ayudo mucho, pero tampoco estorba" | **Los cambios neutrales son un revert** |
| "Ya lo escribimos, mejor dejarlo" | Coste hundido. A la medición no le importa cuánto tardó |

---

## 🚩 Red Flags

- Optimización sin datos de perfil que la justifiquen.
- Patrones N+1 en el fetching.
- Un índice añadido sin plan de query antes y después.
- Una clave de caché que omite una entrada de la que depende la respuesta (tenant, locale, visor).
- Una caché sin ventana de obsolescencia declarada ni estrategia de invalidación.
- Pool de conexiones subido en respuesta a agotamiento, sin hallar qué las retiene.
- Endpoints de lista sin paginación.
- Imágenes sin dimensiones, sin lazy loading, sin tamaños responsive.
- `React.memo` y `useMemo` por todas partes (abusar es tan malo como no usar).
- Optimizaciones conservadas sin remedición que las justifique.
- Varias optimizaciones agrupadas en una medición, sin atribución posible.
- Una "victoria" que exigió cambiar, saltar o borrar un test.
- El mismo experimento fallido reintentado porque nadie registró el primero.

---

## 🔗 Relaciones

**En el flujo de Addy:** eje 5 de [[code-review-and-quality]]. Sus presupuestos son la dimensión de performance de [[constraint-driven-development]], y sus métricas las alimenta [[observability-and-instrumentation]]. Trae datos reales de [[browser-testing-with-devtools]]. La auditoría estructurada de CWV la hace la persona [[web-performance-auditor]], que le **delega explícitamente** a esta skill la guía granular de optimización.

**Equivalencia Matt Pocock:** **no tiene equivalente.** Es otra pieza exclusiva de Addy, y de las que justifican su calificación de cobertura *amplia* del ciclo de vida.

```bash
npx skills add addyosmani/agent-skills --skill performance-optimization
```
