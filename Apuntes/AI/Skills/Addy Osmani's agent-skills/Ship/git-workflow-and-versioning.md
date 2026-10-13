---
alias:
  - Skill git-workflow-and-versioning
  - Addy Osmani Git Workflow
  - Trunk Based Development
tags:
  - ai/skills
  - addy-osmani
  - ai/ship
  - dev/git
  - dev/workflow
source: https://github.com/addyosmani/agent-skills/blob/main/skills/git-workflow-and-versioning/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> **Git es tu red de seguridad.** Trata los commits como puntos de guardado, las ramas como arenas, y el historial como documentación. Con agentes de IA generando código a alta velocidad, el control de versiones disciplinado es lo que mantiene los cambios manejables, revisables y reversibles.

---

## 🎯 ¿Cuándo usarla?

**Siempre.** Todo cambio de código pasa por git. Concretamente: al commitear, al ramificar, al resolver conflictos, al **dividir trabajo sin commitear de un working tree messy en commits atómicos**, al abrir o revisar un PR, al hacer push, al organizar trabajo en flujos paralelos, al cortar un release, elegir un bump de versión semver, o escribir un changelog.

---

## 🌳 Trunk-Based Development (recomendado)

Mantén `main` **siempre desplegable**. Trabaja en ramas de feature cortas que se mergean en **1–3 días**. Las ramas de desarrollo largas son **costes ocultos**: divergen, crean conflictos de merge y retrasan la integración. La investigación DORA muestra consistentemente que trunk-based development correlaciona con equipos de alto rendimiento.

```
main ──●──●──●──●──●──●──●──●──●──  (siempre desplegable)
        ╲      ╱  ╲    ╱
         ●──●─╱    ●──╱    ← feature branches cortas (1-3 días)
```
Quien use gitflow o ramas largas puede adaptar los principios —**la disciplina de commits importa más que la estrategia de ramificación concreta**.

- **Las ramas dev son costes.** Cada día que vive una rama, acumula riesgo de merge.
- **Las ramas de release son aceptables** cuando necesitas estabilizar un release mientras `main` avanza.
- **Feature flags > ramas largas.** Prefiere desplegar trabajo incompleto tras flags a tenerlo en una rama semanas.

---

## 📏 Los principios

**1. Commitea temprano, commitea a menudo.** Cada incremento exitoso lleva su commit. Los commits son puntos de guardado: si el siguiente cambio rompe algo, **reviertes al último estado conocido-bueno al instante.**

**2. Commits atómicos.** Cada commit hace una sola cosa lógica.
```bash
# Bien: cada commit autocontenido
a1b2c3d Add task creation endpoint with validation
d4e5f6g Add task creation form component
h7i8j9k Connect form to API and add loading state
m1n2o3p Add task creation tests (unit + integration)

# Mal: todo mezclado
x1y2z3a Add task feature, fix sidebar, update deps, refactor utils
```

**3. Mensajes descriptivos** que explican el **por qué**, no solo el qué.
```
feat: add email validation to registration endpoint

Prevents invalid email formats from reaching the database.
Uses Zod schema validation at the route handler level,
consistent with existing validation patterns in auth.ts.
```
Formato: `<tipo>: <descripción corta>` + cuerpo opcional. Tipos: `feat` · `fix` · `refactor` · `test` · `docs` · `chore`.

**4. Mantén las preocupaciones separadas.** No mezcles cambios de formato con cambios de comportamiento. No mezcles refactors con features. **Separa el refactor del trabajo de feature** — son dos cambios distintos (limpiezas pequeñas como renombrar una variable sí pueden incluirse).

**5. Dimensiona tus cambios.** Objetivo **~100 líneas** por commit/PR. ~300 aceptable si es un cambio lógico único. **~1000 → divídelo.**

**Ramas:** una feature por rama · ramifica desde `main` · mantén cortas · **borra la rama tras el merge** · prefiere flags sobre ramas largas para features incompletas. Nomenclatura: `feature/` · `fix/` · `chore/` · `refactor/` + descripción corta.

---

## 🌲 Worktrees para trabajo paralelo

```bash
git worktree add ../proyecto-feature-a feature/task-creation
git worktree add ../proyecto-feature-b feature/user-settings
# Cada worktree es un directorio con su propia rama
# Al terminar: git worktree remove ../proyecto-feature-a
```
Permite que **múltiples agentes trabajen en features a la vez sin interferir**, sin cambiar de rama, y que si un experimento falla, borres el worktree sin perder nada.

> [!tip] El patrón de punto de guardado
> Cambia → test pasa → **commit** y sigue · test falla → **revierte al último commit** e investiga. Nunca pierdes más de un incremento de trabajo. Si el agente se descarría, `git reset --hard HEAD` te devuelve al último estado exitoso.

## 📋 Resúmenes de cambio

Después de cualquier modificación, da un resumen estructurado: **cambios realizados** (fichero: qué) · **cosas que NO tocaste (a propósito)** · **preocupaciones potenciales**. La sección *"DIDN'T TOUCH"* es especialmente importante: demuestra que ejercitaste la disciplina de alcance y no te pusiste a reformar por tu cuenta.

## 🧼 Higiene pre-commit

```bash
git diff --staged                                    # 1. qué vas a commitear
git diff --staged | grep -i "password\|secret\|api_key\|token"   # 2. sin secretos
npm test                                             # 3. tests
npm run lint                                         # 4. lint
npx tsc --noEmit                                     # 5. tipos
```
Automatízalo con git hooks (`lint-staged` + `husky`).

**Ficheros generados:** commitea los que el proyecto espera (`package-lock.json`, migraciones de Prisma). **No** commitees build output (`dist/`, `.next/`), ficheros de entorno (`.env`) ni config de IDE (salvo que sea compartida). `.gitignore` debe cubrir `node_modules/`, `dist/`, `.env`, `.env.local`, `*.pem`.

**Git para depurar:** `git bisect` (encontrar el commit que introdujo un bug) · `git log --oneline -20` · `git diff HEAD~5..HEAD -- src/` · `git blame` (quién tocó una línea) · `git log --grep="validation"`.

---

## 🏷️ Release y versionado

> [!important] Los commits son para *ti*; una **versión** es para *tus consumidores*
> En cuanto algo más depende de tu código —otro equipo, un paquete publicado, un cliente desplegado—, *"lo último en main"* deja de responder a *"¿qué estoy ejecutando y es seguro subir?"*.

**Semantic Versioning** para todo lo que tenga consumidores:
```
MAJOR  cambio incompatible — los consumidores deben cambiar su código
MINOR  funcionalidad nueva, retrocompatible — seguro de subir
PATCH  corrección de bug, retrocompatible — seguro de subir
```
El número es una promesa, así que **haz que el código case con ella**. Un *"patch"* que cambia un comportamiento del que dependían los consumidores es un cambio major disfrazado (Hyrum's Law — ver [[api-and-interface-design]]). **Cuando dudes si un cambio es incompatible, assume que lo es**: un major sorpresa sale mucho más barato que un consumidor roto.

**Etiqueta el release, y deja que el tag sea la fuente de verdad.** Un release es un punto inmutable en la historia, no una rama móvil. **Deriva la versión del tag** en vez de editarla a mano en ficheros dispersos, para que artefacto, tag y changelog nunca discrepen.

**Changelog escrito para humanos.** *No es `git log`.* Es la respuesta curada y orientada al consumidor a *"¿qué cambió y me importa?"*, agrupada por `Added / Changed / Fixed / Deprecated / Removed / Security`, **lo más reciente arriba**, y **cada entrada redactada en torno al impacto en el usuario, no a la mecánica interna**. Escribe la entrada **en el mismo cambio que hace el cambio**, mientras el impacto está fresco — no la reconstruyas por arqueología de commits en el momento del release.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Commitearé cuando la feature esté hecha" | Un commit gigante es imposible de revisar, depurar o revertir |
| "El mensaje no importa" | Los mensajes son documentación |
| "Lo aplasto todo (squash) después" | Aplastar destruye la narrativa de desarrollo. Prefiere commits incrementales limpios desde el principio |
| "Las ramas añaden overhead" | Las ramas cortas son gratis. Las largas son el problema — mergea en 1–3 días |
| "No necesito `.gitignore`" | Hasta que un `.env` con secretos de producción se commitea |
| "Es un fix pequeño, bumpeo patch" | **Revisa qué pueden observar los consumidores.** Un cambio de comportamiento del que dependían es major, sea cual sea el tamaño del diff |
| "El changelog es el log de commits" | Los commits son para ti; el changelog es para consumidores, curado por impacto |
| "Escribimos el changelog en el release" | Para entonces el impacto se reconstruye de memoria y falta la mitad |

---

## 🚩 Red Flags

- Cambios grandes sin commitear acumulándose.
- Mensajes de commit como *"fix"*, *"update"*, *"misc"*.
- Cambios de formato mezclados con cambios de comportamiento.
- Sin `.gitignore`.
- Commiteando `node_modules/`, `.env`, o artefactos de build.
- Ramas longevas que divergen significativamente de `main`.
- Force-push a ramas compartidas.
- Un cambio incompatible bajo un bump minor o patch.
- Un release sin tag, o versión editada a mano desincronizada del tag.
- Un release user-facing sin entrada de changelog, o un changelog que es un volcado de commits.

---

## 🔗 Relaciones

**En el flujo de Addy:** se usa en **cada incremento** de [[incremental-implementation]]. Sus reglas de versionado alimentan [[deprecation-and-migration]] (ventanas de deprecación) y [[shipping-and-launch]] (el release en sí).

**Equivalencia Matt Pocock:** **no tiene nota equivalente.** Matt no cubre git ni versionado; su pack asume el control de versiones como algo dado. Addy aporta aquí **trunk-based development, semver como contrato, y el changelog como documento para consumidores**.

```bash
npx skills add addyosmani/agent-skills --skill git-workflow-and-versioning
```
