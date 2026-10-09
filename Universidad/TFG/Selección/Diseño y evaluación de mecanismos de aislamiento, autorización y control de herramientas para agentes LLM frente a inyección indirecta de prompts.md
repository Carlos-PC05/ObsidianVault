
## 1. Resumen de la propuesta

Los sistemas basados en grandes modelos de lenguaje están evolucionando desde simples asistentes conversacionales hacia **agentes capaces de interactuar con sistemas externos**: consultar documentos, navegar por Internet, acceder a bases de datos, utilizar APIs, modificar archivos, ejecutar código o enviar información mediante servicios externos.

Esta capacidad convierte al modelo en un componente activo dentro de un sistema software y crea una superficie de ataque nueva.

Uno de los problemas más importantes es la **inyección indirecta de prompts** (_Indirect Prompt Injection_, IPI). A diferencia de un ataque directo, el atacante no necesita comunicarse directamente con el agente. Puede introducir instrucciones maliciosas dentro de información que posteriormente será procesada por él: una página web, un documento, un correo electrónico, una descripción de una incidencia, la respuesta de una API o información recuperada mediante RAG.

OWASP identifica precisamente este escenario como uno de los principales riesgos de los sistemas LLM y advierte de que una inyección indirecta puede acabar provocando acceso no autorizado a información o utilización indebida de herramientas conectadas al modelo.

El objetivo de este TFG será **diseñar, implementar y evaluar experimentalmente una arquitectura de seguridad para agentes LLM con acceso a herramientas**, estudiando hasta qué punto distintos mecanismos de aislamiento, autorización y validación pueden reducir el impacto de ataques de inyección indirecta sin impedir que el agente continúe realizando correctamente sus tareas.

El resultado no será solamente un análisis teórico. Se desarrollará un **prototipo funcional de una capa de seguridad para agentes**, acompañado por un entorno de experimentación reproducible y un conjunto de métricas que permitan comparar objetivamente distintas estrategias defensivas.

---

# 2. El problema

Supongamos que un usuario solicita:

> «Revisa mis correos de esta mañana, identifica los relacionados con el proyecto y prepara un resumen.»

El agente dispone de:

- acceso de lectura al correo;
    
- una herramienta para consultar archivos;
    
- una herramienta para enviar mensajes;
    
- acceso a determinados documentos internos.
    

Uno de los correos contiene texto controlado por un atacante que intenta modificar el comportamiento del agente.

El problema fundamental es que, para el LLM, **las instrucciones legítimas y los datos externos terminan formando parte de un mismo contexto lingüístico**.

El modelo debe interpretar simultáneamente:

```
INSTRUCCIÓN DEL USUARIO
        ↓
"Resume los correos importantes"

DATOS NO CONFIABLES
        ↓
contenido de emails
documentos
páginas web
respuestas de herramientas

INSTRUCCIÓN MALICIOSA
        ↓
"Realiza otra acción"
```

Si el modelo interpreta accidentalmente la información externa como una nueva instrucción, puede desviarse del objetivo original.

En un chatbot normal esto puede provocar una respuesta incorrecta.

En un agente con herramientas puede provocar:

```
Prompt Injection
       ↓
Control parcial del agente
       ↓
Tool Call malicioso
       ↓
──────────────────────────
│ acceso a información    │
│ modificación de datos   │
│ envío de información    │
│ ejecución de acciones   │
──────────────────────────
```

Por ello, el problema deja de ser únicamente de NLP y pasa a ser un problema clásico de **seguridad de sistemas: autorización, aislamiento, control de privilegios y validación de operaciones**.

OWASP recomienda precisamente aplicar mínimo privilegio, separación del contenido externo, autorización de acciones y supervisión de operaciones sensibles en los sistemas agentic.

---

# 3. Pregunta principal de investigación

La pregunta que vertebraría el TFG sería:

> **¿Qué combinación de mecanismos de aislamiento de contexto, control de privilegios y validación de acciones permite reducir eficazmente el impacto de ataques de inyección indirecta de prompts en agentes LLM sin degradar significativamente su capacidad para completar tareas legítimas?**

De ella derivan varias preguntas secundarias:

**RQ1.** ¿Hasta qué punto son vulnerables diferentes configuraciones de agentes LLM ante inyección indirecta?

**RQ2.** ¿Qué mecanismos defensivos proporcionan una mejor relación entre seguridad y utilidad?

**RQ3.** ¿Es más efectivo intentar detectar la inyección o limitar las consecuencias de un agente potencialmente comprometido?

**RQ4.** ¿Qué impacto introducen las defensas sobre latencia, coste y número de llamadas al modelo?

**RQ5.** ¿Siguen siendo eficaces las defensas frente a ataques adaptados específicamente para intentar evadirlas?

La última pregunta es especialmente importante. Investigaciones recientes han demostrado que defensas aparentemente robustas pueden degradarse significativamente cuando el atacante adapta el ataque específicamente contra ellas.

---

# 4. Hipótesis

El proyecto podría trabajar sobre tres hipótesis principales.

### H1 — Mínimo privilegio

Un agente al que se le proporcionan únicamente las herramientas estrictamente necesarias para una tarea debería presentar una menor tasa de éxito de ataques que un agente con acceso permanente a todas sus herramientas.

### H2 — Validación externa

Validar las acciones sensibles fuera del propio LLM debería ser más robusto que confiar exclusivamente en instrucciones escritas dentro del _system prompt_.

### H3 — Defensa multicapa

Una combinación de:

```
aislamiento de contexto
        +
mínimo privilegio
        +
validación de acciones
```

debería proporcionar una relación seguridad/utilidad superior a cualquiera de estos mecanismos de forma aislada.

---

# 5. Amenaza estudiada

El TFG se centrará específicamente en:

## Indirect Prompt Injection

El atacante controla algún recurso que posteriormente consume el agente.

Por ejemplo:

```
Usuario
   │
   │ "Analiza este repositorio"
   ▼
Agente
   │
   ├── README.md
   ├── issues
   ├── documentación
   └── archivos
          │
          └── contenido manipulado
                    │
                    ▼
             Prompt Injection
```

Esto resulta especialmente relevante en agentes de programación. OWASP señala en su guía de seguridad para desarrollo asistido por IA que descripciones de issues, pull requests, documentación o comentarios de repositorios pueden actuar como vectores de inyección indirecta cuando son procesados por agentes con capacidad para modificar archivos o ejecutar comandos.

El TFG **no estaría centrado principalmente en jailbreaks clásicos** realizados directamente por el usuario.

El adversario considerado sería alguien capaz de modificar uno de los recursos externos consumidos por el agente, pero sin acceso directo a sus credenciales, configuración interna o infraestructura.

---

# 6. Propuesta técnica

El componente principal del proyecto sería una capa de seguridad situada entre el razonamiento del agente y las herramientas reales.

Conceptualmente:

```
                  ┌─────────────────┐
                  │     Usuario     │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │    Agente LLM   │
                  └────────┬────────┘
                           │
                    propone acción
                           │
                           ▼
             ┌──────────────────────────┐
             │     SECURITY LAYER       │
             │                          │
             │  Context isolation       │
             │  Tool authorization      │
             │  Policy engine           │
             │  Action validation       │
             │  Risk classification     │
             │  Audit log               │
             └─────────────┬────────────┘
                           │
                    acción permitida
                           │
                           ▼
              ┌─────────────────────────┐
              │       Herramientas      │
              │                         │
              │  archivos               │
              │  email                  │
              │  calendario             │
              │  APIs                   │
              │  búsqueda               │
              └─────────────────────────┘
```

La idea fundamental será:

> **No asumir que el LLM es una frontera de seguridad.**

Incluso aunque el modelo haya sido manipulado, la arquitectura debe intentar impedir que pueda ejecutar acciones incompatibles con la intención original del usuario.

Esta aproximación coincide con una dirección importante de la investigación reciente: trasladar parte de la seguridad desde el propio modelo hacia mecanismos de control a nivel de sistema.

---

# 7. Defensas que se estudiarían

No intentaría implementar diez mecanismos diferentes. Para un TFG resulta más interesante estudiar profundamente tres o cuatro.

## Defensa 0 — Baseline

Agente normal.

```
LLM → tool call → ejecución
```

No existe validación adicional.

Servirá como referencia.

---

## Defensa 1 — Separación de datos e instrucciones

Todo contenido procedente de fuentes externas se identificará explícitamente como **untrusted data**.

El objetivo es evitar que:

```
documento externo
```

pueda adquirir el mismo nivel semántico que:

```
system prompt
user instruction
```

Se estudiarían mecanismos como:

- encapsulado estructurado;
    
- delimitación estricta;
    
- normalización de entradas;
    
- eliminación de determinados contenidos activos;
    
- separación de canales.
    

Esta defensa probablemente no será suficiente por sí misma, pero constituye un baseline interesante.

---

# 8. Defensa 2 — Least Privilege dinámico

Esta sería una de las partes más interesantes del TFG.

Actualmente muchos agentes reciben todas sus herramientas disponibles desde el comienzo:

```
tools = [
    read_email,
    send_email,
    delete_email,
    read_files,
    write_files,
    web_request
]
```

aunque para una determinada tarea solamente necesiten:

```
read_email
```

El proyecto estudiaría un mecanismo que determine previamente las capacidades necesarias.

Por ejemplo:

```
Usuario:
"Resume mis correos de hoy."
```

Política resultante:

```
allowed_tools:
  - email.read

denied_tools:
  - email.send
  - email.delete
  - filesystem.write
  - network.request
```

Aunque una inyección consiga manipular al LLM para que intente enviar información:

```
LLM
 ↓
send_email(...)
 ↓
POLICY ENGINE
 ↓
DENIED
```

el ataque fracasa.

Esto sigue directamente el principio de mínimo privilegio recomendado para agentes LLM.

---

# 9. Defensa 3 — Action Validator

Antes de ejecutar una operación sensible se comprobaría que la acción propuesta sigue siendo coherente con la intención original del usuario.

Ejemplo:

```
Objetivo original:

"Busca mis próximas reuniones."
```

El agente propone:

```
delete_calendar_event(...)
```

El validador recibiría:

```
USER INTENT
+
PROPOSED ACTION
+
POLICY
```

pero no necesariamente todo el contenido externo potencialmente comprometido.

Resultado:

```
DENY
reason:
"Deleting an event is not required
to retrieve upcoming meetings."
```

La validación podría combinar:

- reglas deterministas;
    
- permisos;
    
- clasificación de riesgo;
    
- validación mediante un segundo modelo;
    
- políticas explícitas.
    

La propia guía de OWASP contempla el filtrado de acciones propuestas como una de las capas posibles, pero recalca que este tipo de guardrails no debe sustituir controles deterministas de permisos y validación.

---

# 10. Defensa 4 — Confirmación humana según riesgo

No todas las acciones necesitan confirmación.

Por ejemplo:

```
READ EMAIL             → automático
SEARCH FILES           → automático
READ CALENDAR          → automático

SEND EMAIL             → confirmación
DELETE FILE            → confirmación
TRANSFER MONEY         → confirmación
EXECUTE COMMAND        → confirmación
```

Se podría crear una clasificación:

|Riesgo|Ejemplos|Política|
|---|---|---|
|Bajo|búsqueda, lectura|automática|
|Medio|escritura reversible|validación|
|Alto|envío, borrado, ejecución|aprobación humana|

Esto permite estudiar también el compromiso entre:

**autonomía ↔ seguridad**.

---

# 11. Producto desarrollado

El proyecto tendría dos componentes claramente diferenciados.

## A. Benchmark experimental

Permitirá ejecutar sistemáticamente agentes, defensas y ataques.

Por ejemplo:

```
agentsec benchmark \
    --model MODEL \
    --attack indirect \
    --defense least-privilege \
    --suite workspace
```

Y producir resultados como:

```
Task Success Rate      87.4 %
Attack Success Rate     6.8 %
Utility Under Attack   79.2 %
False Positive Rate     3.1 %
Mean Latency            2.7 s
```

---

## B. Security Gateway para agentes

El segundo componente sería un pequeño prototipo reutilizable:

```
LLM Agent
   │
   ▼
Agent Security Gateway
   │
   ├── policy engine
   ├── permission manager
   ├── action validator
   ├── risk classifier
   └── audit logger
   │
   ▼
Tools / MCP Servers
```

Esto proporciona al TFG un resultado tangible.

No sería simplemente:

> «He analizado AgentDojo.»

Sino:

> «He diseñado una arquitectura defensiva, la he implementado y he evaluado experimentalmente sus propiedades de seguridad.»

---

# 12. Posible integración con MCP

Como ampliación, el prototipo podría utilizar **Model Context Protocol (MCP)** para representar las herramientas.

La arquitectura sería:

```
Agent
  │
  ▼
MCP Client
  │
  ▼
Security Gateway
  │
  ├── autorización
  ├── filtrado
  ├── políticas
  └── auditoría
  │
  ▼
MCP Servers
```

Esto haría especialmente interesante el proyecto porque permitiría tratar la capa desarrollada como una especie de:

> **firewall o proxy de seguridad para herramientas de agentes.**

MCP ha seguido evolucionando durante 2026 y su especificación de julio de 2026 incorporó, entre otras mejoras, endurecimiento de mecanismos relacionados con autorización, reflejando la importancia creciente de este problema en arquitecturas de agentes conectados a servicios externos.

No obstante, MCP debería ser una **aplicación del trabajo**, no el objetivo principal del TFG.

---

# 13. Entorno experimental: AgentDojo

Para evitar construir desde cero todos los escenarios de ataque se utilizaría **AgentDojo**.

AgentDojo fue presentado en NeurIPS 2024 como un entorno específicamente diseñado para evaluar agentes que utilizan herramientas sobre datos no confiables. Incluye:

- 97 tareas realistas;
    
- 629 casos de prueba de seguridad;
    
- escenarios relacionados con productividad, banca y viajes;
    
- ataques y defensas;
    
- arquitectura extensible para incorporar nuevas estrategias.
    

Además, permite ejecutar combinaciones de:

```
MODEL
 ×
TASK
 ×
ATTACK
 ×
DEFENSE
```

por lo que encaja directamente con la metodología de este TFG.

AgentDojo sería el entorno de referencia, pero **las defensas desarrolladas constituirían la aportación propia**.

---

# 14. Diseño experimental

Se podrían comparar cinco configuraciones:

|Configuración|Separación|Permisos|Validator|Humano|
|---|---|---|---|---|
|Baseline|❌|❌|❌|❌|
|D1|✅|❌|❌|❌|
|D2|❌|✅|❌|❌|
|D3|❌|✅|✅|❌|
|D4|✅|✅|✅|✅|

El interés está en comparar el incremento de seguridad con el coste introducido.

---

# 15. Métricas

## Attack Success Rate

[  
ASR =  
\frac{\text{ataques exitosos}}  
{\text{ataques ejecutados}}  
]

La principal métrica de seguridad.

---

## Task Success Rate

[  
TSR =  
\frac{\text{tareas legítimas completadas}}  
{\text{tareas ejecutadas}}  
]

Permite comprobar que una defensa no inutiliza el agente.

---

## Utility Under Attack

Evalúa si el agente sigue pudiendo completar correctamente la petición original incluso cuando existe contenido malicioso.

Esta métrica es especialmente importante.

Una defensa que simplemente bloquee toda operación podría alcanzar:

```
ASR = 0 %
```

pero también:

```
Utility = 0 %
```

y por tanto no sería una solución útil.

---

## False Positive Rate

Acciones legítimas bloqueadas incorrectamente.

---

## Latencia

Tiempo adicional provocado por la defensa.

---

## Coste

Número de:

- tokens;
    
- llamadas al LLM;
    
- llamadas adicionales al validator.
    

---

# 16. Ataques adaptativos

Una segunda fase especialmente interesante consistiría en comprobar qué ocurre cuando el atacante **conoce la defensa**.

Esto distinguiría:

```
Static Attack
```

de:

```
Adaptive Attack
```

La importancia de esta prueba está respaldada por resultados recientes: trabajos de 2025 consiguieron superar múltiples defensas de prompt injection cuando los ataques se optimizaban específicamente contra ellas.

En 2026 también se han estudiado ataques automatizados dentro de AgentDojo, encontrando que algunas estrategias black-box pueden generar inyecciones efectivas y transferibles entre determinadas tareas y dominios.

Por tanto, una conclusión del TFG podría distinguir entre:

```
seguridad frente a ataques conocidos
```

y:

```
robustez frente a adversarios adaptativos
```

---

# 17. Relación con investigación de 2026

El problema continúa plenamente abierto.

USENIX Security 2026 ha publicado trabajos directamente dedicados a defensas contra inyección indirecta en agentes. Por ejemplo, **AttriGuard** estudia la relación causal entre información no confiable y llamadas a herramientas, mostrando que las defensas pueden analizarse a nivel de la trayectoria de ejecución del agente y no solamente mediante filtrado del texto de entrada.

También se están investigando arquitecturas donde la seguridad se construye mediante controles de sistema alrededor del modelo, en lugar de confiar exclusivamente en la capacidad del LLM para reconocer instrucciones maliciosas.

Esto proporciona una oportunidad clara para el TFG:

> **evaluar experimentalmente hasta qué punto controles clásicos de seguridad software —mínimo privilegio, autorización, separación de confianza y validación de operaciones— pueden trasladarse eficazmente a arquitecturas agentic.**

---

# 18. Stack tecnológico

El proyecto podría mantenerse relativamente sencillo.

|Componente|Tecnología|
|---|---|
|Lenguaje|Python|
|Benchmark|AgentDojo|
|Agente|AgentDojo / implementación ligera propia|
|LLM|1–2 modelos|
|Tool protocol|MCP opcional|
|API|FastAPI|
|Políticas|Python / YAML|
|Tests|pytest|
|Contenedores|Docker|
|Resultados|CSV / Parquet|
|Análisis|pandas|
|Estadística|scipy|
|Visualización|matplotlib|
|Automatización|GitHub Actions|

No entrenaría ningún modelo.

Tampoco desarrollaría un framework de agentes completo.

---

# 19. Posible arquitectura del repositorio

```
agent-security-tfg/

├── agent/
│   ├── runtime.py
│   └── tools.py
│
├── security/
│   ├── gateway.py
│   ├── permissions.py
│   ├── policies.py
│   ├── validator.py
│   └── risk.py
│
├── defenses/
│   ├── context_isolation.py
│   ├── least_privilege.py
│   └── action_validation.py
│
├── attacks/
│
├── benchmark/
│   ├── runner.py
│   ├── metrics.py
│   └── experiments.py
│
├── mcp/
│   ├── client.py
│   └── servers/
│
├── experiments/
│   ├── configs/
│   └── results/
│
├── notebooks/
│
├── tests/
│
├── docker/
│
└── README.md
```

---

# 20. Qué no incluiría

Para impedir que el TFG crezca demasiado, se excluirían deliberadamente:

- entrenamiento de modelos;
    
- fine-tuning;
    
- creación de un LLM propio;
    
- construcción de un RAG complejo;
    
- desarrollo de un nuevo framework agentic;
    
- análisis de decenas de modelos;
    
- ataques contra servicios reales;
    
- herramientas con consecuencias reales;
    
- investigación exhaustiva sobre jailbreaks.
    

Todos los experimentos deberían utilizar:

**datos ficticios + herramientas simuladas + entornos aislados.**

---

# 21. Fases del proyecto

### Fase 1 — Estado del arte

Estudio de:

- agentes LLM;
    
- tool calling;
    
- prompt injection;
    
- indirect prompt injection;
    
- AgentDojo;
    
- aislamiento;
    
- least privilege;
    
- autorización;
    
- guardrails;
    
- MCP.
    

### Fase 2 — Reproducción del baseline

Ejecutar AgentDojo sin defensas y obtener una primera referencia experimental.

### Fase 3 — Arquitectura

Diseñar el Security Gateway y el modelo de permisos.

### Fase 4 — Implementación

Desarrollar las tres defensas principales.

### Fase 5 — Experimentación

Ejecutar:

```
tareas normales
ataques
defensas
combinaciones
repeticiones
```

### Fase 6 — Ataques adaptativos

Evaluar si las conclusiones se mantienen frente a adversarios que conocen las defensas.

### Fase 7 — Prototipo MCP

Demostrar la arquitectura desarrollada sobre un pequeño conjunto de herramientas.

### Fase 8 — Análisis

Calcular:

- ASR;
    
- utilidad;
    
- Utility Under Attack;
    
- falsos positivos;
    
- latencia;
    
- coste.
    

### Fase 9 — Memoria y reproducibilidad

Publicar código, configuración de experimentos, resultados y metodología.

---

# 22. Aportación académica

La contribución no tendría que consistir necesariamente en inventar un algoritmo completamente nuevo.

Para un TFG sería suficiente una aportación rigurosa como:

> **Diseño y evaluación experimental de una arquitectura multicapa para reducir el impacto de indirect prompt injection en agentes LLM mediante aislamiento de contexto, mínimo privilegio y validación externa de acciones.**

La aportación tendría cuatro componentes:

**1. Arquitectura**

Una propuesta concreta de separación entre razonamiento y autorización.

**2. Implementación**

Security Gateway reutilizable.

**3. Evaluación**

Benchmark reproducible sobre AgentDojo.

**4. Resultados**

Análisis cuantitativo del compromiso entre:

[  
Seguridad  
\leftrightarrow  
Utilidad  
\leftrightarrow  
Coste  
]

---

# 23. Resultado esperado

Al terminar el TFG debería ser posible responder con datos a preguntas como:

```
¿Reduce realmente el mínimo privilegio los ataques?

¿Cuánto mejora al añadir validación de acciones?

¿Qué tareas legítimas rompe cada defensa?

¿Cuánto aumenta la latencia?

¿Qué ocurre frente a ataques adaptativos?

¿Es más eficaz detectar la inyección
o impedir que produzca consecuencias?
```

Una posible conclusión experimental podría tener una forma similar a:

> La aplicación conjunta de mínimo privilegio y validación independiente de acciones reduce significativamente la tasa de éxito de ataques respecto al agente base, manteniendo una utilidad elevada en tareas legítimas. Las defensas basadas exclusivamente en manipulación del contexto presentan una mayor degradación frente a ataques adaptativos.

Naturalmente, esa conclusión tendría que surgir de los experimentos y no asumirse previamente.

---

# 24. Qué haría especialmente interesante este TFG

La fortaleza del proyecto está en que conecta varias disciplinas de Ingeniería Informática:

```
                    ┌───────────────┐
                    │      IA       │
                    └───────┬───────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
Ciberseguridad     Ingeniería software      Sistemas
        │                   │                   │
        └───────────────────┼───────────────────┘
                            │
                            ▼
                     LLM Agents
```

Permite trabajar con:

- IA generativa;
    
- arquitectura software;
    
- ciberseguridad;
    
- sistemas de autorización;
    
- APIs;
    
- protocolos;
    
- testing;
    
- Docker;
    
- experimentación;
    
- análisis de datos;
    
- estadística;
    
- MCP.
    

Sin necesitar entrenar grandes modelos ni disponer de una infraestructura de GPUs importante.

---

# 25. Título recomendado

Mantendría como título principal:

> **Evaluación y endurecimiento de agentes LLM con acceso a herramientas frente a ataques de inyección indirecta de prompts**

Si se quisiera hacer algo más técnico:

> **Diseño y evaluación de mecanismos de aislamiento, autorización y control de herramientas para agentes LLM frente a inyección indirecta de prompts**

Y si se quisiera enfatizar más el producto:

> **Diseño de una capa de seguridad para agentes LLM con acceso a herramientas y evaluación de su robustez frente a inyección indirecta de prompts**

De los tres, el segundo refleja mejor el contenido científico del TFG.

---

# 26. Idea central del proyecto

El planteamiento que debería mantenerse durante todo el desarrollo puede resumirse en una sola idea:

> **No intentar conseguir que el LLM sea imposible de engañar, sino construir un sistema en el que engañar al LLM no otorgue automáticamente al atacante capacidad para realizar acciones peligrosas.**

Esto traslada el problema desde:

```
"¿Cómo hago que el modelo nunca siga
una instrucción maliciosa?"
```

a una pregunta más propia de Ingeniería Informática y seguridad:

```
"Si uno de mis componentes puede ser
comprometido, ¿cómo diseño el sistema
para limitar sus privilegios y contener
las consecuencias?"
```

Ese enfoque proporciona al proyecto una base técnica mucho más sólida y lo diferencia de un simple estudio sobre prompting.