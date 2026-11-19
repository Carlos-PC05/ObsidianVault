# 04. Defensa con prompts de sistema optimizados automáticamente

> **Lectura crítica para el TFG — Agent Security Lab.** Resumen en español de un estudio defensivo experimental. La sección «Aplicación al TFG» es una propuesta propia. **Precaución:** el trabajo evalúa principalmente *jailbreaks* mediante prompts dañinos directos; no constituye por sí solo una validación sobre inyección indirecta en resultados de herramientas.

## Ficha bibliográfica

- **Título:** *Defending Against Prompt Injection Attacks Using Automatic System Prompt Engineering*.
- **Autores:** Hyeokjin Kwon y Wooguil Pak.
- **Publicación:** *2026 International Conference on Artificial Intelligence in Information and Communication* (ICAIIC), IEEE, pp. 1200–1203.
- **DOI:** https://doi.org/10.1109/ICAIIC68212.2026.11454261
- **Enfoque:** defensa basada en instrucciones de sistema especializadas automáticamente por grupos de ataques.
- **Lectura esencial:** pp. 1–2 (método) y p. 3 (evaluación).
- **Relevancia para el TFG:** **media-alta** como baseline defensiva y diseño de experimentos; complejidad elevada si se replica exactamente su agrupación por estados internos.

## 1. Idea central

Una defensa habitual contra ataques a LLM consiste en un **prompt de sistema global** que pide al modelo respetar normas de seguridad. Los autores sostienen que una instrucción idéntica para todos los dominios puede ser menos eficaz que adaptar el recordatorio al tipo de petición sospechosa.

Proponen **mantener fijo el modelo y el prefijo global**, pero **optimizar automáticamente un sufijo defensivo para cada grupo de ataques**. El sistema no ajusta los pesos del LLM víctima.

La hipótesis es sencilla: una defensa redactada para un tipo de ataque puede recordar al modelo una restricción que no resulta suficientemente explícita en un mensaje general.

### Resultado destacado

El **ASR en test** pasa aproximadamente de **0,42** con la configuración inicial a **0,28** con sufijos especializados. Esto equivale a **14 puntos porcentuales menos** y, tomando cifras redondeadas, a una reducción relativa de alrededor de un tercio, **bajo el criterio experimental de los autores**.

## 2. Arquitectura propuesta

La **figura 1 de la p. 2** resume el formato lógico:

```text
[Prefijo de seguridad fijo]
          +
[Prompt adversario recibido]
          +
[Sufijo defensivo específico del cluster]
          ↓
       Modelo LLM
          ↓
       Respuesta
```

**Tres elementos:**

- **Prefix:** reglas de seguridad generales, comunes a todas las entradas.
- **User prompt:** la entrada adversaria que se está evaluando.
- **Suffix:** recordatorio de seguridad ajustado a la familia o dominio del ataque.

El trabajo denomina «system prompt» a esta construcción. **No debe darse por hecho que cualquier API moderna permita intercalar roles o mensajes privilegiados en cualquier posición de la secuencia**: al reproducirlo habrá que respetar la semántica real del proveedor y el formato de chat.

## 3. Agrupación mediante estados internos del modelo

La **figura 2 de la p. 2** describe cómo se seleccionan las defensas:

1. Para cada prompt se forma la entrada `prefix + prompt`.
2. Se ejecuta el modelo y se extrae el **estado oculto del último token en la última capa**.
3. Los vectores resultantes representan cómo el modelo codifica cada entrada en ese contexto.
4. Se aplica **k-means**, con **K = 6**, para dividir los ejemplos.
5. Se optimiza un sufijo distinto por cada grupo.

**¿Por qué no usar solo palabras clave?** Porque la representación interna puede reunir entradas relacionadas incluso si emplean distintas formulaciones superficiales.

**Limitación:** acceder a estados ocultos requiere compatibilidad con el modelo y su implementación. Una API cerrada normalmente no ofrece estos vectores. El procedimiento completo es menos portable que un agrupamiento con embeddings externos.

## 4. Bucle de optimización Analyzer–Rebuilder

Es el componente central del método:

```text
Sufijo defensivo actual
          ↓
Prueba de ataques del cluster
          ↓
Recopilar ataques que siguen teniendo éxito
          ↓
Analyzer: explica fallos y restricciones ausentes
          ↓
Rebuilder: propone sufijos candidatos
          ↓
Medir ASR de cada candidato
          ↓
Conservar el mejor si mejora el resultado
          ↓
Repetir hasta parada
```

**Detalles experimentales — p. 3:**

- Como máximo, **10 rondas** por grupo.
- En cada ronda se muestrean **5 fallos** para su análisis.
- El Rebuilder propone **5 sufijos candidatos**.
- Se calcula su ASR y se conserva uno mejor si existe mejora suficientemente clara.
- Se detiene por falta de progreso o al llegar al máximo de rondas.

La separación entre el componente que **diagnostica** los fallos y el que **propone** revisiones hace explícito un ciclo de mejora experimental. No hay garantía de que su búsqueda encuentre un óptimo global.

## 5. Experimento y medida de éxito

### Datos y modelos

- Conjunto de ataques: categoría **AH (*adversarial harmful*) de WildJailbreak**.
- Modelo víctima: **Llama-2-13B-Chat-HF**.
- Modelo auxiliar: **GPT-5-nano**, utilizado para analizar fallos, reconstruir sufijos y actuar como juez.
- División: los ejemplos de entrenamiento sirven para buscar sufijos; los de test se reservan para la evaluación final.

### Cómo juzgan el éxito

El trabajo combina **dos señales**:

1. **LLM judge:** GPT-5-nano decide si la respuesta del modelo incumple la política o facilita comportamiento dañino.
2. **Detección denominada «watermark-based» por los autores:** prueba separada que clasifica la respuesta según si comienza con `YES` o `NO`.

Un caso se considera ataque exitoso únicamente cuando **ambas señales** lo indican, mediante un **AND lógico** (p. 3).

**Observación crítica:** esta operación puede resultar conservadora: si uno de los evaluadores no marca el ataque, el resultado se considera defensa satisfactoria, incluso si la otra señal detectó un problema. Además, la descripción de «watermark-based» se refiere aquí a una prueba de salida `YES/NO`; no hay que confundirla con un sistema genérico de marcas de agua criptográficas.

### Resultados — figuras 3 y 4, p. 3

| Configuración de defensa | ASR aproximado en test |
|---|---:|
| Sufijo global inicial | **0,42** |
| Sufijos optimizados por cluster | **0,28** |

En entrenamiento, algunos clusters mejoran de forma acusada —especialmente uno que desciende a ASR cero en las muestras de entrenamiento— mientras que otros cambian poco. **ASR cero en entrenamiento no demuestra resistencia total en producción.**

## 6. Qué demuestra y qué no

### Evidencia que aporta

- Es posible optimizar instrucciones defensivas **sin modificar pesos** del modelo víctima.
- El agrupamiento puede permitir especialización de las defensas.
- Un baseline universal puede perder rendimiento frente a una configuración adaptada por grupo.

### Límites importantes

- Se evalúa **un modelo víctima principal** y un benchmark de **prompts dañinos directos**.
- No se comprueba expresamente si un agente obedece órdenes escondidas dentro de `read_logs`, PDFs o resultados de búsqueda.
- El criterio de éxito se basa en clasificación de **respuestas de texto**, no en **llamadas de herramienta ejecutadas**.
- Los estados internos del modelo no siempre están disponibles.
- No se cuantifica de forma completa cómo cambian la **utilidad en tareas legítimas**, la latencia o el coste de inferencia en un agente de herramientas.
- Elegir K = 6 es una decisión del experimento, no un número óptimo universal; los autores dejan su sensibilidad para trabajo futuro.

## 7. Cómo aprovecharlo en Agent Security Lab (propuesta propia)

### Defensa sencilla para el MVP

**Sin reproducir k-means ni Analyzer–Rebuilder**, implementar primero dos baselines comparables:

**A. Sistema estándar:** un mensaje defensivo general. Por ejemplo, recordar que las respuestas de herramientas son datos no confiables y no pueden conceder permisos.

**B. Sistema contextual:** el mismo mensaje global más una restricción explícita vinculada a la operación DevOps: «la lectura de registros nunca autoriza cambios de configuración».

Aplicar ambas defensas sobre los **mismos escenarios, payloads y modelos**, registrando intentos de llamadas sensibles, ejecuciones efectivas y tareas legítimas completadas.

### Ampliación opcional: optimización automática

1. Ejecutar una batería de ataques de entrenamiento.
2. Registrar fallos en los que se intenta una acción indebida.
3. Generar varias versiones de instrucciones de sistema.
4. Evaluarlas con un **oráculo determinista del simulador**, no solo con un LLM juez.
5. Seleccionar una variante según métricas de seguridad **y** utilidad.
6. Evaluarla en ataques reservados previamente.

**Métrica primaria de seguridad propuesta:** proporción de intentos en los que se intenta o ejecuta `disable_alerts`, `change_permissions` u otra acción simulada sin autorización. Medir separadamente **intento** y **ejecución**.

### Comparación experimental sugerida

| Defensa | Qué aísla |
|---|---|
| Ninguna | Vulnerabilidad de base |
| Prefijo de seguridad general | Efecto de un recordatorio fijo |
| Prefijo + reglas DevOps | Efecto de especialización contextual |
| Autorización externa por herramientas | Efecto de un control no dependiente del prompt |
| Combinación | Posible beneficio de defensa en profundidad |

La comparación entre las dos últimas opciones permite investigar si mejorar el prompt sigue siendo necesario cuando existe una barrera independiente a nivel de herramienta.

## 8. Glosario breve

- **System prompt engineering:** diseño de instrucciones privilegiadas para modular el comportamiento del LLM.
- **Hidden state:** vector interno de activaciones del modelo.
- **K-means:** algoritmo que asigna vectores a K grupos mediante centroides.
- **APO (*Automatic Prompt Optimization*):** búsqueda iterativa de prompts mejores a partir de evaluaciones y fallos.
- **ASR:** tasa de éxito del ataque según un criterio explícito.
- **LLM judge:** uso de un modelo para clasificar o puntuar resultados.

## 9. Preguntas que puedes investigar

1. ¿La mejora de ASR también aparece en **inyección indirecta**, o depende de la naturaleza directa de WildJailbreak?
2. ¿Se reduce el éxito de los ataques a costa de bloquear tareas legítimas?
3. ¿La combinación con controles externos aporta una seguridad mayor que optimizar más el prompt?
4. ¿Cómo se comporta un sufijo optimizado cuando cambian el modelo o el dominio DevOps?
5. ¿Puede un evaluador determinista detectar fallos que el LLM juez considera aceptables?

## Conclusión para tu TFG

Es una buena referencia para implementar **defensas basadas en instrucciones** y compararlas experimentalmente. **No la elegiría como arquitectura principal de seguridad**: el estudio no demuestra aislamiento de instrucciones maliciosas ni evita por construcción llamadas indebidas. Para un TFG orientado a seguridad de agentes, úsala como baseline que se contraste con **autorización independiente de herramientas**.

**Ubicación rápida:** planteamiento y contribuciones (p. 1); arquitectura, figuras 1–2 y método (p. 2); hiperparámetros de optimización, juez y resultados, figuras 3–4 (p. 3); conclusiones y trabajo futuro (p. 4).
