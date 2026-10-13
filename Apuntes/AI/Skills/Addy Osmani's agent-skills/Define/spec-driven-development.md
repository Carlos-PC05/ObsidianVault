---
alias:
  - Skill spec-driven-development
  - SDD
  - Addy Osmani Spec Driven Development
tags:
  - ai/skills
  - addy-osmani
  - ai/define
  - dev/requirements
  - dev/planning
source: https://github.com/addyosmani/agent-skills/blob/main/skills/spec-driven-development/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Escribe una **especificación estructurada antes de escribir código**. La spec es la **fuente de verdad compartida** entre el agente y la persona: define *qué* construimos, *por qué* y *cómo sabremos que está*. Código sin spec es adivinar.

> [!note] SDD ≠ "escribir docs después"
> El valor de la spec está en forzar claridad **antes** de que exista código. Escribir los requisitos a posteriori es documentación, no especificación.

---

## 🎯 ¿Cuándo usarla?

- Proyecto, feature o cambio nuevo.
- Requisitos ambiguos o incompletos.
- El cambio toca varios archivos o módulos.
- Estás a punto de tomar una decisión arquitectónica.
- La tarea tardaría **más de 30 minutos** en implementarse.

**Cuándo NO:** correcciones de una línea, typos, o cambios con requisitos inequívocos y autocontenidos.

---

## 🔄 El flujo con puertas

```
SPECIFY ──→ PLAN ──→ TASKS ──→ IMPLEMENT
   │          │        │          │
   ▼          ▼        ▼          ▼
 Humano    Humano    Humano    Humano
 revisa    revisa    revisa    revisa
```

**Ninguna fase avanza hasta que la actual ha sido validada por una persona.**

### Fase 0 — Scope Check *(solo si aplica)*

La mayoría de peticiones describen **una** capacidad. Si es así, sáltala. La Fase 0 existe para la excepción: cuando una sola petición **agrupa varias capacidades independientemente testeables**.

**Detecta** si: nombra capacidades distintas con sus propios consumidores o datos · los criterios de aceptación se agrupan en grupos que podrían entregarse y verificarse por separado · una capacidad podría cortarse o reemplazarse sin reescribir los requisitos de las demás.

Si aplica, **propón un mapa de capacidades antes de escribir ninguna spec** (tabla de módulos + orden de build, no un plan de proyecto):

```markdown
| Module id     | Responsibility              | Depends on |
|---------------|-----------------------------|------------|
| identity      | Cuentas, sesiones, SSO      | —          |
| billing       | Planes, facturas, pagos     | identity   |
| reporting     | Dashboards de uso           | billing    |

Build order: identity → billing → reporting
```

- **Module ids estables.** Kebab-case, elegidos una vez, nunca renombrados a mitad de iniciativa. Specs, planes y comandos aguas abajo eligen trabajo por estos ids.
- **Dirección de dependencias, sin ciclos.** Si dos módulos se necesitan mutuamente, son **un** módulo.
- **Las interfaces viven en la frontera.** El mapa registra que `billing` depende de `identity`; el contrato entre ellos va en la spec del módulo *proveedor*.

**El mapa está tan cerrado como cada fase.** Después se **recursa por módulo**: Specify → Plan → Tasks → Implement, en orden de dependencia, con una spec por módulo (`SPEC-identity.md`, `SPEC-billing.md`).

### Fase 1 — Specify

**Declara los supuestos primero**, antes de escribir contenido:

```
ASSUMPTIONS I'M MAKING:
1. Esto es una app web (no nativa móvil)
2. La autenticación usa cookies de sesión (no JWT)
3. La base de datos es PostgreSQL (según el schema Prisma existente)
→ Corrígeme ahora o procedo con estos.
```

**La spec cubre seis áreas núcleo:**

| # | Área | Qué exige |
| :--- | :--- | :--- |
| 1 | **Objetivo** | Qué construimos y por qué, quién es el usuario, cómo se ve el éxito |
| 2 | **Comandos** | Comandos ejecutables completos con flags, no solo nombres de herramienta (`npm run build`, `npm test -- --coverage`) |
| 3 | **Estructura del proyecto** | Dónde vive el código, dónde los tests, dónde la docs |
| 4 | **Estilo de código** | **Un snippet real** de tu estilo bates tres párrafos describiéndolo |
| 5 | **Estrategia de testing** | Framework, dónde viven los tests, expectativa de cobertura, qué nivel para qué preocupación |
| 6 | **Fronteras** | Sistema de tres niveles: **Siempre** / **Preguntar primero** / **Nunca** |

> [!tip] Reformula las instrucciones como criterios de éxito
> *"Haz el dashboard más rápido"* → *"LCP < 2.5 s en 4G · carga inicial de datos < 500 ms · sin layout shift (CLS < 0.1) → ¿Son estos los objetivos correctos?"*
> Esto te permite iterar hacia un objetivo claro en vez de adivinar qué significa "rápido".

> [!important] Para tras guardar la spec (CRÍTICO)
> 1. Resúmela y lista las preguntas abiertas. 2. Pide aprobación o cambios. 3. **DETÉN EL TURNO.** No arranques la Fase 2, no invoques [[planning-and-task-breakdown]], no escribas código. La planificación empieza **solo tras la aprobación, en un turno posterior**.

### Fases 2, 3 y 4
- **Plan** → plan técnico: componentes, dependencias, orden, riesgos, paralelizable vs. secuencial, checkpoints.
- **Tasks** → tareas discretas, cada una completable en **una sesión enfocada**, con criterios de aceptación explícitos, un paso de verificación, ordenadas por dependencia, y **ninguna tocando más de ~5 archivos**.
- **Implement** → delegar en [[incremental-implementation]] + [[test-driven-development]], usando [[context-engineering]] para cargar solo las secciones de spec relevantes.

> [!note] Compatible con herramientas de spec externas
> Si el proyecto ya usa OpenSpec u otro sistema, **mantén su formato y almacenamiento** en vez de crear un `SPEC.md` duplicado. Esta skill es dueña de la aclaración, el contenido y las puertas; la herramienta externa, de cómo se representa la spec aprobada.

---

## 💡 Mantener la spec viva

Es un **documento viviente**, no un artefacto de un solo uso:
- **Actualízala cuando cambien las decisiones** (¿el modelo de datos necesita otro enfoque? actualiza la spec primero, luego implementa).
- **Actualízala cuando cambie el alcance** (features añadidas o cortadas se reflejan).
- **Commitea la spec** junto al código.
- **Referencia la spec en los PRs**, enlazando a la sección que cada PR implementa.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Esto es simple, no necesito spec" | Las tareas simples no necesitan specs *largas*, pero sí criterios de aceptación. Una spec de dos líneas vale. |
| "Escribiré la spec después de codificar" | Eso es documentación, no especificación. |
| "La spec nos ralentizará" | Una spec de 15 min previene horas de retrabajo. |
| "Los requisitos cambiarán igual" | Por eso la spec es un documento vivo. Una spec obsoleta sigue siendo mejor que ninguna. |
| "El usuario sabe lo que quiere" | Incluso las peticiones claras tienen supuestos implícitos. |
| "Es una feature grande, dividir es overhead" | Si los criterios se agrupan en grupos testeables, una spec monolítica obliga a cada tarea a razonar sobre el contrato entero. |

---

## 🔗 Relaciones

**En el flujo de Addy:** upstream [[interview-me]] · downstream [[planning-and-task-breakdown]] → [[incremental-implementation]].

**Equivalencia Matt Pocock:** corresponde a [[to-spec]] (que en la v1.1 de Matt reemplazó a `/to-prd`). **Diferencia clave:** Matt publica la spec como **un issue/ticket en el gestor de tareas** y advierte que la skill *no* busca duplicados en el backlog; Addy guarda un **archivo en el repo** y añade el **Scope Check / mapa de capacidades**, algo que Matt no tiene.

```bash
npx skills add addyosmani/agent-skills --skill spec-driven-development
```
