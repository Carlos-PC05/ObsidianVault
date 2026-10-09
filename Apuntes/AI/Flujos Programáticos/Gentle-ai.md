---
tags:
  - ai
  - ai/flujos-programáticos
---
> **Nota de versión:** a fecha de hoy, **23 de septiembre de 2026**, la última versión estable publicada es **Gentle-AI v3.6.1**, publicada el 22 de septiembre. Parte de `docs/quickstart.md` todavía menciona v2.6.0, así que esa referencia concreta está desactualizada.

# Guía completa de Gentle-AI

## 1. Qué es realmente Gentle-AI

Gentle-AI **no es otro agente de programación**.

No sustituye a:

- Codex
    
- Claude Code
    
- OpenCode
    
- Cursor
    
- Gemini CLI
    
- etc.
    

Es una **capa de ingeniería** que configura al agente que ya utilizas y le proporciona:

|Componente|Función|
|---|---|
|**ODD**|Flujo normal de desarrollo|
|**SDD**|Desarrollo formal basado en especificaciones|
|**RDD**|Revisión determinista del trabajo terminado|
|**Engram**|Memoria persistente entre sesiones|
|**Skills**|Instrucciones especializadas que se cargan según la tarea|
|**Skill Registry**|Índice de skills disponibles en el proyecto|
|**Context7**|Documentación actualizada de librerías/frameworks|
|**CodeGraph**|Grafo de símbolos del proyecto|
|**Persona**|Comportamiento/estilo del agente|
|**Permissions**|Protecciones frente a secretos y rutas sensibles|

La filosofía del proyecto es literalmente que, tras instalarlo, **pienses lo menos posible en Gentle-AI** y simplemente le digas al agente qué quieres conseguir.

---

# 2. El modelo mental que tienes que aprender

Hay cuatro piezas realmente importantes:

```text
                    ┌─────────────┐
                    │   Engram    │
                    │   memoria   │
                    └──────┬──────┘
                           │
                           ▼
TU PETICIÓN ───────► ODD ───────────────────► implementación normal
                     │
                     │ si TÚ eliges SDD
                     ▼
                    SDD
          explore → spec → design → tasks
                     │
                     ▼
                   apply
                     │
                     ▼
               comprobaciones
                     │
                     ▼
                    RDD
              revisión del cambio
                     │
                     ▼
               Ready / decisión
                     │
                     ▼
             commit/push/PR según
             política del proyecto
```

La distinción fundamental es:

**ODD = flujo cotidiano.**

**SDD = flujo formal que eliges conscientemente.**

**RDD = revisión del resultado.**

**Engram = memoria transversal a todo lo anterior.**

Esta separación es fundamental porque uno de los errores más comunes sería utilizar SDD para absolutamente todo. Gentle-AI está expresamente diseñado para **no hacerlo**.

---

# 3. Instalación

En Linux/macOS, la instalación recomendada por el README es:

```bash
curl -fsSL https://raw.githubusercontent.com/Gentleman-Programming/gentle-ai/main/scripts/install.sh | bash
```

Después:

```bash
gentle-ai
```

para abrir el instalador interactivo.

Y finalmente:

```bash
gentle-ai doctor
```

para verificar que todo funciona.

Los requisitos principales son:

```text
Git >= 2.38
Node.js >= 18
npm
```

Go `>=1.25.10` es necesario si construyes/instalas desde código fuente mediante `go install`. Gentle-AI no instala por ti los agentes que selecciones: Codex, Claude Code, OpenCode, etc. deben existir previamente.

Puedes comprobar primero:

```bash
git --version
node --version
npm --version
gentle-ai version
```

---

# 4. Instalación programática en lugar de TUI

Si prefieres configurar todo mediante CLI, puedes hacerlo.

Por ejemplo, para Codex:

```bash
gentle-ai install \
  --agent codex \
  --preset ecosystem-only
```

Antes de tocar nada, recomiendo ejecutar:

```bash
gentle-ai install \
  --agent codex \
  --preset ecosystem-only \
  --dry-run
```

`--dry-run` es bastante importante porque te permite ver qué va a modificar sin aplicar cambios.

Gentle-AI hace además copias de seguridad antes de modificar configuraciones administradas.

---

# 5. Qué preset elegir

Actualmente existen estos presets.

|Preset|Qué incluye|Para quién|
|---|---|---|
|`full-gentleman`|Todo|Quieres toda la experiencia Gentle|
|`ecosystem-only`|Engram + SDD + Skills + Context7 + GGA|Muy buena opción general|
|`minimal`|Engram + skills SDD|Solo quieres memoria + workflow|
|`custom`|Tú eliges|Configuración avanzada|

Para **Codex**, empezaría con:

```bash
--preset ecosystem-only
```

porque obtienes las piezas realmente interesantes sin añadir elementos estéticos o específicos de otros clientes.

Puedes cambiarlo más adelante volviendo a ejecutar el instalador.

---

# 6. ODD: el flujo que vas a utilizar el 90 % del tiempo

ODD significa:

**Organic Driven Development**.

Y este es probablemente el concepto más importante de Gentle-AI.

ODD **no es un comando**.

No necesitas hacer:

```text
/odd
odd-init
odd-new
```

Nada de eso.

Simplemente dices:

```text
Añade paginación al endpoint de usuarios utilizando el patrón
que ya seguimos en los demás endpoints.
```

Y Gentle-AI aplica ODD automáticamente.

---

# 7. Qué hace ODD internamente

Su flujo conceptual es:

```text
PETICIÓN
   │
   ▼
¿autoriza modificar código?
   │
   ├── NO ──► investigar / explicar
   │
   └── SÍ
       │
       ▼
    explorar
       │
       ▼
resolver incertidumbre real
       │
       ▼
clasificar el trabajo
       │
       ├── pequeño ───────────► implementar
       │
       └── sustancial
               │
               ▼
      odd/tasks/feature.md
               │
               ▼
        implementar tareas
               │
               ▼
          comprobar
               │
               ▼
         evidencia
               │
               ▼
             RDD
```

Una petición solo de investigación como:

```text
Explícame cómo funciona la autenticación de esta API.
```

debe permanecer **read-only**.

Una petición como:

```text
Refactoriza la autenticación para separar validación JWT y autorización.
```

autoriza implementación.

Esta distinción es deliberada: el agente no debería convertir una petición de análisis en modificaciones de código.

---

# 8. ODD pequeño frente a ODD sustancial

Gentle-AI diferencia entre trabajo pequeño y sustancial.

### Cambio pequeño

Por ejemplo:

```text
Corrige el nombre de esta variable.
```

o:

```text
Cambia el timeout de 10 a 15 segundos.
```

Puede ejecutarse directamente sin crear documentación adicional.

### Cambio sustancial

Por ejemplo:

```text
Implementa autenticación mediante refresh tokens.
```

Después de explorar el código, si detecta varias tareas relevantes, crea automáticamente algo como:

```text
odd/tasks/refresh-token-auth.md
```

Ese documento contiene cosas como:

```text
objetivo
problema
motivación
scope
restricciones
tareas
criterios de aceptación
progreso
evidencias
siguiente paso
```

Y tiene además una copia en Engram:

```text
odd/<feature>/tasks
```

Eso permite abandonar una sesión y continuar días después sin reconstruir mentalmente todo el trabajo.

---

# 9. No tienes que decidir tú cuándo delegar

Gentle-AI utiliza reglas bastante concretas.

La regla aproximada actual es:

|Situación|Acción|
|---|---|
|Leer 1–3 archivos|inline|
|Comprender 4+ archivos|delegar exploración|
|Cambio mecánico en 1 archivo|inline|
|Modificar 2+ archivos no triviales|delegar writer|
|Investigación amplia|delegar|
|Tests/builds complejos|contexto separado cuando sea útil|

Esto es especialmente interesante con agentes que soportan subagentes.

Pero hay una distinción fundamental:

> **Delegar ≠ utilizar SDD.**

Un cambio de 20 archivos puede seguir siendo **ODD delegado**.

El tamaño, riesgo o número de archivos **no activa SDD automáticamente**.

---

# 10. Los cuatro estados que deberías ver

Gentle-AI intenta esconderte toda la maquinaria interna y reducir el progreso a cuatro estados:

```text
Working
Checking
Ready
Needs your decision
```

Significan:

|Estado|Significado|
|---|---|
|**Working**|Todavía está modificando el resultado|
|**Checking**|Tests/verificación/revisión|
|**Ready**|Hay evidencia suficiente|
|**Needs your decision**|Necesita una decisión humana|

El objetivo es que no tengas que entender hashes, lineages, receipts o estados internos de RDD para trabajar normalmente.

---

# 11. TDD dentro de ODD

Otro detalle importante.

Gentle-AI **no activa TDD simplemente porque existan tests**.

El modo TDD procede de:

```text
configuración del proyecto
        o
decisión explícita del usuario
```

Si TDD está activado, exige observar:

```text
RED
 ↓
GREEN
 ↓
REFACTOR
```

Es decir:

```text
test falla
   ↓
implementación mínima
   ↓
test pasa
   ↓
refactorización
```

Si TDD está desactivado, siguen siendo obligatorias comprobaciones funcionales normales.

Por tanto, puedes decir explícitamente:

```text
Implementa esta feature utilizando TDD.
```

o:

```text
No uses TDD para este cambio.
```

---

# 12. Los commits de unidad de trabajo

Esta parte es importante conocerla.

Para trabajo ODD sustancial, el protocolo está diseñado alrededor de **work-unit commits**.

Es decir:

```text
feature
 ├── task 1 → commit
 ├── task 2 → commit
 ├── task 3 → commit
 └── task 4 → commit
```

En lugar de terminar con:

```text
1 commit gigantesco con 2.000 líneas
```

El documento ODD conserva la identidad de esos commits como evidencia.

Esto encaja bastante bien con:

```text
Conventional Commits
```

como:

```text
feat(auth): add refresh token model

feat(auth): implement token rotation

test(auth): cover expired refresh tokens
```

Ahora bien, esto **no significa que Gentle-AI tenga permiso implícito para hacer push, abrir PR o publicar nada**.

La entrega sigue gobernada por la política normal de tu repositorio.

---

# 13. La regla de ~400 líneas

Verás aparecer bastante el número **400 líneas**.

No significa:

> «una tarea no puede tener más de 400 líneas».

Gentle-AI lo considera una **heurística**, no un límite.

Aproximadamente:

```text
~400 authored changed lines
```

ayuda a decidir cuándo un trabajo empieza a ser demasiado grande para una unidad cómoda de revisión.

No debes:

- minificar código;
    
- eliminar comentarios;
    
- dividir artificialmente funciones;
    
- evitar tests;
    

solo para bajar de 400 líneas.

El propio proyecto prohíbe explícitamente interpretar ese número como un hard cap.

---

# 14. Engram: la memoria persistente

Engram hace que una conversación nueva pueda recordar cosas como:

```text
Elegimos SQLAlchemy async por X.

El endpoint antiguo /users/search está deprecated.

La autenticación utiliza RS256.

Este bug venía de una race condition en X.
```

El agente utiliza automáticamente herramientas como:

```text
mem_save
mem_search
```

No necesitas llamar tú a Engram normalmente.

---

# 15. Comandos de Engram que sí conviene conocer

Los útiles son:

```bash
engram tui
```

Explorar visualmente memoria.

```bash
engram search "authentication"
```

Buscar memoria.

```bash
engram projects list
```

Ver proyectos.

Y especialmente:

```bash
engram sync
```

exporta memoria del proyecto a:

```text
.engram/
```

Esto te permite versionarla en Git si quieres compartir el conocimiento entre máquinas o miembros del equipo.

En otra máquina:

```bash
engram sync --import
```

restaura esas memorias.

Una estrategia interesante sería:

```text
trabajo importante terminado
        ↓
engram sync
        ↓
git add .engram/
        ↓
commit
```

si quieres que el conocimiento del proyecto viaje con el repositorio.

---

# 16. Skills

Las skills son instrucciones especializadas.

Por ejemplo Gentle-AI incluye skills para:

```text
Go testing
Work-unit commits
Chained PR
Skill creation
Skill improvement
Cognitive documentation
Issue creation
Branch + PR
SDD
```

Actualmente la documentación enumera **22 skills integradas** entre las de SDD y foundation.

Además existe el repositorio separado `Gentleman-Skills` para tecnologías como:

```text
React
TypeScript
Angular
Tailwind
Zod
Playwright
...
```

---

# 17. Skill Registry

Aquí Gentle-AI hace algo que me parece especialmente sensato.

No mete todas las skills completas en el contexto.

En su lugar construye:

```text
.atl/skill-registry.md
```

que funciona como índice.

Ejemplo conceptual:

```text
typescript
  → ~/.codex/skills/typescript/SKILL.md

playwright
  → ~/.codex/skills/playwright/SKILL.md

fastapi
  → project/skills/fastapi/SKILL.md
```

Cuando una tarea necesita una skill:

```text
petición
   ↓
orquestador consulta registry
   ↓
encuentra skill adecuada
   ↓
pasa ruta del SKILL.md
   ↓
subagente lee la skill completa
```

No genera resúmenes de las skills porque podrían perder instrucciones importantes.

---

# 18. ¿Tengo que regenerar el registry?

Normalmente **no**.

Codex, Claude Code y OpenCode reciben hooks que ejecutan algo equivalente a:

```bash
gentle-ai skill-registry refresh --quiet
```

al iniciar.

Además existe caché:

```text
.atl/.skill-registry.cache.json
```

por lo que no reconstruye todo cada vez.

Si instalas/modificas una skill:

```bash
gentle-ai skill-registry refresh --force
```

Y para inspeccionar sin modificar nada:

```bash
gentle-ai skill-registry list
```

o:

```bash
gentle-ai skill-registry list --json
```

---

# 19. ¿Debo subir `.atl/` a Git?

No.

Gentle-AI trata `.atl/` como infraestructura local regenerable y añade:

```gitignore
.atl/
```

al `.gitignore`. El código del registry define explícitamente `.atl/` como entrada a ignorar.

Y el proceso de refresh actualiza `.gitignore` de forma atómica precisamente para añadir esa exclusión.

Por tanto:

```text
.engram/             → puedes versionarlo intencionadamente
odd/tasks/           → estado real de la feature
.atl/                → NO, generado localmente
```

Esta diferencia merece la pena recordarla.

---

# 20. SDD: cuándo utilizarlo

SDD significa:

**Spec-Driven Development**.

No deberías utilizarlo simplemente porque una feature sea grande.

Úsalo cuando quieras explícitamente separar:

```text
qué queremos
qué requisitos existen
cómo se va a diseñar
qué tareas habrá
cómo se implementará
cómo se comprobará
```

Gentle-AI deja muy claro que:

```text
tamaño ≠ SDD
riesgo ≠ SDD
ambigüedad ≠ SDD
```

SDD se activa porque **quieres artefactos formales separados**.

---

# 21. Cómo activar SDD

La forma más sencilla:

```text
Usa SDD para implementar el sistema de refresh tokens.
```

o:

```text
Quiero utilizar Spec-Driven Development para esta feature.
```

No necesitas memorizar cada fase.

Especialmente en clientes como Codex, donde Gentle-AI no depende de slash commands para este flujo.

---

# 22. Ciclo completo de SDD

Conceptualmente:

```text
Explore
   ↓
Research (opcional)
   ↓
Proposal
   ↓
      ┌───────────────┐
      │ aprobación    │
      │    humana     │
      └───────┬───────┘
              ↓
            Spec
              ↓
           Design
              ↓
            Tasks
              ↓
            Apply
              ↓
         Verify opcional
              ↓
           Archive
```

### Explore

Investiga el código existente.

### Proposal

Define:

```text
qué queremos cambiar
por qué
scope
enfoque
```

Aquí existe una decisión humana.

### Spec

Convierte la propuesta en requisitos concretos y escenarios.

### Design

Define arquitectura y decisiones técnicas.

### Tasks

Transforma el diseño en unidades implementables.

### Apply

Implementa.

### Verify

Comprueba que la implementación coincide con la especificación.

Es opcional.

### Archive

Conserva el estado final real del cambio.

Muy importante:

> **Archive no significa aprobado, desplegado ni publicado.**

---

# 23. ODD vs SDD

Una forma sencilla de decidir:

|Situación|Utilizaría|
|---|---|
|Bug|ODD|
|Refactor normal|ODD|
|Endpoint nuevo|ODD|
|Feature pequeña|ODD|
|Feature grande pero bien entendida|ODD|
|Investigación|ODD read-only|
|Cambio multiarchivo|ODD delegado|
|Diseño arquitectónico que quiero documentar formalmente|SDD|
|Feature donde quiero proposal/spec/design/tasks separados|SDD|
|Cambio con requisitos que quiero auditar posteriormente|SDD|

Es decir:

```text
ODD por defecto.

SDD porque TÚ quieres SDD.
```

---

# 24. RDD: revisión del resultado

RDD significa:

**Receipt-Driven Development**.

Está **activado por defecto**, aunque puedes desactivarlo.

Su objetivo es evitar algo muy típico de los agentes:

```text
agente modifica código
       ↓
agente revisa "más o menos"
       ↓
se modifica otra cosa
       ↓
la revisión ya no corresponde exactamente
al código que estamos viendo
```

RDD primero **congela un candidato exacto** y revisa esa versión.

---

# 25. Riesgo de RDD

RDD clasifica candidatos aproximadamente como:

```text
passive
medium
high
```

Y adapta la profundidad.

Conceptualmente:

```text
passive
   ↓
comprobación estructural

medium
   ↓
revisión focalizada

high
   ↓
revisión profunda
```

Para los candidatos de alto riesgo utiliza el esquema de revisión 4R:

```text
Risk
Resilience
Readability
Reliability
```

El candidato revisado es inmutable durante la revisión.

---

# 26. No tienes que ejecutar RDD manualmente

Esto es importantísimo.

Aunque existen comandos como:

```bash
gentle-ai review status
gentle-ai review assess
gentle-ai review ...
```

**no están pensados para que reconstruyas tú el protocolo manualmente en el día a día**.

El agente recibe transiciones exactas del binario y debe ejecutarlas literalmente.

La idea es evitar que el LLM invente qué transición debería venir después.

Tú normalmente solo necesitas controlar si RDD está activado.

---

# 27. Los comandos RDD que sí debes conocer

Comprobar:

```bash
gentle-ai review mode status --cwd .
```

Activarlo globalmente:

```bash
gentle-ai review mode enable \
  --scope global \
  --cwd .
```

Desactivarlo globalmente:

```bash
gentle-ai review mode disable --cwd .
```

Desactivarlo solo en un repositorio:

```bash
gentle-ai review mode disable \
  --scope clone \
  --cwd .
```

Volver a heredar la configuración global:

```bash
gentle-ai review mode enable \
  --scope clone \
  --cwd .
```

Yo mantendría **RDD activado**, especialmente para desarrollo agentic serio.

---

# 28. RDD NO da permiso para hacer push

Esta separación también es fundamental.

Una revisión puede terminar satisfactoriamente:

```text
RDD
 ↓
Ready
```

pero eso **NO significa**:

```text
git push
gh pr create
merge
release
deploy
```

La documentación insiste repetidamente en que la entrega sigue siendo responsabilidad de:

```text
usuario
+
política normal del repositorio
```

Esto evita que una revisión técnica se convierta accidentalmente en autorización de despliegue.

---

# 29. Codex + Gentle-AI

Gentle-AI tiene integración específica para Codex.

La configuración principal queda en:

```text
~/.codex/
```

Concretamente:

```text
~/.codex/config.toml
~/.codex/AGENTS.md
~/.codex/skills/
~/.codex/engram-instructions.md
```

Gentle-AI también configura MCP mediante:

```text
~/.codex/config.toml
```

---

# 30. Multi-agent de Codex

La versión actual de Gentle-AI está diseñada para utilizar la superficie multi-agent nativa de Codex.

Comprueba:

```toml
features.multi_agent = true
```

y que la sesión disponga de herramientas equivalentes a:

```text
spawn_agent
wait_agent
list_agents
```

Si existen, Gentle-AI puede hacer:

```text
Codex principal
    │
    ├── exploration agent
    │
    ├── implementation agent
    │
    └── verification/review agent
```

La documentación interna del orquestador comprueba explícitamente esta capacidad al comienzo de la sesión.

Esto encaja perfectamente con la filosofía de:

```text
mantener limpio el contexto principal
         ↓
delegar lectura pesada
         ↓
devolver solo resultados comprimidos
```

---

# 31. Una sesión real de desarrollo

Supongamos que quieres implementar:

```text
Sistema de refresh tokens para FastAPI.
```

Tú simplemente dices:

```text
Implementa refresh tokens en la API siguiendo la arquitectura
existente y manteniendo compatibilidad con el login actual.
```

Gentle-AI debería hacer algo similar a:

```text
1. ODD recibe la petición

2. Explora:
   routers/
   services/
   models/
   schemas/
   auth/

3. Detecta que es sustancial

4. Crea:
   odd/tasks/refresh-tokens.md

5. Consulta skills:
   .atl/skill-registry.md

6. Localiza skills relevantes

7. Delega exploración si hace falta

8. Implementa cada unidad

9. Ejecuta tests

10. Registra evidencia

11. Crea work-unit commits

12. RDD evalúa los candidatos

13. Devuelve:
    Ready
```

Y entonces tú decides qué hacer con Git:

```bash
git log
git diff main...HEAD
git push
```

y posteriormente PR si procede.

---

# 32. Si quieres SDD para esa misma feature

En cambio dirías:

```text
Quiero utilizar SDD para diseñar e implementar el sistema
de refresh tokens. No empieces la implementación hasta que
haya aprobado la propuesta.
```

Entonces:

```text
Explore
 ↓
Proposal
 ↓
TU APROBACIÓN
 ↓
Spec
 ↓
Design
 ↓
Tasks
 ↓
Apply
 ↓
Verify
 ↓
Archive
```

Eso tiene mucho más sentido si estás tomando una decisión arquitectónica importante que quieres conservar y revisar.

---

# 33. Actualizar Gentle-AI correctamente

Este detalle también es importante.

Cuando actualizas el binario:

```bash
gentle-ai upgrade
```

debes mantener sincronizados los assets administrados.

Existe:

```bash
gentle-ai sync
```

Antes puedes comprobar:

```bash
gentle-ai sync --dry-run
```

Gentle-AI recuerda qué agentes seleccionaste en:

```text
~/.gentle-ai/state.json
```

y solo sincroniza esos agentes, evitando modificar configuraciones de clientes que no autorizaste.

Mi rutina sería:

```bash
gentle-ai upgrade
gentle-ai sync
gentle-ai doctor
```

---

# 34. Cuando algo falla

Primero:

```bash
gentle-ai doctor
```

Es read-only.

Comprueba entre otras cosas:

```text
binarios
state.json
Engram
espacio en disco
configuración
```

y devuelve:

```text
PASS
WARN
FAIL
```

con posibles soluciones.

Después puedes comprobar:

```bash
gentle-ai version
```

y:

```bash
gentle-ai sync --dry-run
```

---

# 35. Qué NO deberías hacer

Aquí se resume buena parte de la filosofía del proyecto:

```text
❌ editar a mano ~/.codex/AGENTS.md administrado por Gentle-AI

❌ modificar manualmente skills generadas

❌ ejecutar sdd-init para cada feature

❌ utilizar SDD simplemente porque haya muchos archivos

❌ activar TDD solo porque existen tests

❌ reconstruir manualmente comandos internos de RDD

❌ interpretar Ready como autorización para hacer push

❌ versionar .atl/

❌ pedir explícitamente ODD cada vez

❌ crear planes enormes para bugs pequeños
```

En cambio:

```text
✅ describe el resultado que quieres

✅ deja ODD como flujo normal

✅ selecciona SDD conscientemente

✅ deja Engram trabajar automáticamente

✅ deja al registry cargar las skills

✅ usa RDD para revisar

✅ utiliza doctor para diagnosticar

✅ utiliza sync tras actualizar

✅ conserva control humano sobre delivery
```

Esto coincide prácticamente con la propia “Golden Rule” del proyecto: Gentle-AI configura el ecosistema y después intenta apartarse de tu camino.

---

# 36. Mi flujo recomendado para trabajar todos los días

Yo reduciría toda esta guía mentalmente a esto:

```text
┌──────────────────────────────────────────────┐
│ 1. Abres el repositorio                      │
│                                              │
│ 2. Abres Codex                               │
│                                              │
│ 3. Dices qué resultado quieres               │
│                                              │
│ 4. Gentle-AI utiliza ODD                     │
│                                              │
│ 5. Engram recupera contexto                  │
│                                              │
│ 6. Skill Registry selecciona skills          │
│                                              │
│ 7. Codex delega cuando compensa               │
│                                              │
│ 8. Se implementa + comprueba                 │
│                                              │
│ 9. RDD revisa cuando corresponde              │
│                                              │
│ 10. Recibes Ready / Needs your decision      │
│                                              │
│ 11. Tú decides push / PR / merge             │
└──────────────────────────────────────────────┘
```

Y solo sales de ese flujo cuando deliberadamente dices:

```text
"Usa SDD"
```

---

## Chuleta rápida

|Quiero…|Qué hago|
|---|---|
|Implementar algo|Describir la feature|
|Arreglar un bug|Describir el bug|
|Refactorizar|Pedir el refactor|
|Investigar|Pedir investigación|
|Usar TDD|Decir explícitamente que use TDD|
|Planificación formal|Pedir SDD|
|Ver memoria|`engram tui`|
|Compartir memoria|`engram sync`|
|Actualizar skills|`gentle-ai skill-registry refresh --force`|
|Ver skills|`gentle-ai skill-registry list`|
|Comprobar instalación|`gentle-ai doctor`|
|Ver RDD|`gentle-ai review mode status --cwd .`|
|Desactivar RDD localmente|`gentle-ai review mode disable --scope clone --cwd .`|
|Previsualizar actualización|`gentle-ai sync --dry-run`|
|Actualizar configuración|`gentle-ai sync`|

### Lo más importante de todo

Si solo recuerdas **tres reglas**, que sean estas:

1. **ODD es el flujo normal. No tienes que invocarlo.**
    
2. **SDD solo se utiliza cuando quieres explícitamente proposal/spec/design/tasks separados.**
    
3. **RDD revisa el código, pero tú sigues controlando push, PR, merge y release.**
    

Con esas tres ideas ya estás utilizando Gentle-AI como sus autores pretenden, y todo lo demás —Engram, skills, registry, delegación y receipts— funciona principalmente como infraestructura por debajo.