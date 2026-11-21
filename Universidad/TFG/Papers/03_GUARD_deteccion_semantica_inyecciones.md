# 03. GUARD: detección semántica de inyecciones en documentos

> **Lectura crítica para el TFG — CarpacAI.** Síntesis en español basada en el PDF. Se distinguen resultados del artículo y aplicaciones propuestas para el laboratorio. El estudio se centra en **currículos de selección de personal**, pero su mecanismo es relevante para otros documentos no confiables.

## Ficha bibliográfica

- **Título:** *Detecting prompt injection attacks in LLM-driven hiring via semantic embedding*.
- **Autores:** Loubna Aminou, Abdelaziz Daaif, Maha Soulami y Mohamed Youssfi.
- **Revista:** *Applied Soft Computing*, volumen 205 **fechado en 2027**, artículo 116787; **publicado en línea el 2 de noviembre de 2026**.
- **DOI:** https://doi.org/10.1016/j.asoc.2026.116787
- **Sistema propuesto:** **GUARD** (*Generative Use-case Adversarial Resume Detector*).
- **Naturaleza:** defensa basada en detección, sin entrenamiento específico de un clasificador.
- **Relevancia para Agent Security Lab:** **alta para diseñar un detector opcional** y, especialmente, para aprender a **evaluar detectores con ataques no vistos**.
- **Lectura esencial:** pp. 4–5 (pipeline), 6–8 (métricas) y 8–9 (límites).

## 1. Resumen ejecutivo

GUARD intenta identificar instrucciones adversarias ocultas en un CV **antes** de que un sistema de contratación con LLM lo lea. No comprueba si el candidato cumple requisitos; compara segmentos del documento con una **biblioteca de ataques conocidos** usando *embeddings* semánticos y similitud coseno.

Su aportación más valiosa no es una detección perfecta —no la consigue—, sino un protocolo explícito para **evitar que paráfrasis casi idénticas aparezcan a la vez en la biblioteca conocida y en los ataques de evaluación**. Esto resulta muy útil para tu TFG, porque un detector puede parecer excelente si se prueba únicamente con variantes de ataques que ya conoce.

**Resultados que conviene recordar:** AUC de **0,794** sobre los ataques de referencia, pero **0,682** con ataques reservados (*held-out*); con el umbral principal de 0,70, el *recall* de ataques no vistos es **0,398**, y la tasa de falsos positivos es **15,7 %**.

## 2. Modelo de amenaza

- **Defensor:** aplicación que analiza automáticamente un CV y utiliza un LLM para decidir o recomendar.
- **Atacante:** candidato que puede modificar el contenido del CV, un **documento de menor confianza**.
- **Canal:** texto del CV extraído antes de enviarlo al modelo; otras investigaciones también estudian texto visualmente oculto en PDF.
- **Objetivo:** influir en la evaluación, por ejemplo, forzar una recomendación de contratación sin cambiar las cualificaciones reales.
- **Protección de GUARD:** detectar y localizar fragmentos sospechosos **antes** del análisis del documento.

Es un caso cercano a la **inyección indirecta**: el usuario legítimo del sistema no es necesariamente quien insertó la instrucción adversaria.

## 3. Cómo funciona GUARD

El flujo se ilustra en la **figura 1, p. 4**:

```text
Biblioteca histórica de ataques       CV por analizar
             ↓                              ↓
Normalización y deduplicación       Segmentación en chunks
             ↓                              ↓
Embeddings de ataques              Embeddings de chunks
             └────────────┬─────────────────┘
                          ↓
          Similitud coseno entre pares
                          ↓
          Máxima similitud del documento
                          ↓
              ¿Puntuación ≥ umbral?
                  ↓              ↓
                Alerta       Sin alerta
                  ↓
       Chunk sospechoso + ataque parecido
```

### Puntuación del documento

La puntuación consiste en tomar el **máximo** de las similitudes entre todos los fragmentos del CV y todos los ataques de referencia:

\[
	s(d) = \max_{c\in C(d),\ a\in A_{ref}}\cos(e(c),e(a))
\]

Donde `e(x)` es el embedding de un texto, `C(d)` son sus fragmentos y `A_ref` la biblioteca de patrones adversarios. Si `s(d) ≥ τ`, el documento se marca.

**Ventajas:** no requiere ajustar un clasificador con etiquetas; permite incorporar nuevos patrones a la biblioteca y señala el fragmento que provocó la alerta.

**Inconvenientes:** depende mucho de la cobertura de esa biblioteca, del modelo de embeddings y de cómo se segmentan los documentos.

### Embeddings y segmentación

El modelo principal es **`all-mpnet-base-v2`**. Se prueban cuatro segmentaciones (tabla 3, p. 5):

| Estrategia | Qué hace | Riesgo/ventaja |
|---|---|---|
| **Fine-grained** | Una oración por chunk | Buena localización, puede perder contexto |
| **Sentence-aware** | Hasta tres oraciones agrupadas | Conserva más contexto, puede diluir la inyección |
| **Paragraph** | Un chunk por párrafo | Localización gruesa; muchas alarmas posibles |
| **Fixed-window** | Ventanas solapadas de 40 palabras, paso 20 | Compromiso entre cobertura y fragmentación |

## 4. Protocolo experimental: la parte más aprovechable

1. Se unen **1.503** filas de dos colecciones de ataques históricos y quedan **1.081** ataques únicos tras normalización/deduplicación.
2. Se calcula similitud semántica entre ataques y se agrupan casi paráfrasis usando un grafo y componentes conectados, con umbral **0,85**.
3. Se reparten **componentes enteros** entre **757 ataques de referencia** y **324 ataques reservados**. Así, los equivalentes más próximos tienden a permanecer juntos.
4. Se parte de **2.481 CV limpios** únicos; los documentos se asignan sin reutilización y se inyecta un ataque en posiciones aleatorias para los ejemplos modificados.
5. Se selecciona el umbral de alarma en el subconjunto de referencia, optimizando F1, y **se congela** antes de evaluar los ejemplos reservados.
6. Se comparan otras segmentaciones y cuatro detectores existentes bajo un protocolo común.

**Lección metodológica:** dividir ataques aleatoriamente *por fila* puede permitir filtraciones de paráfrasis entre entrenamiento/referencia y prueba. En tu laboratorio, los **familias de payloads** deberían mantenerse separadas cuando evalúes generalización.

## 5. Resultados cuantitativos

### Generalización — tablas 4 y 5, p. 6

Para la configuración de una oración por chunk:

| Métrica | Ataques de referencia | Ataques reservados (*held-out*) |
|---|---:|---:|
| ROC AUC | **0,794** | **0,682** |
| F1 en el umbral elegido | **0,718** | **0,512** |
| Umbral `τ` | 0,70 | 0,70, **sin recalibrar** |
| Precisión | — | **0,717** |
| Recall | — | **0,398** |

AUC mide capacidad de **ordenar** documentos benignos y maliciosos a través de umbrales; **no equivale** a proporción de ataques detenidos.

### Matriz de confusión — tabla 8, p. 7

Sobre **648** documentos de prueba, 324 con ataque y 324 benignos:

| Real / detección | Marcado como ataque | Marcado como benigno |
|---|---:|---:|
| **Con ataque** | 129 verdaderos positivos | **195 falsos negativos** |
| **Benigno** | **51 falsos positivos** | 273 verdaderos negativos |

Por tanto, con `τ=0,70`, **se escapan 195 de 324 ataques** del conjunto reservado. Es un resultado útil, pero insuficiente para considerar el detector una barrera única de seguridad.

### Localización — tabla 7, p. 6

Entre los documentos atacados que sí se detectan, la coincidencia del chunk superior con el tramo realmente inyectado es **73,6 %**. Sin embargo, **detección correcta + localización correcta** solo se consigue en el **29,3 % de todos los documentos atacados**. No confundir ambos porcentajes.

### Comparación con otros detectores — tabla 9, p. 8

| Detector | AUC sobre ataques reservados |
|---|---:|
| **GUARD** | **0,682** |
| Meta Prompt Guard 86M | 0,654 |
| PIGuard | 0,633 |
| Sentinel-v2 | 0,577 |
| ProtectAI DeBERTa-v3 | 0,526 |

GUARD tiene la mejor AUC puntual **en esta evaluación concreta**, pero **no tiene el mejor F1 ni el mejor recall**. Algunos comparadores quedan prácticamente reducidos a marcar casi todo como positivo. El artículo no demuestra superioridad universal de GUARD sobre todos los detectores ni reproduce una comparación directa con RAPIDS.

### Validación externa

En **648 CV en inglés** procedentes del conjunto Djinni, utilizando el umbral congelado, los autores informan de **AUC 0,799** y **recall 0,438**, con tasa de falsos positivos aproximada del **4,9 %** (tablas 10 y discusión, pp. 8–9). Este experimento añade evidencia fuera de su corpus inicial, pero sigue usando **ataques insertados experimentalmente**.

## 6. ¿Por qué falla GUARD?

Los autores distinguen dos causas de falsos negativos en el conjunto reservado:

1. **Dilución por fragmentación/contexto:** un chunk benigno obtiene una puntuación mayor que el fragmento que contiene el ataque. Se describen **130 de los 195 falsos negativos** en este grupo.
2. **Biblioteca incompleta:** el chunk malicioso recibe la mejor puntuación del documento, pero no alcanza el umbral; **65 casos**.

También se producen falsos positivos porque ciertas formulaciones profesionales del CV se parecen semánticamente a las frases de ataque.

### Límites adicionales

- **Documento de texto, no inspección visual integral del PDF:** GUARD analiza texto ya extraído; no prueba por sí solo que detecte capas invisibles, imágenes con texto o manipulaciones del renderizado.
- **Distribución equilibrada artificial:** la mitad del conjunto de prueba contiene ataques. En un despliegue real con menor prevalencia, el valor predictivo de las alertas puede variar sustancialmente.
- **Multilingüismo no demostrado:** la prueba piloto de traducción al ucraniano no superó las comprobaciones de fidelidad; no se publican métricas de detección ucraniana.
- **Equidad en contratación:** el paper distingue explícitamente entre detectar ataques y garantizar justicia de la selección. El sistema se propone como alerta para **revisión humana**, no como mecanismo automático para rechazar candidatos.

## 7. Aplicación al TFG: detector de contenido externo (propuesta propia)

GUARD puede inspirar un **módulo defensivo opcional y asequible** sin entrenar un LLM de gran tamaño:

```text
Tool response: read_logs / read_documentation
                    ↓
         Splitter de mensajes
                    ↓
           Encoder de embeddings
                    ↓
     Comparación con patrones conocidos
                    ↓
  Riesgo detectado → alertar / escalar / bloquear
                    ↓
  Motor de políticas externo decide si una
       acción sensible puede ejecutarse
```

### Plan de evaluación recomendable

- **Baseline 1:** agente sin protección.
- **Baseline 2:** instrucciones de sistema que recuerdan que los logs son datos, no órdenes.
- **Variante GUARD simplificada:** similitud semántica contra una biblioteca de ataques.
- **Defensa adicional:** política de autorización independiente del LLM.
- **Ataques conocidos y desconocidos:** dividir **por familias semánticas**, no solo aleatoriamente.
- **Métricas distintas:** AUC/precisión/recall/FPR **para el detector** y ASR/TSR/**intentos y ejecuciones no autorizadas** para el agente.

**Muy importante:** detectar una frase sospechosa no implica impedir una acción si el agente ya tiene un camino alternativo para ejecutarla; y una alerta puede bloquear por error una tarea legítima. Debes medir ambos efectos.

## 8. Glosario mínimo

- **Embedding:** vector numérico que representa características semánticas del texto.
- **Cosine similarity:** similitud angular entre embeddings.
- **TP, FP, FN, TN:** verdaderos positivos, falsos positivos, falsos negativos y verdaderos negativos.
- **Precision:** de las alertas emitidas, cuántas eran ataques.
- **Recall/TPR:** de los ataques reales, cuántos se identifican.
- **FPR:** de los textos benignos, cuántos se etiquetan equivocadamente como ataques.
- **ROC AUC:** calidad de separación de clases a través de umbrales.
- **Held-out:** ejemplos excluidos del conjunto usado para configurar o conocer los patrones de ataque.

## 9. Preguntas de investigación para tu memoria

1. ¿Cómo cambia la detección de una inyección cuando se introduce en una línea de log frente a un documento largo?
2. ¿La generalización cae al reservar **familias de ataques** completas para el test?
3. ¿Qué umbral evita mejor los falsos positivos sin permitir demasiadas acciones indebidas?
4. ¿Qué porcentaje de ataques detectados habría conseguido realmente una llamada no autorizada?
5. ¿Puede el motor de autorización bloquear acciones maliciosas aunque GUARD no detecte el texto?

## Conclusión para tu TFG

**GUARD es muy aprovechable por su protocolo experimental y el uso de embeddings.** Enseña que un detector **entrenamiento-libre** puede aportar una señal útil y explicable, pero sus errores obligan a combinarlo con políticas de autorización. Para CarpacAI es especialmente valioso **copiar la disciplina de evaluación**, no asumir que sus métricas se trasladan desde CV a logs DevOps.

**Ubicación rápida:** figura 1 y construcción de datos (p. 4); fórmula y segmentación (p. 5); tablas 4–7 (p. 6); matriz de confusión, tabla 8 (p. 7); comparación y validación externa, tablas 9–10 (p. 8); discusión ética, límites y trabajo futuro (pp. 8–9).
