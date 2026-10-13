---
alias:
  - Skill ci-cd-and-automation
  - Addy Osmani CI/CD
  - Shift Left
tags:
  - ai/skills
  - addy-osmani
  - ai/ship
  - dev/workflow
  - dev/quality
source: https://github.com/addyosmani/agent-skills/blob/main/skills/ci-cd-and-automation/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Automatiza las puertas de calidad** para que ningún cambio llegue a producción sin pasar tests, lint, comprobación de tipos y build. CI/CD es **el mecanismo de enforcement de todas las demás skills**: captura lo que los humanos y los agentes dejan pasar, y lo hace consistentemente en cada cambio.

> [!important] Dos principios que definen la skill
> **Shift Left** — atrapa los problemas lo antes posible en el pipeline. Un bug que se pilla en lint cuesta minutos; el mismo en producción, horas. Mueve los checks aguas arriba: análisis estático antes que tests, tests antes que staging, staging antes que producción.
> **Faster is Safer** — lotes más pequeños y releases más frecuentes **reducen** el riesgo, no lo aumentan. Un despliegue con 3 cambios es más fácil de depurar que uno con 30.

---

## 🎯 ¿Cuándo usarla?

Montando el pipeline CI de un proyecto nuevo · añadiendo o modificando checks automatizados · configurando pipelines de despliegue · cuando un cambio debería disparar verificación automática · depurando fallos de CI.

---

## 🚧 El pipeline de puertas de calidad

```
Pull Request abierto
    │
    ▼
┌─────────────────┐
│   LINT          │  eslint, prettier
│   ↓ pasa        │
│   TYPE CHECK    │  tsc --noEmit
│   ↓ pasa        │
│   UNIT TESTS    │  jest/vitest
│   ↓ pasa        │
│   BUILD         │  npm run build
│   ↓ pasa        │
│   INTEGRACIÓN   │  tests de API/BD
│   ↓ pasa        │
│   E2E (opcional)│  Playwright/Cypress
│   ↓ pasa        │
│   SECURITY AUDIT│  npm audit
│   ↓ pasa        │
│   BUNDLE SIZE   │  bundlesize check
└─────────────────┘
    │
    ▼
  Listo para revisión
```

> [!warning] Ninguna puerta se puede saltar
> Si falla el lint, **arregla el lint** — no deshabilites la regla. Si falla un test, **arregla el código** — no saltes el test.

La skill trae configs completas de GitHub Actions para el pipeline básico, tests de integración con PostgreSQL como servicio (con health checks y migraciones), E2E con Playwright (con upload del reporte en fallo), y deployments de preview.

> [!tip] Hasta para BD de solo test en CI, usa GitHub Secrets
> En vez de valores hardcodeados. Construye buenos hábitos y evita reutilizar credenciales de test en otros contextos.

---

## 🔄 Devolver los fallos de CI al agente

Aquí está el poder real del CI con agentes de IA: **el bucle de feedback**.

```
CI falla → copia la salida concreta →
  al agente: "El pipeline falló con este error: [pega el error
  específico]. Arréglalo y verifica localmente antes de pushar." →
  el agente arregla → pushea → CI corre de nuevo
```
Por tipo de fallo: lint → `npm run lint --fix` · tipo → lee la ubicación del error · test → sigue [[debugging-and-error-recovery]] · build → revisa config y dependencias.

---

## 🚀 Estrategias de despliegue

**Preview deployments.** Cada PR recibe un despliegue de preview para testing manual.

**Feature flags.** Desacoplan despliegue de release:
- **M ships código sin habilitarlo.** Mergea a `main` temprano, habilita cuando esté listo.
- **Revierte sin redesplegar.** Desactiva el flag en vez de revertir código.
- **Canary.** Habilita para el 1 % de usuarios, luego 10 %, luego 100 %.
- **A/B tests.**

> [!important] Ciclo de vida de un flag
> Crear → Habilitar para testing → Canary → Rollout completo → **Quitar el flag y el código muerto**. Los flags que viven para siempre son deuda técnica — **ponle fecha de limpieza cuando lo crees.**

**Rollouts escalonados:** PR mergeado a `main` → staging automático → verificación manual → producción (trigger manual o automático) → **monitorizar 15 minutos** → si hay errores, rollback; si está limpio, listo.

**Plan de rollback.** *Todo* despliegue debe ser reversible. La skill incluye un workflow de rollback manual parametrizado por versión.

**Entornos:** `.env.example` commiteado · `.env` NO commiteado · `.env.test` commiteado sin secretos reales · secretos de CI en el gestor de secretos · secretos de producción en la plataforma de despliegue. **CI nunca debe tener secretos de producción.**

---

## 🔧 Automatización más allá de CI

- **Dependabot / Renovate** con `open-pull-requests-limit` para no inundar.
- **Build Cop.** Designa a alguien responsable de mantener CI en verde. Cuando el build se rompe, el trabajo del Build Cop es arreglar o revertir — **no la persona cuyo cambio lo rompió.** Esto evita que los builds rotos se acumulen mientras todos asumen que lo arregla otro.
- **PR checks.** Revisiones requeridas (≥1 aprobación) · status checks requeridos (CI debe pasar) · protección de rama (no force-push a `main`) · auto-merge si todo pasa y está aprobado.

## ⚡ Optimizar CI

Cuando el pipeline **supera los 10 minutos**, por orden de impacto:
1. **Cachea dependencias** (`actions/cache`, `setup-node` con `cache: 'npm'`).
2. **Paraleliza jobs** — separa lint, typecheck, test y build en jobs paralelos.
3. **Ejecuta solo lo que cambió** — filtros de rutas para saltar jobs irrelevantes (no ejecutes E2E en PRs solo de docs).
4. **Matrix builds** — reparte las suites de test entre varios runners.
5. **Optimiza la suite de test** — saca los tests lentos del camino crítico.
6. **Runners más grandes** — hosted más grandes o self-hosted para builds CPU-intensivos.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "CI es demasiado lento" | **Optimiza el pipeline, no lo saltes.** Uno de 5 minutos previene horas de depuración |
| "Este cambio es trivial, salta CI" | Los cambios triviales rompen builds. CI es rápido para cambios triviales |
| "El test es flaky, solo re-ejecuta" | Los tests flaky tapan bugs reales y desperdician el tiempo de todos. Arregla el flakiness |
| "Añadiremos CI luego" | Los proyectos sin CI acumulan estados rotos. Móntalo el día uno |
| "El testing manual basta" | El manual no escala ni es repetible. Automatiza lo que puedas |

---

## 🚩 Red Flags

- Sin pipeline CI en el proyecto.
- Fallos de CI ignorados o silenciados.
- Tests deshabilitados en CI para que el pipeline pase.
- Despliegues a producción sin verificación en staging.
- Sin mecanismo de rollback.
- Secretos en código o ficheros de config de CI (en vez del gestor de secretos).
- Tiempos largos de CI sin esfuerzo de optimización.

---

## 🔗 Redes

**En el flujo de Addy:** la fase SHIP ejecuta [[constraint-driven-development]] en CI. Alimenta a [[shipping-and-launch]] (staging, preview, flags) y usa los límites de [[security-and-hardening]] (auditoría de dependencias como gate).

**Equivalencia Matt Pocock:** **no tiene equivalente.** Otra pieza exclusiva de la cobertura *amplia* del ciclo de vida de Addy.

```bash
npx skills add addyosmani/agent-skills --skill ci-cd-and-automation
```
