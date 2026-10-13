---
alias:
  - Skill deprecation-and-migration
  - Addy Osmani Deprecation and Migration
  - Expand Contract
  - Strangler Pattern
tags:
  - ai/skills
  - addy-osmani
  - ai/ship
  - dev/architecture
  - dev/refactoring
source: https://github.com/addyosmani/agent-skills/blob/main/skills/deprecation-and-migration/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **El código es un pasivo, no un activo.** La mayoría de las organizaciones son buenas construyendo cosas; **pocas son buenas quitándolas**. Esta skill cubre la disciplina de **retirar el código que ya no se gana su mantenimiento**, y de **mover a los usuarios con seguridad** del viejo al nuevo.

---

## 🎯 ¿Cuándo usarla?

Reemplazando un sistema, API o librería antigua · retirando una feature que ya no se necesita · consolidando implementaciones duplicadas · eliminando código muerto que nadie posee pero del que todo el mundo depende · **planificando el ciclo de vida de un sistema nuevo (la planificación de deprecación empieza en tiempo de diseño)** · decidiendo si mantener un sistema legacy o invertir en migrarlo.

---

## 📐 Los principios

**El código es un pasivo.** Cada línea tiene coste continuo: tests, documentación, parches de seguridad, actualizaciones de dependencias y sobrecarga mental. **El valor del código es la funcionalidad que aporta, no el código en sí.**

**Hyrum's Law hace la eliminación difícil.** Con suficientes usuarios, todo comportamiento observable delega —incluidos bugs y rarezas de timing— así que la deprecación requiere **migración activa**, no solo anuncio.

**La planificación de deprecación empieza en tiempo de diseño.** Al construir algo nuevo, pregunta: *"¿cómo lo quitaría esto dentro de 3 años?"* Los sistemas con interfaces limpias, feature flags y superficie mínima son más fáciles de deprecar que los que filtran detalles de implementación por todas partes.

---

## 🤔 La decisión de deprecar

Antes de deprecar nada, responde:
1. ¿Este sistema **sigue aportando valor único**? → Sí: mantenlo. No: sigue.
2. **¿Cuántos usuarios/consumidores dependen de él?** → Cuantifica el alcance de la migración.
3. **¿Existe un reemplazo?** → **No: construye el reemplazo primero. No deprecas sin alternativa.**
4. **¿Cuál es el coste de migración por consumidor?** → Si es trivialmente automatizable, migra. Si es manual y costoso, sopésalo contra el coste de mantenimiento.
5. **¿Cuál es el coste continuo de NO deprecar?** → Riesgo de seguridad, tiempo de ingenieros, coste de oportunidad de la complejidad.

### Compulsory vs. Advisory

| Tipo | Cuándo | Mecanismo |
| :--- | :--- | :--- |
| **Advisory** | La migración es opcional, el sistema viejo es estable | Warnings, documentación, nudges. Los usuarios migran a su ritmo |
| **Compulsory** | El sistema viejo tiene problemas de seguridad, bloquea el progreso, o su coste de mantenimiento es insostenible | **Fecha límite dura.** Se remove en la fecha X. **Proporciona herramientas de migración** |

> [!warning] Por defecto, advisory
> Usa compulsory solo cuando el coste o el riesgo justifique forzar la migración. **La deprecación compulsory exige tooling de migración, documentación y soporte** — no puedes simplemente anunciar una fecha límite.

---

## 🔄 El proceso de migración

**1. Construye el reemplazo.**
**2. Anuncia y documenta** — con un aviso de deprecación, una **guía de migración** y las **métricas de adopción** que vas a seguir.
**3. Migra incrementalmente** — el proceso largo.
**4. Retira el sistema viejo** — solo cuando las métricas lo permitan.

> [!important] La Regla del Churn
> **Los usuarios no migrarán por su cuenta.** Proporciona tooling, documentación e incentivos — **o haz la migración tú**. Asumir que migrarán solos es la razón número uno de por qué los sistemas viejos nunca mueren.

### Patrones de migración

| Patrón | Cómo | Cuándo |
| :--- | :--- | :--- |
| **Strangler** | Envuelve el sistema viejo y dirige tráfico progresivamente al nuevo | Migrar un sistema completo sin parar el servicio |
| **Adapter** | Una capa fina traduce entre viejo y nuevo, ambos siguen vivos | Necesitas el viejo y el nuevo coexistiendo durante un tiempo |
| **Feature Flag Migration** | Enruta por flag, luego retira el código viejo | Cutover arriesgoso, o cuando quieras que migrar y revertir sea trivial |

---

## 🗄️ Migraciones de esquema de base de datos (Expand/Contract)

> [!danger] Esta es la migración más arriesgada, porque los datos son lo único que **no puedes revertir revirtiendo un deploy**
> El modo de fallo es **acoplar el cambio de esquema al cambio de código**: renombra una columna en la misma release que empieza a usar el nombre nuevo, y durante la ventana de rollout —cuando código viejo y nuevo corren a la vez— **uno de ellos consultará una columna que no existe**. La solución: **nunca cambies una columna in situ.** Migra en fases aditivas para que código viejo y nuevo sean **ambos válidos en cada paso**.

```
EXPAND ──────────→ MIGRATE ──────────→ CONTRACT
añade la columna   rellena las filas    cuando nada lee la
nueva, nullable,   existentes,         columna vieja, la
junto a la vieja   dual-write viejo+nuevo  borras en un deploy
```

**Ejemplo trabajado — renombrar `name` a `full_name`:**
1. **Expand.** Añade `full_name` como nullable. Deploy. *(El código viejo la ignora; nada se rompe.)*
2. **Dual-write.** La app escribe ambas en cada insert/update. Deploy.
3. **Backfill.** Copia `name → full_name` para filas existentes, **por lotes**, para no bloquear la tabla.
4. **Switch reads.** Apunta la app a `full_name`, **sigue escribiendo ambas**. Deploy y bakea.
5. **Contract.** Deja de escribir `name` y —**en un deploy separado y posterior**— borra la columna.

Cada paso es **desplegable y reversible independientemente**: si el paso 4 se comporta mal, reviertes el código y `full_name` sigue poblándose. **Trata cada fase como una rebanada vertical fina** — ver [[incremental-implementation]].

**Reglas:**
- **Aditivo primero, destructivo al final y solo.** Los adds (columna nueva nullable, tabla nueva, índice nuevo) son seguros en cualquier deploy; **los drops y renombres gets su propio deploy, después de que nada referencie la forma vieja.**
- **Toda migración tiene un camino `down` probado.** Una migración que no puedes revertir es un deploy que no puedes deshacer. **Escribe y ejecuta el `down` antes de mergear.**
- **Backfill por lotes, fuera del hot path.** Un `UPDATE` único sobre millones de filas bloquea la tabla; trocéalo y estrangúlalo.
- **Construye índices grandes sin bloquear escrituras** (p. ej. `CREATE INDEX CONCURRENTLY` en Postgres).
- **Desacopla del código con feature flag** cuando el cutover es arriesgado.

---

## 🧟 El código zombi

**Código zombi = código que nadie posee del que todo el mundo depende.** No se mantiene activamente, no tiene dueño claro, y acumula vulnerabilidades. Señales: sin commits en 6+ meses pero con consumidores activos · sin mantenedor asignado · tests fallando que nadie arregla · dependencias con vulnerabilidades conocidas sin actualizar · documentación que referencia sistemas que ya no existen.

**Respuesta: o le asignas un dueño y lo mantienes bien, o lo deprecas con un plan de migración concreto. El código zombi no puede quedarse en el limbo** — o recibe inversión o se elimina.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Todavía funciona, ¿por qué quitarlo?" | El código que funciona y nadie mantiene acumula deuda de seguridad y complejidad |
| "Alguien podría necesitarlo luego" | Si hace falta más adelante, se puede reconstruir. Mantener código sin usar "por si acaso" **cuesta más que reconstruirlo** |
| "La migración es demasiado cara" | Compara el coste de migrar con el de mantener **durante 2–3 años**. Migrar suele ser más barato a largo plazo |
| "Deprecaremos cuando acabemos el sistema nuevo" | La planificación empieza en tiempo de diseño. Cuando el sistema nuevo esté listo tendrás nuevas prioridades |
| "Los usuarios migrarán por su cuenta" | No lo harán. Proporciona tooling e incentivos, o migra tú (la Regla del Churn) |
| "Podemos mantener ambos sistemas indefinidamente" | Dos sistemas haciendo lo mismo es el doble de mantenimiento, testing, documentación y onboarding |
| "Solo es renombrar la columna, una línea" | Durante el rollout, código viejo y nuevo corren juntos — uno consultará una columna que ya no existe. **Expand/contract, nunca renombrar in situ** |
| "Escribiré el rollback si hace falta" | Una migración sin camino `down` es un deploy irreversible. **Escríbelo y ejecútalo antes de mergear** |

---

## 🔗 Relaciones

**En el flujo de Addy:** el paso "proteger a los consumidores" de [[api-and-interface-design]] (Hyrum's Law). Sus fases expand/contract son rebanadas de [[incremental-implementation]]. El versioning de [[git-workflow-and-versioning]] define los bumps, y el flag lifecycle lo gestiona [[shipping-and-launch]].

**Equivalencia Matt Pocock:** **no tiene equivalente.** El pack de Matt no cubre retirada ni migración de sistemas — otraLaguna en su cobertura post-Build.

```bash
npx skills add addyosmani/agent-skills --skill deprecation-and-migration
```
