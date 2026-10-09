Sí. Y, de hecho, creo que estás describiendo **uno de los cambios más importantes que está viviendo la profesión de informática**.

Lo primero que te diría es que **no estás perdiendo la motivación porque seas peor programador o porque no tengas disciplina**. Estás intentando medir tu progreso con una métrica que ha dejado de tener demasiado sentido: *«¿Qué rápido soy escribiendo código?»*. En 2026, esa ya no es una competición especialmente interesante para un humano.

La pregunta está empezando a ser:

> **«¿Soy capaz de entender un problema, diseñar una solución, evaluar si la solución de una IA es correcta y dirigir a la IA para construirla?»**

Y eso cambia muchísimo cómo creo que deberías estudiar Ingeniería Informática.

---

# 1. No competiría contra la IA escribiendo código

Imagínate que tienes que construir una aplicación.

Hace unos años, una persona competente podía necesitar:

* 3 horas pensando la arquitectura.
* 5 horas programando.
* 2 horas depurando.
* 2 horas buscando documentación.
* 3 horas haciendo pruebas.

Ahora puedes pedirle a una IA que genere buena parte de esas 12 horas de trabajo.

Pero hay una parte que **no desaparece**:

* ¿Qué arquitectura necesitamos?
* ¿Qué requisitos son realmente importantes?
* ¿Qué base de datos tiene sentido?
* ¿Dónde están los problemas de seguridad?
* ¿Qué pasa cuando tenemos 100.000 usuarios?
* ¿Este algoritmo es adecuado?
* ¿Por qué esta aplicación está tardando 2 segundos?
* ¿Qué ocurre si se cae este servicio?
* ¿Cómo diseñamos la API?
* ¿Qué información debería almacenar?
* ¿Cómo sabemos que el código generado es correcto?
* ¿Qué concesiones estamos haciendo?
* ¿Qué solución es mejor entre A, B y C?

Eso es **ingeniería**.

Y es precisamente lo que yo intentaría convertir en tu ventaja.

---

# 2. Yo cambiaría tu objetivo: de «aprender a programar» a «aprender informática»

Hay una diferencia enorme.

Programar es una herramienta dentro de la informática.

Si mañana las IAs generan el 95 % del código de una aplicación, **los fundamentos de informática siguen existiendo**.

Por ejemplo:

### Sistemas operativos

No necesitas convertirte en un experto escribiendo un kernel.

Pero sí deberías comprender:

* procesos
* hilos
* memoria virtual
* concurrencia
* scheduling
* llamadas al sistema
* sistemas de archivos
* permisos
* aislamiento
* virtualización
* contenedores

Porque entonces, cuando una IA te diga:

> «Esta aplicación tiene un problema de concurrencia.»

tú puedes preguntarle:

> «¿Dónde está la condición de carrera? ¿Qué recurso compartido está provocándola? ¿Podemos solucionarlo con sincronización o deberíamos cambiar el diseño?»

Eso es muchísimo más valioso que saber escribir un `pthread_mutex_lock()` de memoria.

---

# 3. Lo mismo con redes

No necesitas memorizar 200 comandos de Cisco.

Pero sí entender realmente:

**Aplicación → TCP/UDP → IP → Ethernet/Wi-Fi → hardware**

Y conceptos como:

* DNS
* HTTP/HTTPS
* TCP
* UDP
* TLS
* NAT
* routing
* subredes
* firewalls
* balanceadores
* proxies
* CDN
* latencia
* ancho de banda

Porque si una IA te genera una arquitectura:

```text
Frontend
   ↓
Cloudflare
   ↓
Load Balancer
   ↓
API
   ↓
Redis
   ↓
PostgreSQL
```

quieres ser capaz de mirar eso y decir:

> «Espera. ¿Por qué necesitamos Redis aquí? ¿Qué problema estamos solucionando? ¿Qué ocurre si Redis deja de estar disponible?»

Eso es conocimiento que **no se vuelve obsoleto porque una IA escriba código más rápido**.

---

# 4. Hay una categoría de conocimiento que yo priorizaría muchísimo

## Arquitectura y diseño de sistemas

Creo que esto puede encajarte especialmente bien.

Aprendería cosas como:

* monolitos
* microservicios
* event-driven architecture
* REST
* GraphQL
* colas
* sistemas distribuidos
* cachés
* bases de datos
* replicación
* particionamiento
* consistencia
* disponibilidad
* escalabilidad
* observabilidad
* tolerancia a fallos

Y empezaría a hacer preguntas como:

> «¿Cómo diseñaría YouTube?»

> «¿Cómo diseñaría WhatsApp?»

> «¿Cómo diseñaría Uber?»

> «¿Cómo diseñaría un sistema de reservas?»

> «¿Cómo diseñaría una plataforma de IA que recibe millones de peticiones?»

No necesitas escribir 20.000 líneas de código.

Necesitas ser capaz de **dibujar la arquitectura y justificar cada decisión**.

Y entonces utilizar la IA para implementar cada componente.

---

# 5. Las bases de datos son otro campo que no abandonaría

Aquí también hay muchísimo conocimiento transferible.

Aprendería de verdad:

* modelo relacional
* normalización
* índices
* B-trees
* transacciones
* ACID
* aislamiento
* locks
* concurrencia
* SQL
* query planning
* replicación
* sharding
* NoSQL
* bases de datos distribuidas

No necesitas convertirte en una persona que escribe SQL a 150 palabras por minuto.

Pero sí quiero que puedas mirar una consulta y pensar:

> «Esto probablemente está haciendo un full table scan.»

Y después pedirle a la IA:

> «Analiza el plan de ejecución y dime por qué ocurre. Propón tres soluciones y explícame las ventajas e inconvenientes de cada una.»

Ahí estás utilizando la IA como **multiplicador de conocimiento**, no como sustituto de tu cerebro.

---

# 6. Y hay algo que considero incluso más importante: aprender a pensar

Esto es probablemente lo que más intentaría desarrollar durante la carrera.

### Abstracción

Ser capaz de eliminar detalles irrelevantes y quedarte con el problema esencial.

### Descomposición

Convertir:

> «Quiero construir Netflix»

en:

> autenticación → catálogo → almacenamiento → streaming → CDN → recomendaciones → pagos → observabilidad...

### Modelización

Representar problemas mediante:

* modelos
* diagramas
* estructuras
* relaciones
* estados

### Razonamiento

Entender:

> Si modifico X, ¿qué consecuencias tiene sobre Y?

### Trade-offs

Aceptar que en ingeniería normalmente **no existe una solución perfecta**.

Hay soluciones:

* más rápidas
* más baratas
* más simples
* más escalables
* más seguras
* más mantenibles

Y tienes que elegir.

---

# 7. ¿Y entonces qué hago con la programación?

Aquí creo que puedes encontrar un término medio.

**No abandonaría la programación.**

Pero tampoco intentaría competir con Claude, GPT, Codex, etc. escribiendo código manualmente.

Aprendería a programar lo suficiente como para:

1. leer código;
2. entenderlo;
3. modificarlo;
4. depurarlo;
5. diseñar soluciones;
6. escribir pequeñas piezas sin ayuda;
7. reconocer código incorrecto;
8. utilizar una IA para generar grandes cantidades de código.

Es una diferencia enorme.

---

# 8. De hecho, te propongo una regla

Cuando estés aprendiendo algo nuevo:

### Primera fase → tú

Intenta resolver el problema.

No necesariamente escribiendo 500 líneas.

Piensa.

Haz un esquema.

Formula una solución.

Predice qué debería ocurrir.

---

### Segunda fase → IA

Ahora sí:

> «Esta es mi solución. Revísala. No me des directamente el código. Busca errores conceptuales.»

Esto es **muchísimo mejor para aprender** que:

> «Hazme este ejercicio.»

---

### Tercera fase → IA como profesor

Puedes decirle:

> «Hazme preguntas para comprobar si realmente entiendo esto. No me des las respuestas hasta que haya respondido.»

Y aquí la IA es brutal.

Puedes tener literalmente un profesor particular disponible permanentemente.

---

### Cuarta fase → IA como programador

Una vez que entiendes el problema:

> «Implementa esta arquitectura siguiendo estas restricciones.»

Y entonces revisas lo que produce.

---

# 9. Hay una técnica que creo que te vendría especialmente bien

Cuando aprendas algo, intenta poder responder a estas cinco preguntas:

### 1. ¿Qué problema resuelve?

### 2. ¿Cómo funciona internamente?

### 3. ¿Por qué funciona?

### 4. ¿Cuándo utilizaría esto?

### 5. ¿Cuándo NO lo utilizaría?

Si puedes responder esas cinco preguntas, probablemente has aprendido algo de verdad.

Por ejemplo, **Redis**.

No quiero que simplemente sepas:

> «Redis es una base de datos en memoria.»

Quiero que puedas explicar:

> «Redis almacena datos principalmente en memoria, por lo que proporciona latencias muy bajas. Puede utilizarse como caché para evitar acceder continuamente a una base de datos más lenta. Sin embargo, introducirlo aumenta la complejidad del sistema y plantea problemas de consistencia, invalidación y disponibilidad.»

Eso es conocimiento.

---

# 10. Y aquí viene algo que creo que te puede solucionar bastante la frustración

## Deja de utilizar la IA únicamente para producir.

Utilízala para **hacerte pensar**.

Hay una diferencia gigantesca entre:

> «Explícame Kubernetes.»

y:

> «Quiero aprender Kubernetes. Hazme preguntas progresivas para descubrir qué conocimientos me faltan. No me expliques nada hasta que intente responder.»

O:

> «Explícame qué ocurre cuando escribo google.com en el navegador. Quiero intentar reconstruir el proceso yo primero. Hazme preguntas para guiarme.»

Eso convierte la IA en un **entrenador intelectual**.

---

# 11. Cómo estudiaría yo Ingeniería Informática actualmente

Si estuviera en tu situación, organizaría mi aprendizaje aproximadamente así:

| Área                              | Prioridad |
| --------------------------------- | --------- |
| Fundamentos de informática        | ⭐⭐⭐⭐⭐     |
| Sistemas operativos               | ⭐⭐⭐⭐⭐     |
| Redes                             | ⭐⭐⭐⭐⭐     |
| Bases de datos                    | ⭐⭐⭐⭐⭐     |
| Arquitectura de software          | ⭐⭐⭐⭐⭐     |
| Sistemas distribuidos             | ⭐⭐⭐⭐⭐     |
| Algoritmos y estructuras de datos | ⭐⭐⭐⭐      |
| Seguridad                         | ⭐⭐⭐⭐      |
| Cloud / DevOps                    | ⭐⭐⭐⭐      |
| IA / ML                           | ⭐⭐⭐⭐      |
| Programación                      | ⭐⭐⭐⭐      |
| Frameworks concretos              | ⭐⭐        |
| Sintaxis de lenguajes             | ⭐⭐        |
| Memorizar APIs                    | ⭐         |

Y hay una idea importante:

**Los fundamentos tienen una vida útil enorme.**

React cambia.

Angular cambia.

Laravel cambia.

Docker cambia.

AWS cambia.

Los modelos de IA cambian.

Pero:

> memoria → CPU → procesos → redes → bases de datos → algoritmos → sistemas distribuidos

siguen siendo fundamentalmente los mismos problemas.

---

# 12. Y yo añadiría una cosa que probablemente no te están enseñando suficiente en la universidad

## Aprende a construir sistemas completos.

No 25 tutoriales.

Construye **5 o 6 proyectos realmente buenos**.

Por ejemplo:

### Proyecto 1 — Aplicación web

Frontend + backend + PostgreSQL + autenticación + Docker.

---

### Proyecto 2 — Sistema distribuido

API + cola de mensajes + Redis + PostgreSQL + varios servicios.

---

### Proyecto 3 — Sistema de tiempo real

WebSockets + backend + base de datos.

---

### Proyecto 4 — Infraestructura

Aplicación desplegada en Linux con:

* Docker
* reverse proxy
* HTTPS
* logs
* métricas
* backups

---

### Proyecto 5 — IA

Una aplicación que utilice un LLM.

Pero no simplemente:

> «ChatGPT clone».

Algo donde tengas que resolver problemas reales de:

* RAG
* embeddings
* búsqueda
* evaluación
* contexto
* costes
* latencia
* seguridad
* agentes
* herramientas

---

Y **en todos ellos utilizaría IA intensivamente**.

No ocultaría que has utilizado IA.

Al contrario.

Tu objetivo sería demostrar:

> «Puedo dirigir a una IA para construir un sistema complejo y entiendo perfectamente lo que está haciendo.»

Eso me parece una habilidad profesional extraordinariamente valiosa.

---

# 13. Tu carrera podría acabar siendo bastante diferente de «programador»

Y esto creo que es importante para ti.

Estás estudiando Ingeniería Informática, pero no tienes por qué terminar siendo:

> **Software Engineer que escribe código 8 horas al día.**

Puedes acabar en:

* arquitectura de software
* cloud
* DevOps / platform engineering
* ciberseguridad
* sistemas distribuidos
* datos
* IA/ML
* ingeniería de IA
* infraestructura
* producto tecnológico
* soluciones empresariales
* consultoría tecnológica
* investigación
* ingeniería de sistemas
* gestión técnica

Y muchas de esas profesiones van a necesitar **más comprensión técnica**, no menos.

---

# 14. De hecho, creo que deberías cambiar tu definición de «ser bueno en informática»

Antes:

> **Buen informático = escribe mucho código y muy rápido.**

Ahora:

> **Buen informático = entiende problemas complejos y es capaz de construir soluciones fiables utilizando las herramientas disponibles.**

Y en 2026 una de esas herramientas es una IA extremadamente potente.

Es exactamente igual que un ingeniero civil no compita con una calculadora haciendo multiplicaciones.

**La calculadora no eliminó la ingeniería.**

El profesional dejó de dedicar su cerebro a hacer cálculos mecánicos y empezó a dedicarlo a cosas de mayor nivel.

Con la IA está ocurriendo algo parecido, solo que a una escala muchísimo mayor.

---

# 15. Si estuviera en tu lugar, haría esto durante los próximos meses

No intentaría aprender 15 tecnologías nuevas.

Haría un ciclo:

**Fundamento → problema → proyecto → IA → explicación → revisión.**

Por ejemplo:

### Semana: sistemas operativos

Aprendes procesos, memoria y concurrencia.

Después:

**Problema:**

> «Quiero entender por qué Docker puede ejecutar procesos aislados.»

Investigas namespaces, cgroups, procesos, filesystem, etc.

Después utilizas IA para construir un pequeño experimento.

Y finalmente intentas explicarlo **sin IA**.

Si puedes explicarlo con tus propias palabras, has aprendido.

---

# Y una última cosa

Creo que tu frustración contiene una conclusión muy buena, aunque ahora mismo te esté haciendo sentir perdido:

**te has dado cuenta de que escribir código por escribir código ya no te resulta intelectualmente estimulante.**

Eso no significa necesariamente que no te guste la informática.

Puede significar que estás empezando a interesarte más por **lo que hay detrás del código**.

Y, sinceramente, si estás estudiando Ingeniería Informática, yo aprovecharía esa intuición.

No intentaría convertirme en un programador que compite con la IA.

Intentaría convertirme en **el ingeniero que sabe qué hay que construir, por qué hay que construirlo así, cómo comprobar que funciona y cómo conseguir que diez agentes de IA lo construyan correctamente**.

Eso es una posición muchísimo más interesante.

Y además hay una paradoja bastante bonita: **cuanto más potente se vuelve la IA programando, más valor tiene entender informática de verdad**, porque aumenta la cantidad de código que puedes producir, pero no aumenta automáticamente tu capacidad para saber si ese código debería existir.
