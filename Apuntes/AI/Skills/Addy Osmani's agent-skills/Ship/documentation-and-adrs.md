---
alias:
  - Skill documentation-and-adrs
  - Addy Osmani Documentation and ADRs
  - Architecture Decision Records
tags:
  - ai/skills
  - addy-osmani
  - ai/ship
  - dev/documentation
  - dev/architecture
source: https://github.com/addyosmani/agent-skills/blob/main/skills/documentation-and-adrs/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Documenta decisiones, no solo código.** La documentación más valiosa captura el **por qué**: el contexto, las constraints y los trade-offs que llevaron a una decisión. El código muestra *qué* se construyó; la documentación explica **por qué** se construyó así y **qué alternativas seConsideraron**.

---

## 🎯 ¿Cuándo usarla?

Tomando una decisión arquitectónica significativa · eligiendo entre enfoques que compiten · añadiendo o cambiando una API pública · entregando una feature que cambia el comportamiento visible · **onboarding** de nuevos miembros del equipo (o agentes) al proyecto · cuando te descubres **explicando lo mismo una y otra vez**.

**Cuándo NO:** documentar código obvio. **No añadas comentarios que restaten lo que el código ya dice.** No escribas docs para prototipos desechables.

---

## 📜 Los ADR (Architecture Decision Records)

**Los ADR capturan el razonamiento detrás de decisiones técnicas significativas. Son la documentación de mayor valor que puedes escribir.**

### Cuándo escribir un ADR
Elegir un framework, librería o dependencia mayor · diseñar un modelo de datos o esquema de BD · seleccionar una estrategia de autenticación · decidir la arquitectura de la API (REST vs. GraphQL vs. tRPC) · elegir herramientas de build, plataformas de hosting o infraestructura · **cualquier decisión cara de revertir**.

> [!tip] Casa primero con la convención existente
> Si tu proyecto ya tiene ADRs, **copia su formato y ubicación**. No introduzcas un segundo sistema de convenciones en el mismo repo.

### La plantilla

```markdown
# ADR-XXX: <Título de la decisión>

## Estado
Propuesto | Aceptado | Deprecado | Sustituido por ADR-YYY

## Fecha
YYYY-MM-DD

## Contexto
<El problema, las fuerzas en juego, las constraints. Sin esto,
nadie puede entender por qué la decisión tiene sentido.>

## Decisión
<Qué decidimos, explícitamente.>

## Alternativas consideradas
### MongoDB
<Por qué se descartó.>
### SQLite
<Por qué se descartó.>
### MySQL
<Por qué se descartó.>

## Consecuencias
<Positivas y negativas. Las negativas son las importantes.>
```

> [!important] Las alternativas son la mitad del valor
> Un ADR sin alternativas descartadas no registra por qué no tomaste los otros caminos. Seis meses después, alguien replanteará la misma pregunta y encontrará una decisión ya tomada sin contexto, y preguntará por qué cambió.

### Ciclo de vida de un ADR
`Propuesto` → `Aceptado` (o `Rechazado`, con el razonamiento) → `Deprecado` cuando una decisión posterior lo sustituye → `Sustituido por ADR-nuevo`. **Los ADRs son inmutables una vez aceptados**: un ADR que editas es un ADR que pierde su función.

---

## 💬 Documentación inline

### Cuándo comentar
Documenta **por qué**, no **qué**:
```typescript
// ✅ Explica el porqué, que el código no puede expresar
// Batching porque cada llamada individual cuesta ~200ms en la API de Maps

// ❌ Restata el código
// increment counter
count++
// increase counter by one
```
La regla general: si el comentario desaparece al reformatear el código, probablemente era un "qué" inútil.

### Cuándo NO comentar
Código obvio · ruido de formato auto-generado · **aserciones desactivadas o"Do it later"** · el tipo ya lo declara en TypeScript.

### Documenta los gotchas conocidos
Fuente de configuración, valores mágicos, condiciones de carrera conocidas, limitaciones de rendimiento — con un enlace a la issue. Esto es lo que más valoran los futuros lectores.

---

## 📡 Documentación de API

- **Inline con los tipos** *(preferido en TypeScript)* — JSDoc en la interfaz: el contrato, los errores que lanza, los ejemplos de uso.
- **OpenAPI / Swagger para APIs REST** — la especificación es la fuente de verdad.
- **Documentación de API commiteada junto a la implementación**, no después.

## 📄 Estructura del README

Quick start (qué es y cómo se usa en 30 s) · comandos · arquitectura (mapa de alto nivel) · cómo contribuir · changelog.

**Mantenimiento del changelog:** agrupa por `Added / Changed / Fixed`, con la versión y fecha, redactado en torno al impacto para el usuario.

## 🤖 Documentación para agentes
Escribe en `AGENTS.md` o `CLAUDE.md` lo que un agente necesita saber: **stack, comandos, convenciones, boundaries y patrones**. Ver [[context-engineering]] para la gestión dinámica de esa ventana.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "El código se documenta solo" | El código muestra el **qué**. No muestra el **por qué**, las alternativas rechazadas, ni las constraints que aplican |
| "Escribiremos docs cuando la API se estabilice" | Las APIs se estabilizan **más rápido** cuando las documentas. El doc es el primer test del diseño |
| "Nadie lee docs" | **Los agentes sí.** Los futuros ingenieros sí. Tu yo de dentro de 3 meses sí |
| "Los ADRs son overhead" | Un ADR de 10 minutos previene un debate de 2 horas sobre la misma decisión seis meses después |
| "Los comentarios se quedan obsoletos" | Los comentarios del **por qué** son estables. Los del **qué** se quedan obsoletos — por eso solo escribes los primeros |

---

## 🚩 Red Flags

- Decisiones arquitectónicas tomadas sin ADR.
- ADRs editados una vez aceptados.
- ADRs sin sección de alternativas consideradas.
- Comentarios que restatan el código.
- README desactualizado respecto al código real.
- Documentación de API que no coincide con la implementación.
- Documentación solo para humanos cuando el proyecto también lo usan agentes (falta `AGENTS.md`).

---

## 🔗 Relaciones

**En el flujo de Addy:** fase SHIP. Cierra el ciclo de [[spec-driven-development]] (la spec es el *qué*, el ADR es el *por qué* de las decisiones que no cabían en ella). Las APIs que documenta las define [[api-and-interface-design]].

**Equivalencia Matt Pocock:** **solapamiento fuerte** con [[domain-modeling]], que mantiene `CONTEXT.md` (vocabulario de dominio) y ADRs. **Matiz:** el `domain-modeling` de Matt separa explícitamente **contexto único** (`CONTEXT.md` + `docs/adr/`) de **multicontexto** (`CONTEXT-MAP.md` para monorrepos), y pone el foco en modelar el *dominio*; `documentation-and-adrs` de Addy pone el foco en **decisiones técnicas y su trazabilidad** (estado, ciclo de vida, alternativas, consecuencias), incluyendo el inline.

```bash
npx skills add addyosmani/agent-skills --skill documentation-and-adrs
```
