### Agent Security Lab: Diseño y desarrollo de una herramienta CLI para la evaluación de seguridad de agentes LLM con acceso a herramientas

**Titulación:** Grado en Ingeniería Informática  
**Modalidad:** Proyecto de ingeniería de software con evaluación experimental  
**Periodo de desarrollo:** Noviembre de 2026 – Mayo de 2027  
**Fecha prevista de entrega:** 17 de mayo de 2027

---

## 1. Resumen

Los avances recientes en inteligencia artificial generativa han impulsado el desarrollo de agentes basados en grandes modelos de lenguaje (_Large Language Models_, LLM). A diferencia de los sistemas conversacionales tradicionales, estos agentes pueden interactuar con herramientas externas, consultar información, planificar tareas y ejecutar acciones sobre otros sistemas informáticos.

Esta capacidad amplía considerablemente sus aplicaciones, pero también introduce nuevos riesgos de seguridad. Entre ellos destaca la **inyección indirecta de prompts**, una técnica mediante la cual un atacante introduce instrucciones maliciosas en fuentes de información externas que posteriormente son procesadas por un agente. Estos ataques pueden provocar que el sistema altere su comportamiento o utilice herramientas de manera no autorizada.

El presente Trabajo Fin de Grado propone el diseño y desarrollo de **Agent Security Lab**, una herramienta de línea de comandos (CLI) destinada a la experimentación y evaluación de la seguridad de agentes LLM con acceso a herramientas.

La herramienta permitirá configurar escenarios de prueba mediante comandos reproducibles, simular ataques de inyección indirecta de prompts, aplicar diferentes mecanismos de defensa y analizar el comportamiento de los agentes durante su ejecución.

Como caso de uso inicial, se desarrollará un agente especializado en tareas DevOps que interactuará con una infraestructura informática simulada. Este entorno permitirá estudiar situaciones en las que información externa maliciosa intenta inducir al agente a realizar operaciones administrativas no autorizadas.

El proyecto adoptará un enfoque de ingeniería de software, priorizando el desarrollo de una herramienta funcional, modular y extensible. Su utilidad se validará mediante una evaluación experimental que compare diferentes estrategias de protección utilizando métricas objetivas y escenarios reproducibles.

**Palabras clave:** inteligencia artificial, LLM, agentes autónomos, Agent Engineering, prompt injection, ciberseguridad, seguridad de herramientas, evaluación experimental.

---

## 2. Introducción y motivación

La inteligencia artificial generativa está evolucionando desde sistemas orientados principalmente a producir contenido hacia aplicaciones capaces de interactuar con su entorno.

Los agentes basados en LLM representan una de las principales manifestaciones de esta evolución. Estos sistemas utilizan modelos de lenguaje para interpretar solicitudes, seleccionar herramientas y coordinar operaciones que permiten completar tareas de cierta complejidad.

Sus aplicaciones abarcan ámbitos como el desarrollo de software, la administración de sistemas, la automatización empresarial, las finanzas y la ciberseguridad.

Sin embargo, la capacidad de ejecutar acciones introduce desafíos que no aparecen con la misma intensidad en los sistemas conversacionales convencionales.

Un agente que únicamente genera texto puede producir información incorrecta. En cambio, un agente con acceso a herramientas también puede modificar archivos, alterar configuraciones, consultar información restringida o interactuar con servicios externos.

Por ello, resulta fundamental estudiar cómo garantizar que sus acciones respondan a las instrucciones legítimas del usuario y respeten las restricciones de seguridad establecidas.

Uno de los problemas más relevantes es la **inyección indirecta de prompts**.

Este tipo de ataque se produce cuando un agente procesa contenido externo que incluye instrucciones introducidas por un tercero. Si el agente interpreta dichas instrucciones como órdenes que debe cumplir, el atacante puede influir en su comportamiento sin interactuar directamente con él.

Por ejemplo, un agente encargado de diagnosticar problemas en una infraestructura podría consultar documentación o registros que contengan instrucciones maliciosas. El atacante podría intentar conseguir que el agente desactive un mecanismo de seguridad, modifique una configuración o acceda a información que no necesita para completar su tarea.

Este problema plantea cuestiones importantes relacionadas con la separación entre datos e instrucciones, la autorización de operaciones y el diseño de arquitecturas seguras para agentes autónomos.

### Motivación del proyecto

El interés de este TFG surge de la combinación de tres áreas de la ingeniería informática:

**Inteligencia artificial:** comprender el funcionamiento de los modelos de lenguaje y desarrollar sistemas capaces de razonar operativamente, seleccionar herramientas y ejecutar tareas.

**Ingeniería de software:** diseñar una aplicación completa que integre agentes, simuladores, mecanismos de seguridad, evaluación automatizada y visualización de resultados.

**Ciberseguridad:** investigar las vulnerabilidades que aparecen cuando los agentes procesan información no confiable y tienen capacidad para ejecutar acciones.

La propuesta busca combinar estos ámbitos mediante el desarrollo de un producto práctico, cuyo diseño y evaluación permitan adquirir conocimientos avanzados de ingeniería de sistemas de inteligencia artificial.

---

## 3. Problema que se pretende resolver

La incorporación de herramientas externas a los agentes LLM aumenta su utilidad, pero también amplía las consecuencias potenciales de un comportamiento incorrecto.

En particular, los ataques de inyección indirecta de prompts pueden intentar aprovechar la incapacidad de un agente para distinguir adecuadamente entre instrucciones autorizadas e información procedente de fuentes no confiables.

Aunque existen mecanismos de protección y herramientas de evaluación de seguridad para sistemas basados en LLM, su eficacia depende del modelo, el contexto, las herramientas disponibles y las características del ataque.

Esto plantea la necesidad de disponer de entornos controlados donde sea posible:

- Reproducir situaciones de ataque sobre agentes que utilizan herramientas.
- Observar las acciones que realizan durante su ejecución.
- Determinar si dichas acciones estaban autorizadas.
- Comparar diferentes mecanismos de protección.
- Evaluar el impacto de las defensas sobre la ejecución de tareas legítimas.

**El problema que aborda este proyecto es la necesidad de evaluar, de manera controlada y reproducible, la resistencia de agentes LLM con acceso a herramientas frente a intentos de manipulación mediante información externa.**

Agent Security Lab pretende ofrecer un entorno experimental que facilite este análisis y permita estudiar las ventajas y limitaciones de distintas estrategias defensivas.

La plataforma no buscará garantizar la seguridad absoluta de los agentes, sino proporcionar mecanismos para analizar su comportamiento y obtener evidencias sobre la eficacia de las protecciones evaluadas.

---

## 4. Objetivos

### 4.1. Objetivo general

Diseñar, implementar y evaluar una herramienta CLI modular, instalable y automatizable que permita experimentar con agentes basados en modelos de lenguaje, reproducir ataques de inyección indirecta de prompts y comparar mecanismos de defensa destinados a prevenir el uso no autorizado de herramientas.

### 4.2. Objetivos específicos

**OE1. Estudiar los fundamentos de los agentes LLM y sus riesgos de seguridad.**

Analizar la arquitectura y funcionamiento de los modelos de lenguaje, los mecanismos de uso de herramientas, las principales vulnerabilidades asociadas a los agentes y las estrategias existentes para mitigarlas.

**OE2. Diseñar una arquitectura modular y extensible.**

Definir una arquitectura que separe la ejecución de agentes, los escenarios simulados, los ataques, los mecanismos defensivos y la evaluación de resultados.

**OE3. Implementar un agente LLM con acceso a herramientas.**

Desarrollar un agente propio capaz de interpretar tareas, consultar información externa y ejecutar operaciones mediante herramientas definidas.

**OE4. Desarrollar un entorno de simulación DevOps.**

Construir una infraestructura ficticia sobre la que el agente pueda realizar operaciones de consulta, diagnóstico y administración, sin interactuar con sistemas de producción.

**OE5. Implementar escenarios reproducibles de ataque.**

Diseñar casos de inyección indirecta de prompts que intenten provocar acciones no autorizadas a través de documentación, registros o respuestas de herramientas.

**OE6. Integrar mecanismos de defensa existentes.**

Implementar y comparar diferentes estrategias de protección, incluyendo mecanismos basados en instrucciones y controles de autorización independientes del modelo.

**OE7. Desarrollar una interfaz CLI para la experimentación interactiva.**

Permitir seleccionar escenarios, ataques y defensas mediante comandos; ejecutar agentes de forma interactiva; inspeccionar trazas y presentar resultados en tablas de terminal.

**OE8. Automatizar la ejecución y evaluación de experimentos.**

Desarrollar comandos para ejecutar baterías de pruebas, recopilar resultados, calcular métricas de seguridad y rendimiento y exportar datos en formatos reutilizables como JSON y CSV.

**OE9. Evaluar experimentalmente la plataforma y los mecanismos defensivos.**

Realizar una campaña de pruebas que permita analizar la eficacia de las defensas, su impacto sobre las tareas legítimas y las limitaciones observadas.

---

## 5. Descripción de la solución propuesta

Agent Security Lab se desarrollará como una herramienta CLI de Python que permitirá experimentar con agentes LLM dentro de escenarios controlados.

La plataforma estará organizada en componentes independientes, con el objetivo de facilitar su mantenimiento, evaluación y futura ampliación.

### 5.1. Motor de agentes

Este componente será responsable de ejecutar los agentes LLM.

Gestionará la interacción con el modelo de lenguaje, el contexto de ejecución, las llamadas a herramientas y los resultados devueltos por estas.

Inicialmente se implementará un agente propio para comprender y controlar directamente su funcionamiento.

Se priorizará una implementación sencilla y transparente, evitando introducir dependencias complejas cuando no aporten ventajas necesarias para el proyecto.

### 5.2. Motor de simulación

Permitirá representar entornos ficticios sobre los que los agentes puedan actuar.

Cada escenario definirá sus propios datos, herramientas, operaciones y condiciones de evaluación.

La arquitectura se diseñará para admitir **escenarios intercambiables**, de manera que sea posible incorporar nuevos dominios sin modificar los componentes principales del laboratorio.

No obstante, el alcance obligatorio del TFG incluirá únicamente un escenario DevOps.

### 5.3. Escenario DevOps

El escenario inicial representará una infraestructura informática simplificada formada por diferentes servicios, como una API, una base de datos y un sistema de autenticación.

El agente podrá consultar métricas, registros y configuraciones, así como ejecutar determinadas operaciones administrativas simuladas.

Se establecerán distintos niveles de autorización:

- **Operaciones de consulta:** acceso a información permitida.
- **Operaciones rutinarias:** acciones de bajo riesgo que el agente podrá ejecutar autónomamente.
- **Operaciones sensibles:** modificaciones que requerirán autorización explícita o deberán ser bloqueadas cuando no estén justificadas por la tarea.

El objetivo será estudiar si un atacante puede conseguir que el agente utilice indebidamente sus herramientas mediante instrucciones introducidas en información externa.

### 5.4. Motor de ataques

Permitirá configurar y ejecutar escenarios de inyección indirecta de prompts.

Los ataques se introducirán en fuentes de información simuladas, como documentos, registros o respuestas de herramientas.

Cada experimento contará con una tarea legítima y un objetivo malicioso claramente definidos.

Esto permitirá comprobar si el agente completa correctamente la solicitud original y si intenta realizar acciones que no estaban autorizadas.

### 5.5. Motor de defensas

Este componente permitirá incorporar y activar diferentes mecanismos de protección.

Se estudiarán estrategias existentes, que podrán incluir instrucciones de seguridad, separación de contenidos no confiables y controles externos sobre las llamadas a herramientas.

El diseño modular facilitará la comparación de distintas defensas bajo condiciones experimentales equivalentes.

Como ampliación opcional, se contempla desarrollar una estrategia de protección propia y evaluar su comportamiento frente a las alternativas existentes.

### 5.6. Motor de evaluación

Será responsable de registrar las ejecuciones y determinar los resultados de los experimentos.

Se utilizarán métricas como:

|Métrica|Descripción|
|---|---|
|Attack Success Rate|Porcentaje de ataques que consiguen provocar la acción maliciosa definida|
|Task Success Rate|Porcentaje de tareas legítimas completadas correctamente|
|False Positive Rate|Frecuencia con la que una defensa bloquea acciones legítimas|
|Latencia|Tiempo necesario para completar las ejecuciones|
|Coste computacional|Recursos o tokens consumidos durante los experimentos|

Siempre que sea posible, la evaluación se realizará mediante comprobaciones deterministas sobre el estado de la simulación y las llamadas a herramientas.

### 5.7. Interfaz de línea de comandos (CLI)

La herramienta ofrecerá una interfaz de terminal desde la que seleccionar escenarios, ataques, defensas y modelos, así como ejecutar experimentos y consultar sus resultados. El diseño separará la lógica de negocio de la capa de presentación para permitir futuras interfaces sin modificar el núcleo experimental.

El **MVP** incorporará un modo de ejecución interactiva con trazas legibles de las llamadas a herramientas, decisiones de autorización y cambios en el estado simulado. Posteriormente se implementarán comandos de _benchmark_ para ejecutar conjuntos de pruebas de forma automatizada y comparar resultados entre defensas.

Se prevé la exportación de resultados y registros en formatos estructurados (JSON y CSV), facilitando su análisis y reproducción. Una interfaz de terminal a pantalla completa (TUI), informes HTML o una aplicación web se considerarán ampliaciones opcionales, no requisitos del MVP.

Como ejemplos ilustrativos de uso, la CLI podría admitir comandos como:

```
agentsec scenarios list
agentsec run --scenario devops --attack malicious-log --defense none
agentsec benchmark --scenario devops --defenses all --runs 30
```

La sintaxis definitiva se establecerá durante el diseño y podrá variar respecto a estos ejemplos.

---

## 6. Metodología de desarrollo

El proyecto seguirá una metodología incremental e iterativa, organizada en fases que permitan obtener versiones funcionales de la plataforma de manera progresiva.

En primer lugar, se realizará un estudio de los fundamentos de los agentes LLM, sus principales vulnerabilidades y las herramientas de evaluación existentes.

Posteriormente se definirán los requisitos y la arquitectura de la aplicación, prestando especial atención a la separación de responsabilidades entre sus componentes.

La implementación comenzará con el desarrollo del agente propio y del entorno DevOps simulado.

Una vez validado su funcionamiento, se incorporarán los escenarios de ataque y los mecanismos defensivos.

El siguiente paso será implementar los comandos de ejecución interactiva y visualización de trazas, integrar el sistema de evaluación y automatizar la ejecución de experimentos mediante comandos reproducibles.

Finalmente, se realizará una campaña experimental destinada a analizar el comportamiento del agente bajo distintas configuraciones.

### Evaluación experimental

La evaluación se estructurará mediante experimentos controlados.

Para cada escenario se establecerán una tarea legítima, un objetivo de ataque, las herramientas disponibles y los criterios que determinan el éxito o fracaso.

Se compararán ejecuciones sin protección y ejecuciones con diferentes mecanismos defensivos.

Cuando se utilicen modelos probabilísticos, se realizarán repeticiones suficientes para analizar la variabilidad de los resultados, dentro de los límites de tiempo y recursos disponibles.

El análisis permitirá identificar qué estrategias resultan más eficaces en los escenarios estudiados y qué compromisos introducen respecto al rendimiento y la capacidad del agente para completar tareas.

---

## 7. Tecnologías previstas

Se propone utilizar las siguientes tecnologías, sujetas a revisión durante la fase de diseño:

|Tecnología|Aplicación|
|---|---|
|**Python**|Desarrollo de la CLI, el agente, los simuladores, los ataques y las defensas|
|**Typer**|Definición de comandos, argumentos y opciones de la CLI|
|**Rich**|Presentación de trazas, tablas y estados en terminal|
|**Pydantic**|Validación de configuraciones y datos de experimentos|
|**SQLite**|Persistencia local de ejecuciones y resultados|
|**JSON / CSV**|Exportación e intercambio de resultados reproducibles|
|**Pandas / Matplotlib**|Análisis estadístico y generación de gráficas para informes|
|**Pytest**|Pruebas automatizadas del núcleo y los escenarios|
|**Docker (si es necesario)**|Reproducibilidad y aislamiento de componentes|
|**Modelos LLM mediante API o ejecución local**|Inferencia y ejecución del agente|
|**Git y GitHub**|Control de versiones y gestión del proyecto|

Se valorará el uso de bibliotecas especializadas para la evaluación de agentes y la instrumentación de ejecuciones, siempre que su incorporación esté justificada por los requisitos.

La selección definitiva se realizará durante las primeras fases del proyecto.

---

## 8. Planificación temporal

El desarrollo se llevará a cabo entre noviembre de 2026 y mayo de 2027.

|Periodo|Actividades|
|---|---|
|**Noviembre de 2026**|Estudio de fundamentos, análisis de soluciones existentes, definición de requisitos y arquitectura|
|**Diciembre de 2026**|Implementación del agente LLM y del entorno DevOps simulado|
|**Enero de 2027**|Desarrollo del motor de ataques y primeros escenarios experimentales|
|**Febrero de 2027**|Implementación de defensas, registro de ejecuciones y evaluación inicial|
|**Marzo de 2027**|Desarrollo y pulido de la CLI interactiva, visualización de trazas e integración de los componentes|
|**Abril de 2027**|Automatización de experimentos, campaña de evaluación y análisis de resultados|
|**1-10 de mayo de 2027**|Finalización de la memoria, documentación y preparación de la demostración|
|**11-16 de mayo de 2027**|Revisión final y margen para imprevistos|
|**17 de mayo de 2027**|Entrega del Trabajo Fin de Grado|

La redacción de la memoria se realizará de forma progresiva durante todo el desarrollo.

Se priorizará disponer de un MVP funcional antes de comenzar las ampliaciones opcionales.

---

## 9. Resultados esperados

Al finalizar el proyecto se espera disponer de una herramienta CLI funcional que permita estudiar la seguridad de agentes LLM con acceso a herramientas.

Los principales resultados previstos son:

**Una CLI instalable y operativa**, desde la que configurar experimentos, ejecutar agentes, inspeccionar trazas y consultar resultados.

**Un agente LLM propio**, capaz de interactuar con herramientas dentro de un entorno controlado.

**Un simulador DevOps**, con servicios y operaciones ficticias que permitan reproducir tareas legítimas y situaciones de riesgo.

**Un conjunto de escenarios de ataque**, diseñados para evaluar intentos de uso no autorizado de herramientas mediante inyección indirecta de prompts.

**Una implementación de mecanismos defensivos**, que permita comparar diferentes estrategias de protección.

**Un sistema de evaluación reproducible**, capaz de registrar ejecuciones, ejecutar benchmarks automatizados, calcular métricas objetivas y exportar resultados estructurados.

**Un análisis experimental**, que documente los resultados obtenidos, las limitaciones observadas y las conclusiones derivadas de las pruebas.

Adicionalmente, se espera que la arquitectura facilite futuras ampliaciones, como la incorporación de escenarios financieros o la integración de agentes externos.

---

## 10. Alcance y limitaciones

Para garantizar la viabilidad del proyecto, se establecerán las siguientes delimitaciones.

El laboratorio se centrará en agentes LLM con acceso a herramientas y en ataques de inyección indirecta de prompts cuyo objetivo sea provocar acciones no autorizadas.

El escenario obligatorio será una infraestructura DevOps simulada. No se realizarán pruebas sobre sistemas de producción ni se utilizarán datos sensibles reales.

Se desarrollará un agente propio y se compararán mecanismos de defensa existentes. El diseño de una defensa original se considerará una ampliación opcional.

Aunque la plataforma se diseñará para admitir escenarios intercambiables, no será obligatorio implementar varios dominios durante el TFG.

La evaluación se limitará a los modelos, escenarios y ataques seleccionados. Por tanto, sus resultados no podrán extrapolarse automáticamente a todos los agentes LLM ni interpretarse como una garantía general de seguridad.

La prioridad será desarrollar una herramienta CLI estable, comprensible, instalable y reproducible, antes que maximizar el número de funcionalidades. No se exigirá una interfaz web ni una TUI a pantalla completa; ambas podrán abordarse como ampliaciones opcionales.

---

## 11. Conclusión

El desarrollo de agentes LLM capaces de interactuar con herramientas externas representa una evolución significativa en las aplicaciones de inteligencia artificial, pero introduce nuevos desafíos relacionados con la seguridad y el control de sus acciones.

Agent Security Lab propone abordar estos desafíos mediante el diseño y desarrollo de una herramienta CLI experimental que permita reproducir ataques, analizar ejecuciones y evaluar diferentes mecanismos de protección.

El proyecto combina conocimientos de inteligencia artificial, ingeniería de software y ciberseguridad, proporcionando una aplicación práctica de los fundamentos de _AI Engineering_.

Su principal aportación será una herramienta modular y funcional que facilite el estudio reproducible de la seguridad de agentes LLM, acompañada de una evaluación experimental de las estrategias defensivas implementadas.

El alcance definido y la planificación propuesta permiten abordar el desarrollo dentro del periodo disponible, manteniendo la posibilidad de incorporar mejoras adicionales si el progreso del proyecto lo permite.

---

### Estado de la propuesta

Este documento constituye un **primer borrador académico**, preparado a partir de las decisiones tomadas durante nuestra sesión socrática.

Antes de presentarlo al tutor, recomiendo realizar una revisión bibliográfica específica de plataformas existentes —especialmente AgentDojo, PyRIT, garak y promptfoo— para delimitar la aportación de Agent Security Lab y evitar afirmaciones de originalidad que todavía no hemos verificado.

También habrá que adaptar su extensión y estructura a la plantilla oficial de propuesta de TFG de tu universidad, si existe.

**La temática, el producto CLI, los objetivos, el alcance y el calendario ya están definidos.** No es necesario resolver ahora los detalles de implementación para presentar una propuesta técnicamente fundamentada.