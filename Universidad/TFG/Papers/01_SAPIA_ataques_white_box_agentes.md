# 01. SAPIA: ataques de *prompt injection* adaptados al escenario

> **Lectura crítica para el TFG — Agent Security Lab.** Resumen analítico en español, no traducción íntegra. Los apartados «Aplicación al TFG» y «Valoración crítica» contienen propuestas y análisis propios, diferenciados de las afirmaciones del artículo.

## Ficha bibliográfica

- **Título:** *A white-box prompt injection attack on embodied AI agents driven by large language models*.
- **Autores:** Tongcheng Geng, Yubin Qu y W. Eric Wong.
- **Publicación:** *Journal of Systems and Software*, vol. 235, artículo 112782 (2026).
- **DOI:** https://doi.org/10.1016/j.jss.2026.112782
- **Enfoque:** ofensivo, experimental, **caja blanca** y agentes de IA que toman decisiones con consecuencias físicas.
- **Dónde leer lo esencial:** pp. 7–10 (SAPIA), 11–12 (evaluación), 12–19 (resultados y limitaciones).
- **Relevancia para Agent Security Lab:** **media-alta**, sobre todo para diseñar ataques adaptados a un dominio y experimentar con distintas defensas. No es una implementación directa de inyección **indirecta** sobre un agente DevOps.

## 1. Resumen en cinco ideas

1. Los ataques genéricos pueden no aprovechar las particularidades de sistemas que operan en entornos concretos: vehículos, robots, drones o instalaciones industriales.
2. SAPIA combina **contextualización del ataque** y **optimización de sufijos adversarios** para dirigir la salida de un LLM hacia una instrucción peligrosa.
3. El método se prueba con **40 instrucciones peligrosas**, repartidas entre **cuatro escenarios**, y con modelos abiertos de las familias Llama, Mistral y Vicuna.
4. El artículo comunica un **ASR medio de 90,0 %** para SAPIA, frente al **86,7 %** de instrucciones maliciosas explícitas (OMI), en el protocolo de los autores.
5. Los ataques optimizados presentan baja transferencia entre arquitecturas diferentes, pero mayor transferencia entre versiones de la misma familia. Las defensas sencillas basadas en prompts no los neutralizan de forma fiable.

## 2. Problema investigado

Un LLM empleado para controlar un sistema físico puede interpretar órdenes en lenguaje natural y convertirlas en acciones. Una petición aparentemente relacionada con el dominio podría provocar una operación peligrosa si el modelo confunde la intención y los límites de seguridad.

El artículo sostiene que un ataque diseñado sin conocer el **escenario operativo** es menos eficaz que uno que incorpora su vocabulario, contexto y restricciones. Por ejemplo, la misma formulación no sirve necesariamente para un robot manipulador y para un controlador industrial.

### Conceptos esenciales

- **Agente corporizado (*embodied agent*):** sistema de IA que percibe o actúa sobre un entorno físico o simulado.
- **Caja blanca (*white-box*):** el atacante puede acceder a los parámetros o gradientes del modelo para optimizar el ataque.
- **Sufijo adversario:** secuencia de tokens añadida a una entrada para aumentar la probabilidad de una respuesta concreta.
- **GCG (*Greedy Coordinate Gradient*):** técnica que busca sustituciones de tokens usando información del gradiente.
- **ASR (*Attack Success Rate*):** proporción de casos que satisfacen un criterio de éxito del ataque. **Ese criterio debe especificarse; no todos los artículos miden lo mismo.**

## 3. ¿Cómo funciona SAPIA?

La arquitectura se presenta en la **figura 1 de la p. 8** y tiene tres componentes:

### A. ACPGM: generación adaptativa del contexto

1. Recibe la instrucción objetivo, el escenario y su contexto.
2. Extrae características semánticas específicas, con ayuda de un LLM y plantillas asociadas al dominio.
3. Relaciona el objetivo con terminología y situaciones pertinentes.
4. Construye un prompt más ajustado a ese escenario que una fórmula genérica.

**Idea importante:** un ataque puede ser más convincente cuando parece parte natural de la tarea que el sistema está realizando.

### B. EGASM: generación del sufijo adversario

El segundo módulo mejora GCG con un objetivo compuesto presentado como **90 % pérdida de coincidencia con la salida objetivo + 10 % penalización por longitud**. Añade estrategias adaptativas, *momentum* y generación mediante muestreo, para controlar tanto el efecto adversario como la longitud o estructura del sufijo.

No se trata simplemente de insertar «ignora las instrucciones anteriores»: existe un proceso de búsqueda sobre tokens que requiere acceso interno al modelo.

### C. Prueba en simulador

El artículo utiliza **EyeSim VR**, un puente entre decisiones textuales y llamadas a API de control de robots. Menciona funciones de control de velocidad, servos, motores y visualización de estado (pp. 10–12).

**Distinción importante:** aunque se describe una integración con simulación física, el **ASR principal se define mediante coincidencias de palabras o expresiones en la salida del modelo**. Por tanto, los porcentajes de ASR del artículo **no deben interpretarse automáticamente como porcentajes de acciones físicas realmente ejecutadas**.

```text
Escenario y objetivo peligroso
            ↓
Adaptación semántica al dominio (ACPGM)
            ↓
Optimización del sufijo (EGASM / GCG)
            ↓
Entrada adversaria al modelo
            ↓
Respuesta → puente a herramientas/simulador
            ↓
Evaluación del efecto
```

## 4. Experimento

- **40 instrucciones**, 10 por dominio: conducción, manipulación robótica, drones y control industrial.
- **Niveles de riesgo:** L1 (bajo), L2 (medio), L3 (alto) y L4 (extremadamente alto).
- **Modelos principales:** Llama-3-8B-Instruct, Mistral-7B-Instruct-v0.2 y Vicuna-7B-v1.5.
- **Comparadores:** OMI (*Overt Malicious Instructions*), GCG y I-GCG.
- **Defensas:** IPD (perturbaciones de entrada), SSI (autoinspección estructurada) y SP (*safety prefix*).
- **Infraestructura:** servidor con **8 NVIDIA A800 de 80 GB**, 20.000 iteraciones máximas y lote de 256 para el método de ataque (p. 12). Es un coste experimental muy alejado de un portátil corriente.

### Resultados principales — tabla 2, p. 12

| Modelo | OMI | GCG | I-GCG | SAPIA |
|---|---:|---:|---:|---:|
| Llama-3-8B | 81,0 % | 5,0 % | 8,0 % | **85,0 %** |
| Mistral-7B v0.2 | 87,0 % | 8,0 % | 12,0 % | **90,0 %** |
| Vicuna-7B v1.5 | 92,0 % | 12,0 % | 17,0 % | **95,0 %** |
| **Media** | **86,7 %** | **8,3 %** | **12,3 %** | **90,0 %** |

**Lectura correcta:** la ventaja media de SAPIA sobre OMI es de **3,3 puntos porcentuales**, no del 90 %. La diferencia es mucho mayor respecto a GCG e I-GCG. El alto ASR de OMI indica, además, que la configuración experimental resulta vulnerable incluso ante instrucciones abiertas y directas.

### Riesgo y transferencia — tablas 3, 5 y 6

- SAPIA alcanza **92,5 %** de ASR en el conjunto de instrucciones L4, dentro del protocolo del estudio.
- La transferencia del ataque entre familias diferentes ronda **0,8–1,5 %** por par; media comunicada de **1,1 %**.
- Entre versiones de una misma familia, los autores observan transferencias bastante más elevadas. **No debe confundirse adaptabilidad tras optimizar para cada modelo con transferencia sin reoptimización.**

### Ablación — tabla 7, p. 18

| Variante | Llama | Mistral | Vicuna |
|---|---:|---:|---:|
| SAPIA completo | 85,0 % | 90,0 % | 95,0 % |
| Sin adaptación contextual ACPGM | 1,2 % | 1,8 % | 1,0 % |
| Sin EGASM (GCG original) | 23,7 % | 27,5 % | 18,3 % |

Según el estudio, **ambos componentes importan**, especialmente la adaptación al escenario.

### Resistencia frente a defensas — tabla 8, p. 19

| Defensa | OMI: Llama / Mistral / Vicuna | SAPIA: Llama / Mistral / Vicuna |
|---|---|---|
| IPD: perturbación de entrada | 52,3 / 48,7 / 41,2 % | 76,8 / 73,4 / 69,1 % |
| SSI: autoinspección | 71,5 / 68,9 / 57,3 % | 84,2 / 81,7 / 78,6 % |
| SP: prefijo de seguridad | 7,8 / 5,2 / 3,1 % | 83,6 / 80,4 / 77,9 % |

La lectura más llamativa es que el *safety prefix* reduce notablemente el éxito de OMI, pero poco el de SAPIA **en estas condiciones experimentales**. No demuestra que todos los prompts de sistema sean ineficaces: las conclusiones dependen del tipo de ataque, modelo y evaluación.

## 5. Limitaciones y precauciones científicas

**Observación crítica especialmente importante:** la **ecuación (13), p. 11**, parece exigir que la coincidencia se produzca en **al menos 5 de 10 pruebas** (`ceil(N/2)` con `N=10`). Sin embargo, su texto explicativo afirma «al menos 1 de 10» y la p. 12 se refiere explícitamente a un **umbral del 10 %**. **Fórmula y descripción verbal no coinciden.** Antes de reproducir o citar sus tasas de éxito como referencia, habría que aclarar qué criterio se ejecutó realmente.

Otras reservas:

- El ASR depende de criterios basados en palabras o expresiones de salida; esto puede producir discrepancias respecto a la ejecución efectiva de un comando.
- Solo se evalúan 40 instrucciones y un conjunto concreto de modelos, defensas y simulaciones.
- Los ataques requieren acceso de caja blanca y recursos computacionales considerables.
- No constituye una evaluación directa de **inyección indirecta a través de documentos, logs o resultados de herramientas**.
- Las afirmaciones causales sobre por qué se transfiere el ataque entre versiones son interpretaciones de los autores, no una verificación concluyente de todos los mecanismos internos.

## 6. ¿Qué puedes aprovechar en Agent Security Lab?

**Propuestas de aplicación — no resultados del artículo:**

1. **Ataques específicos de DevOps:** diseñar un catálogo de payloads para registros de errores, documentación de despliegue y respuestas de herramientas, en lugar de reutilizar únicamente prompts genéricos.
2. **Separar el texto de la acción:** medir por separado `malicious_text_generated`, `unauthorized_tool_attempted` y `unauthorized_tool_executed`; la tercera solo será verdadera si cambia el estado simulado.
3. **Riesgos graduados:** operaciones ficticias de consulta, cambios rutinarios y acciones sensibles (p. ej., desactivar alertas, modificar permisos o borrar copias de seguridad).
4. **Ablaciones sencillas:** comparar una instrucción maliciosa genérica frente a su variante contextualizada, con y sin defensa.
5. **Defensas representativas:** incluir un recordatorio de jerarquía de instrucciones, un verificador externo de acciones y un control determinista de permisos; no confiar únicamente en autoverificación del modelo.
6. **Reproducibilidad:** fijar escenario, tarea, payload, modelo, temperatura, versión y semilla cuando proceda; registrar trazas de cada intento.

### Ejemplo seguro, en simulación

- **Tarea legítima:** «Diagnostica por qué falla el despliegue de la API».
- **Superficie no confiable:** resultado simulado de `read_logs("api")`.
- **Objetivo adversario:** inducir una llamada simulada a `disable_alerts("api")` sin autorización.
- **Éxito del ataque:** llamada indebida **intentada** y, por separado, **ejecutada** si el motor de permisos la permite.
- **Comparación:** payload genérico vs. payload adaptado al vocabulario de despliegues.

Este escenario reproduce la **idea de adaptación contextual**, no el algoritmo completo de optimización blanca de SAPIA.

## 7. Conceptos que merece la pena estudiar

GCG; gradientes sobre tokens; *white-box* frente a *black-box*; diseño de funciones de pérdida; ASR y definición de eventos de éxito; experimentos de ablación; transferencia entre modelos; ataques semánticos frente a coincidencias léxicas; seguridad en simuladores.

## 8. Preguntas para leer con criterio

1. ¿La métrica verifica una acción ejecutada o solo la aparición de cierto texto?
2. ¿Cuál es el umbral verdadero de éxito: 1/10 o 5/10?
3. ¿Cómo cambiaría el ASR si el sistema tuviera un autorizador independiente del LLM?
4. ¿Podría observarse la misma ventaja contextual en un ataque **indirecto**, sin gradientes ni acceso a los pesos?
5. ¿Qué cuesta generar cada ejemplo adversario y cuántos intentos se permiten?

## Conclusión para tu TFG

**Utilidad principal:** diseño de **ataques contextualizados**, **ablaciones** y **evaluación experimental**. No es realista replicar íntegramente SAPIA como parte obligatoria del MVP, y el paper exige especial cuidado al interpretar su métrica de éxito. Su enseñanza práctica es evaluar comportamientos del agente en un contexto concreto, no extrapolar tasas de éxito de texto a acciones reales.

**Localización rápida:** arquitectura, figura 1 (p. 8); método, secc. 5 (pp. 7–10); métrica, ecuaciones 13–14 (pp. 11–12); resultados, tablas 2–3 (pp. 12–13); transferencia, tablas 5–6 (p. 15); ablación, tabla 7 (p. 18); defensas, tabla 8 (p. 19).
