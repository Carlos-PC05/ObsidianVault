---
alias:
  - Skill observability-and-instrumentation
  - RED Metrics
  - Addy Osmani Observability
tags:
  - ai/skills
  - addy-osmani
  - ai/ship
  - dev/monitoring
  - dev/architecture
source: https://github.com/addyosmani/agent-skills/blob/main/skills/observability-and-instrumentation/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Instrumenta el código para que **el comportamiento en producción sea visible y diagnosticable**. **Código que no puedes observar es código que no puedes operar.** La instrumentación no es un añadido post-lanzamiento — **se escribe junto a la feature, igual que los tests**. Si una feature se entrega sin telemetría, el primer bug reportado por un usuario se convierte en **arqueología en vez de una query**.

---

## 🎯 ¿Cuándo usarla?

Construyendo cualquier feature que vaya a correr en producción · añadiendo un servicio, endpoint, job en background o integración externa · un incidente de producción tardó demasiado en diagnosticarse (*"no pudimos saber qué pasó"*) · montando o revisando reglas de alerta · revisando un PR que añade I/O, reintentos, colas o llamadas entre servicios.

**NO para:**
- Diagnosticar un fallo que está pasando **ahora** → usa [[debugging-and-error-recovery]] (la observabilidad es lo que hará esa skill rápida la próxima vez).
- Perfilado y optimización de lentitud medida → usa [[performance-optimization]].
- Checklists de monitoring del día del lanzamiento y triggers de rollback → ver [[shipping-and-launch]].

---

## 🔄 El proceso

**1. Define "funciona" antes de instrumentar.** Antes de añadir telemetría, responde: ¿qué preguntas de on-call necesita hacer este servicio? (*"¿Los usuarios estánKdFubo viendo errores?"* · *"¿Qué es lo más lento?"* · *"¿Estamos cerca del límite?"*) La telemetría que no responde a una pregunta concreta es ruido.

**2. Elige la señal correcta para cada pregunta.**

| Pregunta | Señal |
| :--- | :--- |
| *"¿Ocurrió algo específico y cuándo?"* | **Logging estructurado** |
| *"¿Cómo va el sistema en agregado?"* | **Métricas** |
| *"¿Dónde se fue el tiempo en esta petición?"* | **Tracing distribuido** |
| *"¿Algo requiere intervención humana?"* | **Alertas** |

**3. Logging estructurado.** Campos clave/valor, **nunca texto libre concatenado** — es lo que hace un log filtrable y correlacionable. Incluye siempre: timestamp, nivel, mensaje, **correlation/trace id**, servicio, y contexto específico. **Nunca** secrets, tokens ni PII en los logs (mismo principio de minimización de datos de [[security-and-hardening]]).

**4. Métricas — el modelo RED.** Para servicios que responden peticiones: **Rate** (tasa de peticiones) · **Errors** (tasas de error) · **Duration** (latencia). Son las tres que te dicen si un usuario está teniendo problemas. ParaSaturation y trabajo encolado, añade colas y utilisation. Cuida la **cardinalidad**: un ID de usuario como etiqueta de métrica **tumba tu backend de métricas** — las búsquedas de alta cardinalidad van en **logs y traces**.

**5. Tracing distribuido.** Con OpenTelemetry o equivalente, para responder preguntas de latencia entre servicios que los logs solos no pueden. **La auto-instrumentación hace que el coste sea trivial** — incluso para dos servicios ya tienes preguntas de latencia entre servicios que los logs no responden.

**6. Alerting.** Alerta por **síntoma**, no por causa. Pregunta siempre *"si esta alerta salta a las 3 AM, ¿la personaacted de verdad quería despertarse?"* Un pager ruidoso entrena a la gente a ignorarlo — **y el tuning nunca llega; el incidente real que se pierde sí.** Escribe **runbooks** para cada alerta: qué significa, qué comprobar, qué hacer. Una alerta sin runbook es ruido con volumen.

**7. Verifica la telemetría en sí misma.** La telemetría no verificada no es evidencia: **métrica con cardinalidad distinta a la esperada, traza que no aparece, alerta que nunca dispara**. Instrumenta el pipeline de instrumentación.

---

## 🔗 La conexión con el resto del pack

> [!important] Un ciclo que se retroalimenta
> - [[observability-and-instrumentation]] **instrumenta** → [[debugging-and-error-recovery]] **diagnostica** con datos → [[performance-optimization]] **optimiza** con baselines reales → [[shipping-and-launch]] **usa las alertas** como triggers de rollback → y vuelta a empezar.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Añadiré logging cuando funcione" | *"Después"* se convierte en *"después del primer incidente"*, que es el momento más caro de descubrir que estás ciego |
| "Más logs = más observabilidad" | El ruido no estructurado hace los incidentes **más lentos**, no más rápidos. Tres eventos consultables baten a trescientas líneas de prosa |
| "`console.log` vale por ahora" | La salida no estructurada no se puede filtrar, correlacionar ni alertar. El logger estructurado cuesta 5 minutos una vez |
| "Ya miraremos los dashboards cuando algo se rompa" | Los dashboards construidos sin preguntas definidas te muestran todo **excepto** la respuesta. Empieza por las preguntas de on-call |
| "Alertamos de todo lo importante, afinamos luego" | Un pager ruidoso entrena a ignorar. El tuning no ocurre; el incidente real que se pierde, sí |
| "Poner el user ID como etiqueta de métrica facilita el debug" | También tumba tu backend de métricas. Las búsquedas de alta cardinalidad van en logs y traces |
| "Tracing es overkill para nuestros dos servicios" | **Dos servicios ya significan** preguntas de latencia entre servicios que los logs no responden. Y la auto-instrumentación hace el coste trivial |

---

## 🚩 Red Flags

- Ninguna telemetría accompanies a una feature nueva.
- Logs en texto libre concatenado en vez de estructurados.
- Secrets, tokens o PII en los logs.
- Métricas con cardinalidad desbocada (IDs como etiquetas).
- Alertas por causa en vez de por síntoma.
- Alertas sin runbook.
- Ninguna verificación de que la propia telemetría funciona.
- Dashboard sin preguntas de on-call definidas detrás.

---

## 🔗 Relaciones

**En el flujo de Addy:** fase SHIP. Corre **en paralelo con Build**, no después — la skill `using-agent-skills` la coloca explícitamente junto a [[incremental-implementation]] en la secuencia. Alimenta [[shipping-and-launch]] con sus alertas y umbrales.

**Equivalencia Matt Pocock:** **no tiene equivalente.** Matt no cubre observabilidad ni instrumentación — otra pieza de la cobertura *amplia* de Addy.

```bash
npx skills add addyosmani/agent-skills --skill observability-and-instrumentation
```
