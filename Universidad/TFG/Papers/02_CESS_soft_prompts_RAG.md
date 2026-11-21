# 02. CESS: selección de evidencias y *soft prompts* en sistemas RAG

> **Lectura crítica para el TFG — CarpacAI.** Síntesis del artículo en español, con valoraciones propias marcadas como tales. **Aviso terminológico:** en este paper, *soft prompt injection* significa introducir representaciones continuas de conocimiento para mejorar las respuestas. **No significa un ataque de prompt injection.**

## Ficha bibliográfica

- **Título:** *Coupling evidence selection and soft prompt injection for retrieval-augmented generation via entropy-guided path compression*.
- **Autores:** Jiaqi Wu, Jiakang Li, Zefei Ning y Li Wang.
- **Publicación:** *Neurocomputing*, artículo aceptado en 2026; el PDF es una versión **pre-proof**, no la maquetación definitiva.
- **DOI:** https://doi.org/10.1016/j.neucom.2026.135552
- **Tema:** calidad de recuperación y uso de evidencias externas en **RAG** (*Retrieval-Augmented Generation*).
- **Localización:** pp. 3–10 del PDF (fundamento y método), pp. 10–17 (experimentación y resultados).
- **Relevancia directa para un laboratorio de inyección indirecta:** **baja**; **relevancia conceptual indirecta:** media, si incorporas un agente con recuperación documental o pruebas sobre contenidos RAG.

## 1. ¿Qué investiga realmente?

Los sistemas RAG recuperan documentos externos para responder preguntas. El problema estudiado no es que los documentos sean deliberadamente maliciosos, sino que la evidencia recuperada puede ser **redundante, poco útil, contradictoria o demasiado extensa**.

El RAG tradicional suele insertar los fragmentos de texto directamente en el prompt. Esa concatenación puede hacer que el resultado dependa excesivamente de:

- Orden de los documentos.
- Longitud y formato del contexto.
- Evidencia distractora o contradictoria.
- Capacidad del LLM para reconocer qué parte es decisiva.

Los autores proponen **CESS**, una arquitectura que une dos decisiones: **qué evidencia conservar** y **cómo suministrársela al modelo**.

## 2. Conceptos necesarios

| Concepto | Explicación sencilla |
|---|---|
| **RAG** | Responder con ayuda de información externa recuperada para una pregunta. |
| **Chunk** | Fragmento en que se divide un documento para procesarlo o recuperarlo. |
| **Grafo de conocimiento** | Estructura de entidades y relaciones; p. ej., `(API, depende_de, base_de_datos)`. |
| **Camino (*path*)** | Cadena de relaciones del grafo que puede justificar una respuesta. |
| **Entropía predictiva** | Medida de incertidumbre de la distribución de respuestas del modelo. |
| **Soft prompt** | Vectores continuos que condicionan el modelo; no son necesariamente palabras legibles. |
| **Hard concatenation** | Añadir el texto recuperado literalmente al contexto del LLM. |
| **Span EM** | *Exact Match* de una respuesta corta frente al texto de referencia. |

### Qué NO debe confundirse

- **Prompt injection maliciosa:** el atacante coloca instrucciones en datos de menor confianza para cambiar el comportamiento del agente.
- **Soft prompt injection de este artículo:** un módulo del propio sistema codifica evidencia seleccionada en vectores para orientar legítimamente la respuesta.

Comparten la palabra *injection*, pero **persiguen objetivos distintos y tienen diferentes modelos de amenaza**. CESS **no evalúa** resistencia a ataques de terceros.

## 3. Arquitectura de CESS

La **figura 2, p. 6**, ilustra tres fases:

```text
Pregunta del usuario
       ↓
Recuperación de documentos
       ↓
Extracción de triples y grafo de conocimiento
       ↓
Búsqueda de caminos relacionados con la pregunta
       ↓
Selección de caminos mediante cambios de entropía
       ↓
Compresión de los caminos en soft prompts
       ↓
LLM generador + pregunta → respuesta
```

### Fase A. Construcción del grafo

El sistema divide las fuentes en fragmentos y usa un LLM para extraer triples `(entidad_origen, relación, entidad_destino)`. A continuación, construye un grafo que facilita enlazar datos dispersos.

**Ejemplo conceptual:** de dos documentos podría extraer `(despliegue, usa, servicio_API)` y `(servicio_API, depende_de, PostgreSQL)`; el camino de dos saltos sirve para razonar sobre dependencias.

### Fase B. Selección de caminos por entropía

Primero se buscan caminos candidatos cercanos a la consulta. Luego se valora cuánto cambia la incertidumbre de respuesta del modelo al incorporar cada camino.

La idea es que una evidencia «interesante» no solo comparte palabras con la pregunta, sino que **modifica la distribución de predicciones del LLM**.

**Límite decisivo:** un cambio grande de entropía **no garantiza verdad ni inocuidad**. Un documento falso pero influyente también puede alterar fuertemente las predicciones. Los autores reconocen expresamente este problema (p. 12 y análisis de errores).

### Fase C. Compresión en *soft prompts*

Los caminos seleccionados se codifican como vectores de memoria de longitud controlada, en lugar de pasarlos como grandes bloques de texto. El módulo se entrena en dos etapas:

1. **Preentrenamiento de reconstrucción** de evidencia, para conservar información útil en la representación.
2. **Ajuste fino orientado a preguntas y respuestas**, para que la evidencia comprimida sea aprovechable por el generador.

La ventaja buscada es reducir sensibilidad a formatos y al orden de los fragmentos. El inconveniente es que exige modelos entrenables y recursos de GPU.

## 4. Diseño experimental

**Conjuntos de evaluación — tabla 2, p. 10:** Natural Questions (NQ), WebQuestions/WebQA, TriviaQA y TruthfulQA. Se emplean distintos tamaños de test, entre ellos 3.610 preguntas en NQ, 2.023 en WebQA, 11.313 en TriviaQA y 817 en TruthfulQA.

**Modelos principales:** Mistral-7B-Instruct, LLaMA-3-8B-Instruct y Qwen3-8B.

**Comparadores:** modelo sin RAG, RAG convencional y métodos CAD, AdaCAD, ICAE, PISCO y CARE, con cobertura variable según el benchmark.

**Métricas:** coincidencia exacta de respuesta corta (*Span EM*) en NQ, WebQA y TriviaQA; F1 y ROUGE-L en TruthfulQA. Estas métricas miden **calidad de respuestas**, no seguridad frente a ataques.

### Resultados principales — tabla 4, p. 13

La media que publica el estudio combina las métricas incluidas por conjunto; es un indicador sintético del artículo, no una medida universal.

| Modelo | RAG convencional, media | CARE, media | CESS, media |
|---|---:|---:|---:|
| Mistral-7B | 0,396 | 0,422 | **0,431** |
| LLaMA-3-8B | 0,396 | 0,423 | **0,431** |
| Qwen3-8B | 0,380 | 0,411 | **0,421** |

**Interpretación:** CESS obtiene una mejora numérica consistente respecto a RAG y CARE en estas condiciones. No es correcto afirmar que mejora de manera estadísticamente significativa en todos los conjuntos.

### Significación estadística — tabla 5, p. 13

En la comparación CESS frente a CARE sobre **Qwen3-8B**, los intervalos del 95 % incluyen cero para **NQ, WebQA y TruthfulQA**. La ventaja resulta significativa en **TriviaQA** (IC de la diferencia: `[0,012; 0,027]`).

### Selección de evidencias — tabla 6, p. 14

| Método | NQ | WebQA | TriviaQA | TruthfulQA (F1) |
|---|---:|---:|---:|---:|
| Sin recuperar | 0,290 | 0,366 | 0,577 | 0,273 |
| Selección por similitud | 0,450 | 0,439 | 0,684 | 0,281 |
| **Selección propuesta** | **0,462** | **0,445** | **0,704** | **0,285** |

La selección por influencia sobre la incertidumbre aporta mejoras adicionales sobre la similitud semántica en ese experimento.

### Ablación — tabla 7, p. 15

| Variante | NQ | WebQA | TriviaQA | TruthfulQA F1 |
|---|---:|---:|---:|---:|
| CESS completo | **0,462** | **0,445** | **0,704** | **0,285** |
| Sin *soft prompt* | 0,441 | 0,434 | 0,692 | 0,273 |
| Sin preentrenamiento del *soft prompt* | 0,374 | 0,348 | 0,589 | 0,213 |

La pérdida de rendimiento sin preentrenamiento ilustra que el coste de construir esta variante no es trivial.

## 5. Coste y limitaciones

### Coste medido — tabla 9, p. 16

El sistema requiere de media **29,63 segundos por pregunta** en NQ: **24,33 s** para construir el grafo (**82,1 %** del tiempo), **4,39 s** para la evaluación de entropía y **0,91 s** para generar la respuesta. En una fase se registra un pico de **21,35 GiB de memoria GPU**. La configuración indicada para los experimentos emplea una **NVIDIA RTX 5090 de 32 GB** (p. 12).

**Conclusión de ingeniería:** aunque los vectores finales son compactos, su preparación puede ser costosa. No se deduce que CESS sea una solución ligera para agentes de tiempo real.

### Dónde falla — pp. 16–17

Sobre 64 errores muestreados en NQ, los autores clasifican aproximadamente:

- **43,75 %:** selección de caminos incorrecta.
- **31,25 %:** fallo en generación pese a recibir evidencia útil.
- **25,00 %:** recuperación insuficiente.

También informan de triples correctos y completos en un **76 %** de una muestra de 100 preguntas NQ, frente al **65 %** en TruthfulQA (tabla 10, p. 17). Extraer hechos con un LLM es otra fuente de error.

### Reserva de seguridad

El artículo **no prueba que el grafo, la selección por entropía o los soft prompts filtren instrucciones adversarias**. De hecho, seleccionar lo «más influyente» podría conservar material manipulado si un atacante controla los documentos. Esta es una **hipótesis para investigar**, no un resultado demostrado por CESS.

## 6. Aplicación a CarpacAI (propuestas propias)

**No lo incorporaría al MVP como mecanismo de defensa.** Su complejidad y finalidad se apartan del objetivo central: medir las acciones no autorizadas de agentes con herramientas.

Sí permite formular un **escenario RAG opcional**:

1. El agente debe responder una consulta legítima sobre documentación operativa.
2. Se recuperan uno o varios documentos, uno potencialmente contaminado.
3. Se compara la recuperación normal con variantes de selección/filtrado.
4. Se observa si el agente trata el documento como **evidencia** o como **órdenes**.
5. Se comprueba si solicita o ejecuta una herramienta no autorizada.

### Ideas experimentales derivadas, no comprobadas por el paper

- Comparar recuperación de documentos íntegros con recuperación de fragmentos pequeños.
- Guardar la **procedencia** de cada chunk y cada respuesta de herramienta.
- Registrar si un fragmento malicioso fue recuperado, añadido al contexto, seguido por el modelo y traducido a acción.
- Evaluar un filtro de confianza independiente del modelo; **no equiparar relevancia informativa con autorización**.

## 7. Preguntas para una lectura crítica

1. ¿Cambiar la entropía equivale a aportar evidencia verdadera? **No.**
2. ¿El pipeline retrasa demasiado una interacción de agente?
3. ¿Qué pasa cuando la extracción de triples omite una relación esencial?
4. ¿Puede un atacante manipular la selección de caminos aportando un documento muy influyente?
5. ¿Merece la pena entrenar *soft prompts* para un laboratorio enfocado en simulación DevOps?

## Conclusión para el TFG

**CESS es un paper de mejora de RAG, no de defensa frente a prompt injection.** Conviene conservarlo en una categoría bibliográfica separada: **gestión de contexto y fuentes externas**. Su principal aprendizaje aplicable es que el contenido recuperado atraviesa varias etapas susceptibles de error; **la utilidad de la evidencia no establece su nivel de confianza ni la autoridad para ejecutar herramientas**.

**Ubicación rápida:** introducción y figura 1 (pp. 3–4); diagrama, figura 2 (p. 6); método (pp. 6–10); conjuntos y entrenamiento (pp. 10–12); rendimiento (tablas 4–7, pp. 13–15); costes y errores (tablas 9–10, pp. 16–17).
