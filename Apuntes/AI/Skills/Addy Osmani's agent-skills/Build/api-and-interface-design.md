---
alias:
  - Skill api-and-interface-design
  - Addy Osmani API and Interface Design
  - Hyrum's Law
tags:
  - ai/skills
  - addy-osmani
  - ai/build
  - dev/architecture
  - dev/design
source: https://github.com/addyosmani/agent-skills/blob/main/skills/api-and-interface-design/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Diseña **interfaces estables y bien documentadas, difíciles de usar mal**. Las buenas interfaces hacen fácil lo correcto y difícil lo incorrecto. Aplica a APIs REST, esquemas GraphQL, límites de módulo, props de componentes y cualquier superficie donde una pieza de código habla con otra.

---

## 🎯 ¿Cuándo usarla?

Diseñando endpoints nuevos · definiendo límites de módulo o contratos entre equipos · creando interfaces de props de componentes · estableciendo el esquema de BD que informa la forma de la API · **cambiando interfaces públicas existentes**.

---

## 📜 Principio 1 — Hyrum's Law

> *Con un número suficiente de usuarios de una API, todos los comportamientos observables de tu sistema serán relied upon por alguien, sin importar qué prometas en el contrato.*

Incluye **bugs, rarezas de timing, texto de mensajes de error y orden**. **Implicaciones de diseño:**
- **Sé intencionado sobre qué expones.** Cada comportamiento observable es un compromiso potencial.
- **No filtres detalles de implementación.** Si los usuarios pueden observarlo, dependerán de él.
- **Planifica la deprecación en tiempo de diseño.**
- **Los tests no bastan.** Aunque tengas contract tests perfectos, cambios "seguros" pueden romper a usuarios reales que dependían de comportamiento no documentado.

---

## 📜 Principio 2 — The One-Version Rule

Evita forzar a los consumidores a elegir entre múltiples versiones de la misma dependencia o API. Los **problemas de dependencia en diamante** aparecen cuando distintos consumidores necesitan versiones distintas de lo mismo. **Diseña para un mundo donde solo existe una versión a la vez: extiende, no fork-eas.**

---

## 🔑 Los 6 principios de diseño

**1. Contrato primero.** Define la interfaz *antes* de implementarla. El contrato **es** la spec; la implementación la sigue. Documenta la semántica en los comentarios del propio tipo (`// Idempotent delete — succeeds even if already deleted`).

**2. Semántica de error consistente.** Elige **una** estrategia y úsala en todas partes: códigos HTTP + cuerpo de error estructurado `{ error: { code, message, details? } }`, con un mapeo fijo (400 inválido · 401 no autenticado · 403 autenticado pero no autorizado · 404 no encontrado · 409 conflicto · 422 validación fallida · 500 error de servidor *nunca exponiendo detalles internos*). **No mezcles patrones**: si algunos endpoints lanzan, otros devuelven `null` y otros `{ error }`, el consumidor no puede predecir el comportamiento.

**3. Valida en las fronteras.** Confía en el código interno; valida en los bordes del sistema donde entra input externo: route handlers de API · handlers de submit de formularios · **parsing de respuestas de servicios externos (SIEMPRE no confiable)** · carga de variables de entorno. **Donde la validación NO pertenece:** entre funciones internas que comparten contratos de tipo, en utilidades llamadas por código ya validado, o sobre datos que acaban de venir de tu propia base de datos.

**4. Prefiere adición sobre modificación.** Extiende interfaces sin romper consumidores existentes: **añade campos opcionales**, nunca cambies tipos ni elimines campos.

**5. Nomenclatura predecible.** REST en plurales y sin verbos (`GET /api/tasks`) · query params y campos de respuesta en `camelCase` · booleanos con prefijo `is`/`has`/`can` · valores de enum en `UPPER_SNAKE`.

**6. Honra la clave de idempotencia.** *(la sección más profunda de la skill)*

---

## 🔑 La idempotencia bien hecha

> [!warning] Aceptar el header **es** el contrato. Honrarlo es la implementación — y ahí es donde se pierde el dinero
> Una clave que el servidor acepta pero maneja con descuido es **peor que no tener clave**, porque el cliente ahora cree que reintentar es seguro.

**Deriva la clave de la intención, no del intento.** Debe ser estable entre reintentos de una misma intención y distinta entre intenciones distintas:
```typescript
crypto.randomUUID()              // ✗ clave nueva por intento — cada reintento es un cargo nuevo
`${userId}:${amount}`            // ✗ dos cargos legítimos de 50 € colapsan en uno
`${orderId}:${Date.now()}`       // ✗ un timestamp es randomUUID() con sombrero

req.headers['idempotency-key']   // ✓ el cliente la genera una vez y la reutiliza
`charge:v1:${orderId}`           // ✓ derivada de un identificador inmutable
```

**Reclama atómicamente. Un check seguido de un act es una carrera (TOCTOU):** deja que la **restricción de unicidad** elija al ganador (insert con estado `in_progress` + hash del request; si viola unicidad, replay o rechazo). *Una restricción única **es** el mecanismo: un almacén que no puede forzar unicidad en una operación no puede sostener esto.*

**Guarda el payload.** Misma clave con cuerpo diferente es un bug del cliente y **debe fallar ruidosamente** (422) en vez de devolver la primera respuesta.

**Decide qué recibe un duplicado en vuelo.** La primera petición sigue corriendo cuando llega la segunda — el caso común en tormentas de reintentos:

| Estrategia | Respuesta | Cuándo |
| :--- | :--- | :--- |
| **Rechazar** | `409 Conflict` | El cliente puede reintentar luego; lo más simple y seguro |
| **Esperar** | Bloquear por el resultado, con límite | El llamador lo necesita de forma síncrona |
| **Devolver pendiente** | `202` + URL de estado | Efectos de larga duración |

*Nunca dejes pasar al segundo llamador porque el primero "parece atascado". Un intento estancado cuyo destino se desconoce es justo cuando duplicar cuesta más.*

> [!important] Toda llamada tiene tres resultados, no dos: éxito, fallo y **desconocido**
> Un timeout no te dice si el efecto se aplicó. **Registra la intención *antes* de llamar**, para que un crash entre la llamada y la respuesta deje evidencia de algo que debe resolverse después — en vez de un cargo silenciosamente reintentado.

**Fija la retención desde la cadena de reintentos más larga**, no desde el coste en disco. Una TTL de 24 h detrás de una DLQ de 7 días es una duplicación esperando a ocurrir.

---

## 🧩 Patrones de tipos TypeScript

- **Uniones discriminadas para variantes:** cada estado explícito con sus campos; el consumidor obtiene *narrowing* automático.
- **Separación input/output:** `CreateTaskInput` (lo que da el llamador) vs. `Task` (lo que devuelve el sistema, con campos generados por el servidor).
- **Tipos branded para IDs:** `type TaskId = string & { readonly __brand: 'TaskId' }` — impide pasar un `UserId` donde se espera `TaskId`.

## 🌐 Patrones REST
Diseño de recursos (`GET/POST /api/tasks`, `GET/PATCH/DELETE /api/tasks/:id`, sub-recursos) · **paginación** en todos los endpoints de lista · filtrado por query params · **actualizaciones parciales (PATCH)** que solo cambian lo proporcionado.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Documentaremos la API luego" | **Los tipos SON la documentación.** Defínelos primero |
| "No necesitamos paginación aún" | La necesitarás en cuanto alguien tenga 100+ items. Añádela desde el inicio |
| "PATCH es complicado, usemos PUT" | PUT exige el objeto completo cada vez. PATCH es lo que los clientes realmente quieren |
| "Versionaremos la API cuando haga falta" | Los cambios sin versionar rompen consumidores. Diseña para extensión desde el principio |
| "Nadie usa ese comportamiento no documentado" | Hyrum's Law: si es observable, alguien depende de él |
| "Aceptar el header `Idempotency-Key` es suficiente" | El header es el contrato; almacenarlo contra el resultado es la implementación |
| "Nuestra cola garantiza entrega exactly-once" | Ninguna cola lo hace entre un crash del consumidor — el ack del broker y tu efecto lateral no están en una transacción. **Diseña para at-least-once con procesamiento idempotente** |
| "Las peticiones duplicadas son raras" | Son **correlacionadas**. Los reintentos se disparan exactamente cuando una dependencia está degradada — el momento en que son más probables y más caras |

---

## 🔗 Relaciones

**En el flujo de Addy:** define los contratos que consume [[incremental-implementation]] (patrón *Contract-First Slicing*) y [[frontend-ui-engineering]]. Sus límites y validaciones son los *Boundaries* de [[security-and-hardening]]. La deprecación se gestiona en [[deprecation-and-migration]].

**Equivalencia Matt Pocock:** es la pieza más técnica del pack y **no tiene equivalente** en el de Matt. Lo más cercano es [[codebase-design]] (diseño de módulos profundos y fácil de navegar), pero allí el foco es **estructura y vocabulary**; aquí es **contratos, semántica de error, versionado y las trampas concretas de idempotencia**.

```bash
npx skills add addyosmani/agent-skills --skill api-and-interface-design
```
