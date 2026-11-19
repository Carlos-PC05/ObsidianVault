# 06. Prompt injection como nueva SQL injection: amenazas en agentes LLM

> **Lectura crítica para el TFG — Agent Security Lab.** Síntesis original en español de un artículo de revisión y modelado conceptual. La analogía con SQL injection es útil, **pero no literal**. Las propuestas para tu arquitectura se marcan como aplicaciones propias.

## Ficha bibliográfica

- **Título:** *Prompt Injection as the New SQL Injection: The Emergence of Attacks on AI Reasoning*.
- **Autores:** Shuktaev Abzal, Karabalin Bakhtiyar, Orynbasar Sultan, Olga Orlova, Elena Aleksandrovna Korochkina y Sholpan Borashova.
- **Publicación:** *2026 International Conference on Cybersecurity, Digital Forensics, and AI Applications* (ICCSDFAI), IEEE, pp. 1009–1014.
- **DOI:** https://doi.org/10.1109/ICCSDFAI70505.2026.11648022
- **Naturaleza:** revisión narrativa y propuesta de modelo conceptual de riesgo; **no** estudio de intervención con un benchmark propio y resultados cuantitativos de defensas.
- **Relevancia para Agent Security Lab:** **muy alta**. Aborda precisamente inyección indirecta, herramientas, autorización, trazabilidad y separación entre datos e instrucciones.
- **Lectura prioritaria:** pp. 2–4 (modelo de amenaza), pp. 4–5 (defensas y fórmulas), p. 5 (recomendaciones de ingeniería).

## 1. Tesis principal

La inyección SQL prosperó cuando una aplicación mezclaba **datos del usuario** con **código SQL** y el intérprete podía ejecutar el resultado. La **prompt injection** aprovecha un fallo análogo de confianza: el LLM recibe una combinación de **instrucciones de mayor autoridad** y **texto de origen menos fiable** y puede tratar este último como si pudiera dirigir su comportamiento.

La diferencia fundamental es que SQL opera sobre un parser con sintaxis formal; un LLM interpreta **lenguaje natural probabilísticamente**. Por eso, técnicas como escapar comillas, sustituir caracteres o usar una lista negra de frases no tienen el mismo efecto de seguridad que las **consultas SQL parametrizadas**.

**Idea central para memorizar:** no se debe delegar la seguridad únicamente a que el modelo «sepa distinguir» qué órdenes obedecer; hay que diseñar el flujo de herramientas y permisos para reducir el daño posible si el modelo se equivoca.

## 2. SQL injection frente a prompt injection

La **tabla I, pp. 3–4**, compara ambas amenazas. Resumen reinterpretado:

| Dimensión           | SQL injection (SQLi)                                    | Prompt injection (PI)                                                                 |
| ------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Origen del fallo    | Mezcla de datos y consulta ejecutable                   | Mezcla de contenido no confiable e instrucciones del agente                           |
| Intérprete          | Motor SQL con gramática formal                          | Modelo probabilístico que procesa lenguaje natural                                    |
| Objetivo            | Alterar una consulta o su efecto sobre la base de datos | Cambiar razonamiento, respuesta o uso de herramientas                                 |
| Defensa estructural | Parámetros preparados, tipado y permisos mínimos        | Separación del workflow, permisos de herramientas, procedencia y autorización externa |
| Consecuencia        | Fuga/modificación de datos, compromiso de BD            | Fuga, acciones indebidas, manipulación de decisiones o herramientas                   |

### Hasta dónde llega la analogía

**Se conserva:** el peligro de elevar **contenido controlado por el atacante** a un nivel de autoridad que no le corresponde.

**No se conserva:** la existencia de un equivalente exacto de `prepared statements` para instrucciones en lenguaje natural. En particular, marcar un fragmento como «texto externo» dentro del propio prompt no constituye automáticamente un aislamiento técnicamente exigible.

Esto no significa que delimitar contexto o usar prompts estructurados carezca de utilidad: **pueden reducir errores**, pero no sustituyen una política externa de autorización.

## 3. Taxonomía de amenazas

El paper organiza las inyecciones por su vía de entrada (sección III, p. 2):

### A. Directa

El atacante envía el prompt directamente al sistema que quiere manipular: intentar ignorar reglas, asumir un rol falso o cambiar el tipo de respuesta. El ataque entra por el canal interactivo.

### B. Indirecta

Un **tercero** modifica contenido que el agente debe consultar: páginas web, correo electrónico, PDF, eventos de calendario o documentos compartidos. El usuario legítimo pide una tarea normal, pero el agente procesa una instrucción adversaria incrustada en los datos.

**Este es el tipo central para tu TFG.**

### C. Multi-turn y ataques con estado

Un payload puede aparecer en una interacción y producir efectos más tarde, cuando se cumple un desencadenante o cuando una memoria conserva un fragmento adversario. Es importante distinguir si el origen malicioso sigue siendo trazable después de varios pasos del agente.

### D. Cadenas y múltiples agentes

La salida de un agente puede servir como entrada del siguiente. Si no se preservan las fronteras de confianza, un texto manipulado puede propagarse entre componentes aunque uno de ellos no lo ejecute directamente.

## 4. Por qué las herramientas aumentan el riesgo

La **sección V, pp. 3–4**, distingue un chatbot que únicamente genera texto de un **agente con herramientas capaces de ejecutar acciones**.

Ejemplo de la **figura 2, p. 3**: un agente que debe resumir correos abre un mensaje manipulado. El contenido adversario induce una consulta a contactos y una posterior petición web para sacar datos. Cada herramienta podría estar autorizada de forma aislada, pero la **secuencia de acciones no responde a la intención legítima del usuario**.

Este fenómeno se relaciona con el problema del **confused deputy** (*intermediario confundido*): un componente que posee permisos legítimos acaba usándolos en beneficio de quien controla los datos de entrada.

### El punto decisivo

Un modelo puede comportarse de manera incorrecta y, aun así, **no llegar a causar daño** si el componente que ejecuta acciones exige autorizaciones independientes.

Por ello hay que medir por separado:

1. Si el texto adversario alcanza al LLM.
2. Si el LLM decide o intenta usar una herramienta indebida.
3. Si el sistema **autoriza y ejecuta** la llamada.
4. Si la acción modifica realmente el estado o expone información.

El artículo da la base conceptual; la separación anterior es una **propuesta de instrumentación para Agent Security Lab**.

## 5. RAG también es una superficie de ataque

La **sección V.B, p. 4**, analiza la recuperación de documentos como vía de inyección. Un atacante podría modificar una página o documento que el RAG recuperará para una consulta previsible. El agente recibiría entonces contenido malicioso bajo apariencia de contexto relevante.

**Importancia:** que un documento sea **pertinente para responder** no significa que sea **confiable para dar órdenes**. La búsqueda vectorial y el *ranking* por similitud miden relación temática, no autoridad.

Esta distinción resulta especialmente relevante junto al paper **CESS**: CESS investiga cómo seleccionar y suministrar mejor la evidencia; el presente artículo advierte que la **procedencia y permisos** de esa evidencia deben tratarse aparte.

## 6. ¿Por qué fallan las defensas basadas exclusivamente en texto?

El artículo argumenta que:

- Las **listas negras** de palabras no cubren las innumerables paráfrasis y reencuadres de una instrucción.
- Los **delimitadores** en el prompt no constituyen una garantía de que el modelo nunca ejecute una orden incrustada.
- El entrenamiento de alineamiento o RLHF **orienta probabilidades de respuesta**, pero no crea por sí mismo una barrera de permisos comparable al sistema operativo.
- Los clasificadores de entrada o salida son defensas útiles, aunque también presentan falsos negativos.

**Matiz:** son tesis generales basadas en la literatura revisada. No prueban que todas las implementaciones de cada técnica fallen en igual medida, ni descartan mejoras futuras.

## 7. Modelo conceptual de éxito y riesgo

La **sección VII, pp. 4–5**, propone:

\[
	P_{success} = P_{bypass} \ times P_{execute} \ times(1-P_{detect})
\]

Según el artículo:

- **`P_bypass`:** probabilidad de que el payload consiga provocar un comportamiento del modelo contrario a la política.
- **`P_execute`:** probabilidad de que ese comportamiento llegue a ejecutarse en el entorno.
- **`P_detect`:** probabilidad de detección a tiempo para impedir las consecuencias.

Después define un riesgo agregado aproximado:

\[
	Risk = \ sum_i S_i \times P_{success,i}
\]

Donde `S_i` refleja la gravedad de cada categoría de acción.

### Interpretación práctica

- Un mejor prompt de sistema intenta reducir **`P_bypass`**.
- Un autorizador con permisos mínimos intenta reducir **`P_execute`**.
- Un detector y un monitor intentan mejorar **`P_detect`**.
- La evaluación de riesgo debe ponderar que **no todas las acciones** tienen consecuencias equivalentes.

### Precaución matemática

La multiplicación presentada es un **modelo ilustrativo**, no una identidad general demostrada por datos. Para ser una probabilidad exacta en todos los casos habría que especificar **probabilidades condicionales** y posibles dependencias entre conseguir el desvío, ejecutar la acción y detectarla. Por ejemplo, una defensa que bloquea una llamada también puede cambiar la probabilidad de observar/detectar el ataque.

**Para el TFG:** puedes utilizar la ecuación para estructurar el razonamiento, pero **no estimar el riesgo real solo multiplicando tasas de experimentos distintos** como si fuesen independientes.

## 8. Defensas arquitectónicas recomendadas

Las **secciones VI y VIII, pp. 4–5**, enfatizan:

### A. Separación entre planificación y ejecución

Un LLM puede **proponer** un plan, pero otro componente debe validar el tipo de operación y los privilegios antes de ejecutarlo. El componente que consume fuentes no confiables no debería recibir acceso irrestricto a acciones sensibles.

### B. Principio de mínimo privilegio

Conceder al agente solo las funciones necesarias para la tarea actual. Un agente que revisa logs no necesita por defecto poder borrar datos o modificar permisos.

### C. Procedencia y confianza de la información

Marcar de dónde procede cada fragmento (usuario, documentación, log, herramienta, etc.) y conservar esa identificación a través de los pasos del flujo. El artículo insiste en la trazabilidad, pero un simple rótulo textual **no impone** una política por sí mismo.

### D. Confirmación de operaciones sensibles

Las operaciones irreversibles o de alto impacto deben requerir una aprobación independiente del resultado del LLM, cuando el diseño lo necesite.

### E. Defensa en profundidad

Combinar controles de arquitectura, detectores, instrucciones defensivas, entrenamiento específico, auditoría y pruebas periódicas. Ninguna técnica se presenta como solución única.

## 9. Aplicación directa a tu arquitectura DevOps (propuesta propia)

```text
                        ┌─────────────────┐
Solicitud legítima ────> │  Agente LLM     │
                        └────────┬────────┘
                                 │ lee datos
                                 ▼
                        ┌─────────────────┐
                        │ read_logs /     │
                        │ read_docs       │
                        └────────┬────────┘
                                 │ resultado: BAJA CONFIANZA
                                 ▼
                        ┌─────────────────┐
                        │ Plan / llamada  │
                        │ a herramienta   │
                        └────────┬────────┘
                                 ▼
                        ┌─────────────────┐
                        │ POLICY ENGINE   │
                        │ independiente   │
                        └───────┬───┬─────┘
                            DENY│   │ALLOW
                                ▼   ▼
                             Bloqueo  Simulador DevOps
                                │        │
                                └───┬────┘
                                    ▼
                          Trazas + métricas
```

**El diagrama es una adaptación propuesta, no la figura exacta de los autores.**

### Reglas experimentales sencillas

| Operación simulada | Permiso orientativo | Motivo |
|---|---|---|
| `read_logs(service)` | Lectura permitida | Relevante para diagnóstico |
| `get_metrics(service)` | Lectura permitida | No cambia estado |
| `restart_service(service)` | Depende de la tarea y autorización | Puede interrumpir servicio |
| `disable_alerts(service)` | Denegar por defecto | Elimina protección y observabilidad |
| `delete_backups(service)` | Denegar por defecto | Alto impacto irreversible en el supuesto simulado |

La política debe venir de los **requisitos legítimos de la tarea** y del motor del laboratorio, no del contenido de un log.

### Métricas de laboratorio recomendables

- **ASR-intentado:** fracción de ataques que consiguen que el LLM proponga una acción prohibida.
- **ASR-ejecutado:** fracción de ataques que consiguen que el motor de herramientas lleve a cabo esa acción.
- **TSR:** fracción de tareas legítimas completadas.
- **FPR defensiva:** fracción de operaciones legítimas bloqueadas incorrectamente.
- **Impacto simulado:** gravedad del cambio de estado realizado.
- **Trazabilidad:** fracción de acciones que pueden relacionarse con su contenido fuente y decisión de autorización.

Esta separación te permitirá mostrar, con evidencia, si una defensa protege de verdad aunque el LLM haya sido inducido a intentar una operación insegura.

## 10. Valoración crítica del artículo

**Fortalezas:** proporciona un argumento arquitectónico claro, una analogía didáctica con SQLi, una taxonomía útil para agentes y una propuesta explícita de combinar modelo, permisos y detección.

**Limitaciones:** la revisión es **narrativa**, no una metaanálisis; sus recomendaciones no se comparan con un único benchmark reproducible; las fórmulas de probabilidad son **conceptuales**; y algunas conclusiones sobre la universalidad de las defensas requieren contraste en sistemas concretos. Conviene apoyarse también en estudios experimentales al discutir valores cuantitativos.

## 11. Preguntas de investigación muy adecuadas para el TFG

1. ¿Puede una política de permisos externa bloquear una llamada prohibida incluso cuando el LLM obedece la inyección?
2. ¿Cuánto empeora el TSR si las políticas restringen excesivamente las herramientas?
3. ¿Qué diferencias hay entre «LLM desviado», «herramienta intentada» y «herramienta ejecutada»?
4. ¿Los ataques incrustados en logs se comportan distinto de los insertados en documentación?
5. ¿Qué información mínima necesita una traza para reconstruir la cadena de confianza entre documento y acción?
6. ¿Cómo cambian los resultados al dar al agente más privilegios o añadir una confirmación explícita?

## Conclusión para tu TFG

**Es el paper más directamente conectado con el núcleo de CarpacAI.** Su enseñanza principal es que la seguridad de un agente con herramientas debe evaluarse como **propiedad del workflow**, no solo como calidad de las respuestas del LLM. El MVP puede demostrar esto comparando **prompts defensivos**, **detección** y **autorización independiente**, con un oráculo basado en el estado del simulador.

**Ubicación rápida:** analogía y objetivos (p. 1); revisión y taxonomía (p. 2); ataque indirecto y figura de correo, figura 2 (p. 3); permisos, RAG, modelos y fórmula de éxito (p. 4); ecuación de riesgo, clasificación por despliegue y recomendaciones (p. 5); conclusiones (p. 6).
