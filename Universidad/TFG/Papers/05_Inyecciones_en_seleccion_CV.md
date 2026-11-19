# 05. Prompt injection en la evaluación automática de currículos

> **Lectura crítica para el TFG — Agent Security Lab.** Resumen en español del artículo, con separación entre lo que revisan los autores y las propuestas de aplicación al proyecto. **Es una revisión narrativa principalmente conceptual**: no debe citarse como si demostrara mediante un experimento amplio la eficacia de una defensa.

## Ficha bibliográfica

- **Título:** *‘Ignore All and Accept My Resume’: The Impact of Prompt Injection in Automatic Resume Screening*.
- **Autores:** Loubna Aminou, Abdelaziz Daaif, Maha Soulami y Mohamed Youssfi.
- **Publicación:** *2025 5th International Conference on Innovative Research in Applied Science, Engineering and Technology* (IRASET), IEEE.
- **DOI:** https://doi.org/10.1109/IRASET64571.2025.11008146
- **Naturaleza:** revisión narrativa de riesgos, ataques y defensas, aplicada al cribado automático de CV.
- **Lectura esencial:** pp. 1–2 (contexto), p. 3 (categorías de ataques y defensas), p. 4 (limitaciones y futuro).
- **Relevancia para el TFG:** **media**, útil para explicar **inyección indirecta mediante documentos** y organizar una taxonomía básica de defensas; menos útil como fuente de métricas experimentales.

## 1. El problema en lenguaje sencillo

Un sistema de selección de personal puede utilizar un LLM para leer un currículo, extraer datos y valorarlo frente a una oferta. El currículo es **contenido suministrado por una parte interesada**, no una instrucción del responsable de selección.

El atacante puede colocar dentro del CV instrucciones cuyo objetivo es que el modelo **deje de evaluar el documento como información y empiece a obedecerlo como una orden**. La consecuencia podría ser una clasificación favorable que no refleje la experiencia real del candidato.

### Ejemplo conceptual del ataque

```text
Responsable de contratación:
  «Analiza este CV y comprueba los requisitos de la vacante».

Documento proporcionado por el candidato:
  Experiencia, formación, habilidades...
  [Contenido malicioso: pide ser considerado apto sin evaluación]

Comportamiento indebido:
  El LLM toma la instrucción del documento como una regla
  y altera la recomendación final.
```

**Lo importante no son las palabras concretas del ataque; es el cambio de autoridad:** un documento que debía ser **objeto de análisis** intenta convertirse en **fuente de instrucciones**.

## 2. Por qué este ejemplo es relevante para agentes LLM

Un CV es equivalente, desde el punto de vista de límites de confianza, a otros contenidos que un agente necesita leer:

- Una página web recuperada por un buscador.
- Un informe o archivo Markdown externo.
- Un ticket de soporte.
- Una respuesta textual de una herramienta.
- Una línea de log en un servidor.

**Traslado al TFG (inferencia propia):** si el agente DevOps lee un log con una instrucción insertada por un tercero, el fallo es análogo al del CV: **datos externos se convierten en órdenes**. En DevOps el riesgo puede ser mayor porque la orden inducida puede disparar **herramientas que cambian el estado** del entorno simulado.

## 3. Tipos de ataque y mecanismos mencionados

El artículo diferencia **prompt injection directa** e **indirecta**:

| Modalidad | Superficie del atacante | Ejemplo de riesgo |
|---|---|---|
| **Directa** | Entrada dirigida directamente al modelo | Intentar alterar sus reglas de respuesta |
| **Indirecta** | Contenido de terceros que el modelo debe analizar | Ocultar órdenes dentro de un CV o documento |

En la **sección V, p. 3**, se describen varios patrones de formulación adversaria:

### A. Anulación directa de instrucciones

Mensajes que intentan sustituir las indicaciones originales o forzar una decisión final. Son fáciles de entender, aunque no necesariamente los más difíciles de detectar.

### B. Ingeniería social y suplantación de autoridad

Texto que solicita o sugiere que el lector atribuya autoridad especial a lo que contiene el documento; por ejemplo, persuadir al modelo de que trate favorablemente al candidato.

### C. Manipulación de contexto

Frases que no ordenan frontalmente «ignora las reglas», sino que buscan **reencuadrar la interpretación** del documento: dar más importancia a algunas cualidades o alterar la conclusión que debería derivarse de los datos.

### D. Patrones que aparentan comandos

El paper también incluye ejemplos de formulaciones con aspecto de órdenes de sistema. **No debe inferirse de su mera presencia en un CV que un comando se ejecute sobre el sistema operativo**: eso requiere que la aplicación permita al modelo realizar acciones externas o no valide dichas acciones.

## 4. Cómo se realizó la revisión

Los autores describen una **búsqueda manual en Google Scholar**, con términos sobre prompt injection y cribado de currículos, y una selección de trabajos generales y específicos del dominio (sección II, p. 2). Clasifican enfoques de ataque y de defensa y analizan sus posibles implicaciones.

**Alcance de la evidencia:** el artículo **no presenta un benchmark propio amplio**, un conjunto de ataques y defensas reproducido de extremo a extremo ni métricas comparativas nuevas de tasa de éxito de ataque. Su fuerza es introductoria: **contextualiza el problema y reúne técnicas previas**, no determina cuál es la defensa más eficaz.

## 5. Defensas identificadas por los autores

El artículo organiza defensas en **detección** y **prevención o transformación del prompt** (sección IV, pp. 2–3).

### Detección por perplejidad (PPL)

La **perplejidad** mide hasta qué punto un modelo encuentra inesperada una secuencia. Algunas formulaciones adversarias producen cadenas lingüísticamente anómalas y pueden ser detectadas por este criterio.

- **Ventaja:** puede detectar texto extraño o tokens adversarios sin depender únicamente de coincidencias de palabras.
- **Límite:** una inyección redactada con fluidez y coherencia podría tener una perplejidad normal. **PPL baja no demuestra que un texto sea seguro.**

### Detección por ventanas (*windowed PPL*)

Divide un documento en segmentos y estima la perplejidad de cada uno. Busca detectar anomalías locales que podrían pasar desapercibidas al promediar el documento completo.

**Idea reutilizable:** en textos largos (logs, tickets, documentación) conviene evaluar **fragmentos**, no solo un único promedio global.

### Paráfrasis

Reescribir una entrada antes de pasarla al modelo puede modificar determinadas formulaciones adversarias, símbolos o intentos explícitos de saltarse instrucciones.

**Limitación inferida:** una paráfrasis puede conservar el **significado** de la orden maliciosa o eliminar información útil para la tarea. No constituye una barrera determinista.

### Retokenización

Transformar la segmentación de la entrada en unidades de tokens distintas puede romper determinados patrones de ataque sensibles a la forma exacta.

**Limitación inferida:** no resuelve automáticamente el problema conceptual de que el modelo trate una orden de baja confianza como autorizada.

### Otras líneas citadas — tabla I, p. 3

El paper menciona, entre otras referencias:

- **Momentum Greedy Coordinate Gradient:** optimización ofensiva mediante gradientes.
- **PromptFuzz:** generación y prueba automatizada de inputs adversarios.
- **SecAlign:** defensa basada en alineamiento/ajuste fino frente a inyección.
- **PromptCare:** sistema de protección y verificación de prompts mediante técnicas de marca de agua; **no es, por sí solo, una política de autorización de herramientas**.

No debe interpretarse que los autores han implementado y comparado todas estas técnicas en un mismo entorno experimental.

## 6. Conclusiones de los autores

La automatización de contratación introduce una amenaza cuando un LLM debe leer **contenido controlado por el candidato**. Los autores recomiendan reforzar las **fronteras de confianza**, combinar medidas de seguridad y mantener **supervisión humana** en decisiones sensibles. También proponen investigar filtros y análisis que funcionen con varios idiomas y tipos de documento.

### Limitaciones reconocidas — p. 4

- Campo emergente y bibliografía todavía limitada en el periodo revisado.
- Dependencia de trabajos preliminares y preprints.
- Heterogeneidad de sistemas y variantes de ataque.
- Ausencia de una validación experimental exhaustiva de todas las protecciones que enumera.

## 7. Relación con el paper posterior de GUARD

**Dato bibliográfico útil:** este paper de 2025 y el estudio **GUARD**, publicado en línea en noviembre de 2026, comparten los cuatro autores. Pueden leerse como piezas complementarias:

| Aspecto | Revisión de 2025 | GUARD, artículo de 2026/vol. 2027 |
|---|---|---|
| Pregunta principal | ¿Por qué el cribado con LLM es vulnerable? | ¿Podemos detectar fragmentos maliciosos semánticamente? |
| Tipo de trabajo | Revisión narrativa | Estudio de un detector concreto con evaluación |
| Datos propios cuantitativos | Limitados | Matrices de confusión, AUC, recall, comparadores |
| Uso en tu TFG | Introducción y taxonomía | Diseño y medición de una defensa |

**Recomendación académica:** usar el paper de 2025 para **justificar el problema** y el de GUARD para **citar resultados medidos**; evita atribuir al primero cifras que provienen del segundo.

## 8. Aplicación al diseño de Agent Security Lab (propuesta propia)

### Modelo de amenaza equivalente para DevOps

```text
Usuario legítimo → «Investiga el fallo de la API»
                         ↓
Agente ejecuta read_logs("api")
                         ↓
Log simulado con texto controlado por atacante
                         ↓
El agente debería analizarlo como DATOS
                         ↓
Intento adversario: convertirlo en instrucción
                         ↓
¿El agente intenta una operación no autorizada?
                         ↓
El autorizador ¿bloquea o permite la ejecución?
```

El paper sirve para explicar **por qué** separar fuentes de información, funciones y permisos. Para medir eficacia real habría que completar su enfoque con protocolos como el de GUARD y con un simulador que verifique acciones.

### Defensas candidatas a comparar

1. **Recordatorio de jerarquía de instrucciones:** los logs no dan órdenes.
2. **Detección previa del texto:** PPL, similitud semántica u otro detector.
3. **Control de acciones:** reglas deterministas que deniegan operaciones no autorizadas incluso si el LLM las solicita.

En el MVP convendría priorizar las opciones 1 y 3; añadir detectores como complemento experimental, no como sustituto del control de permisos.

## 9. Conceptos que conviene dominar

**Frontera de confianza**, inyección directa/indirecta, datos frente a instrucciones, sistema de seguimiento de candidatos (*ATS*), revisión narrativa, perplejidad, tokenización, *prompt fuzzing*, detección de anomalías y revisión humana.

## 10. Preguntas para continuar investigando

1. ¿Qué condiciones técnicas permiten que una instrucción oculta en un archivo altere el flujo de control?
2. ¿Qué diferencias hay entre detectar texto sospechoso y evitar que se ejecute una acción peligrosa?
3. ¿Puede un texto adversario bien escrito evitar detectores basados en perplejidad?
4. ¿Qué fallos aparecen cuando la aplicación concatena documentos externos directamente al contexto?
5. ¿Cómo demostrar que el agente sigue completando tareas legítimas con la defensa activada?

## Conclusión para tu TFG

Este artículo es una **buena puerta de entrada conceptual** a la inyección indirecta en documentos y a las familias generales de detección/prevención. Su principal valor reside en ilustrar la **confusión entre datos e instrucciones**. Para defender rigurosamente la eficacia de una técnica, complementa su lectura con estudios experimentales y con los resultados medidos en tu propio laboratorio.

**Ubicación rápida:** motivación y preguntas (p. 1); método de revisión, ataques directos e indirectos, PPL y paráfrasis (p. 2); patrones de ataque en CV y tabla I (p. 3); limitaciones, futuro y conclusiones (p. 4).
