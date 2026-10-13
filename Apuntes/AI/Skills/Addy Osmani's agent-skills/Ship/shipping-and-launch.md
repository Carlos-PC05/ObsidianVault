---
alias:
  - Skill shipping-and-launch
  - Addy Osmani Shipping and Launch
  - Error Budget
  - Staged Rollout
tags:
  - ai/skills
  - addy-osmani
  - ai/ship
  - dev/workflow
  - dev/monitoring
source: https://github.com/addyosmani/agent-skills/blob/main/skills/shipping-and-launch/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Despliega con confianza.** El objetivo no es solo desplegar — es desplegar **con monitoring en sitio, un plan de rollback listo y una idea clara de cómo se ve el éxito**. Todo lanzamiento debe ser **reversible, observable e incremental**.

> [!important] `/ship` es el comando más especial del pack
> Lanza las **cuatro *personas* de revisión en paralelo** — `code-reviewer`, `security-auditor`, `test-engineer`, `web-performance-auditor` — y luego **fusiona sus hallazgos en un veredicto go/no-go**. Es el único punto donde el framework usa su propia infraestructura multi-agente.

---

## 🎯 ¿Cuándo usarla?

Desplegando una feature a producción por primera vez · liberando un cambio significativo a usuarios · migrando datos o infraestructura · abriendo un programa beta o early access · **cualquier despliegue que conlleve riesgo (todos)**.

---

## ✅ La checklist pre-lanzamiento

| Área | Qué comprobar |
| :--- | :--- |
| **Calidad de código** | Tests pasan · build limpio · sin TODOs ni stubs pendientes · sin cambios de debug |
| **Seguridad** | Sin secretos en código ni config · dependencias sin vulnerabilidades críticas · auth/authz en endpoints nuevos · sin datos sensibles en logs |
| **Performance** | Presupuesto de bundle respetado · Core Web Vitals dentro de umbral · sin queries N+1 en el camino crítico |
| **Accesibilidad** | Teclado navegable · labels ARIA · contraste verificado · estados vacíos y de error |
| **Infraestructura** | Migraciones de DB reversible · feature flags con default **seguro** · variables de entorno configuradas |
| **Documentación** | Changelog actualizado · notas de deprecación si aplica · monitorización y alertas en sitio |

**Detalle de cada bloque:** en *calidad*, verifica que la suite **completa** pasó (no solo los tests del cambio) y que no quedan console.log ni feature flags huérfanos. En *infraestructura*, confirma que **la migración tiene camino `down`** probado. En *documentación*, comprueba que el changelog se escribió **con el cambio** (ver [[git-workflow-and-versioning]]).

---

## 🚩 Estrategia de feature flags

**Todo feature necesita un interruptor.** Incluso los cambios "simples" pueden romperse. El flag te permite: **enviar código sin habilitarlo** (mergea temprano, habilita cuando esté listo) · **revertir sin redesplegar** (desactiva el flag en vez de revertir código) · **canary** (1 % → 10 % → 100 %) · **A/B test**.

**Ciclo de vida:** Crear → Habilitar para testing → Canary → Rollout completo → **Quitar el flag y el código muerto**. **Ponle fecha de limpieza cuando lo crees** — los flags que viven para siempre son deuda técnica.

---

## 🚦 El rollout escalonado

```
1. DEPLOY a staging
   └── Suite completa en staging + smoke test manual de flujos críticos

2. DEPLOY a producción (flag OFF)
   └── Verificar que el deploy fue exitoso (health check)
   └── Revisar monitorización de errores (sin errores nuevos)

3. ENABLE para el equipo (flag ON para internos)
   └── El equipo usa la feature en producción
   └── Ventana de monitoring de 24 horas

4. CANARY (flag ON para el 5 % de usuarios)
   └── Monitorizar tasa de error, latencia, comportamiento
   └── Comparar métricas: canary vs. baseline
   └── Ventana de 24-48 horas
   └── Avanzar solo si todos los umbrales pasan

5. AUMENTO GRADUAL (25 % → 50 % → 100 %)
   └── Mismo monitoring en cada paso
   └── Posibilidad de volver al porcentaje anterior

6. ROLLOUT COMPLETO (flag ON para todos)
   └── Monitorizar 1 semana
   └── Limpiar el feature flag
```

### Umbrales de decisión

| Métrica | **Avanzar** (verde) | **Retener e investigar** (amarillo) | **Revertir** (rojo) |
| :--- | :--- | :--- | :--- |
| Tasa de error | Dentro del 10 % del baseline | 10–100 % sobre el baseline | **>2× el baseline** |
| Latencia P95 | Dentro del 20 % del baseline | 20–50 % sobre el baseline | **>50 % sobre el baseline** |
| Errores JS cliente | Sin tipos nuevos | Nuevos en <0,1 % de sesiones | **Nuevos en >0,1 % de sesiones** |
| Métricas de negocio | Neutras o positivas | Caída <5 % (puede ser ruido) | **Caída >5 %** |

### Cuándo revertir de inmediato
Tasa de error >2× el baseline · latencia P95 >50 % sobre el baseline · pico de issues reportados por usuarios · detección de problemas de integridad de datos · descubrimiento de una vulnerabilidad.

---

## 📊 La puerta del error budget

El **error budget** — la fracción de peticiones o tiempo que tu SLO permite fallar — determina si es seguro entregar. Úsalo como **puerta objetiva, no como una negociación**:

```
Presupuesto restante > 20 %  →  Entrega normal; monitoriza de cerca
Presupuesto restante 0-20 %  →  Solo rollouts lentos; nada de alto riesgo
Presupuesto agotado          →  Congela feature work; céntrate en fiabilidad
Presupuesto reinicia         →  Retoma el ritmo normal; integra el fix que lo recuperó
```

> [!important] Mira el *burn rate*, no solo la tasa de error actual
> Consumir presupuesto **más rápido que el ritmo del baseline** es una señal de **retención** (hold), aunque los umbrales individuales estén en verde.

---

## 🔙 Estrategia de rollback

**Todo despliegue debe ser reversible.** El plan incluye **condiciones de trigger** (qué dispara el rollback), **pasos** (comandos exactos), **consideraciones de DB** (¡las migraciones de datos normalmente **no** se revierten! — ver [[deprecation-and-migration]]), y **tiempo estimado de rollback**.

**Restricción importante:** revertir el código de una app **no** revierte una migración destructiva de esquema. Por eso los cambios de esquema usan [[deprecation-and-migration]] en su patrón *expand/contract* — el flag te deja volver a la forma vieja *sin* revertir la DB.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Funciona en staging, funcionará en producción" | Producción tiene datos, patrones de tráfico y casos límite distintos. **Monitoriza tras el deploy** |
| "No necesitamos feature flags para esto" | Todo feature se beneficia de un interruptor. Incluso los cambios "simples" pueden romperse |
| "El monitoring es overhead" | Sin monitoring descubres problemas desde quejas de usuarios en vez de desde dashboards |
| "Añadiremos el monitoring luego" | Añádelo **antes** del lanzamiento. No puedes depurar lo que no puedes ver |
| "Revertir es admitir un fallo" | Revertir es ingeniería responsable. **Enviar una feature rota es el fallo** |
| "La tasa de error está bien, seguimos entregando" | Mira el **burn rate**. Consumir presupuesto más rápido que el baseline es una señal de retención aunque los umbrales individuales estén verdes |

---

## 🚩 Red Flags

- Desplegar a producción sin checklist pre-lanzamiento.
- Sin feature flag para un cambio de riesgo medio o alto.
- Sin monitoring ni alertas antes del deploy.
- Sin plan de rollback probado.
- Rollouts del 0 % al 100 % sin ventana de observación.
- Migraciones destructivas de esquema coupled al mismo deploy que las usa.
- Ignorar los umbrales "amarillo" de la tabla y avanzar.
- Flags nunca eliminados (deuda técnica acumulada).
- "Monitorizaremos en producción" sin ventana ni criterio de decisión.

---

## 🔗 Relaciones

**En el flujo de Addy:** **la última skill del ciclo**. Consume [[code-review-and-quality]], [[security-and-hardening]], [[performance-optimization]] y [[test-driven-development]] como puertas previas; usa los feature flags y la estrategia de despliegue de [[ci-cd-and-automation]]; y se apoya en [[observability-and-instrumentation]] para los umbrales y alertas.

**Equivalencia Matt Pocock:** **no tiene equivalente.** Su pack se queda en el mundo pre-lanzamiento — otra laguna directamente responsable de la cobertura *amplia* de Addy frente a la "cobertura de ciclo de vida fina después de Build" de Matt.

```bash
npx skills add addyosmani/agent-skills --skill shipping-and-launch
```
