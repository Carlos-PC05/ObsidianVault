---
alias:
  - /setup-matt-pocock-skills
  - Matt Pocock Skills Setup
tags:
  - ai/skills
  - dev/tools
  - workflow
  - ai/getting-started
source: https://www.aihero.dev/skills-setup-matt-pocock-skills
---
> **Comando:** `/setup-matt-pocock-skills`

---

## 📌 ¿Qué hace?
Es la **skill de configuración inicial** para un repositorio. Responde y configura 3 elementos clave:
1. **Gestor de tickets/issues** (dónde se almacenan las tareas).
2. **Etiquetas de triaje** (cómo se clasifican).
3. **Ubicación de la documentación de dominio**.

Guarda estas respuestas como archivos Markdown estáticos bajo la carpeta `docs/agents/`. De este modo, las demás skills leen esa configuración centralizada sin necesidad de ser modificadas.

---

## ⏱️ ¿Cuándo usarla?
- **Una vez por repositorio**, antes de usar cualquier otra skill de desarrollo (`/triage`, `/to-spec`, `/to-tickets`, etc.).
- No la invocan otras skills automáticamente (está marcada como *non-invokable*).
- Se puede ejecutar en repositorios existentes sin perder trabajo previo.

---

## 🗂️ Archivos que crea/modifica

| Archivo                        | Contenido                                                                    |
| :----------------------------- | :--------------------------------------------------------------------------- |
| `docs/agents/issue-tracker.md` | Configuración e instrucciones del gestor de tareas.                          |
| `docs/agents/domain.md`        | Ubicación de la documentación de arquitectura/dominio.                       |
| `docs/agents/triage-labels.md` | Mapeo de etiquetas (solo si la skill `/triage` está instalada).              |
| `CLAUDE.md` o `AGENTS.md`      | Añade un bloque `## Agent skills` enlazando a los archivos de configuración. |

---

## 🧭 Las 3 Decisiones del Setup

1. **Gestor de Issues (Issue Tracker):**
   - **GitHub Issues** *(usa `gh` CLI)*.
   - **Local Markdown** *(archivos bajo `.scratch/<feature>/` — ideal para proyectos locales sin remoto)*.
   - **Otro** *(Jira, Linear, Azure DevOps, Beads; describes el flujo en texto y las skills lo siguen)*.
2. **Etiquetas de Triaje:**
   - Define los equivalentes a las 5 funciones canónicas: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`.
3. **Estructura de Docs de Dominio:**
   - Define si el proyecto usa contexto único (`CONTEXT.md` + `docs/adr/`) o multicontexto (`CONTEXT-MAP.md` para monorrepos).

---

## 💡 Puntos Clave & Preguntas Frecuentes

> [!NOTE] Principios
> - **Independiente de la plataforma:** Funciona con GitHub, GitLab, Jira o Markdown local sin cambiar el código de las skills.
> - **Sin config global:** La configuración vive dentro de cada repositorio (`docs/agents/`).
> - **Config is death:** No permite guardar preferencias personales de tono/formato; estas van en el `CLAUDE.md` general.

- **¿Crea las etiquetas en GitHub/GitLab?**  
  ❌ No. Solo escribe el mapeo de nombres en `triage-labels.md`. Las etiquetas deben existir en el gestor antes de usar `/triage` o `/wayfinder`.
- **¿Qué pasa si uso Codex/otro agente en vez de Claude?**  
  Modifica `CLAUDE.md` si existe, o `AGENTS.md`. Si usas otro harness, puedes mover manualmente el bloque `## Agent skills` a tu archivo de instrucciones principal.
- **¿Cuándo volver a ejecutarla?**  
  Solo si cambias de gestor de tareas o si actualizas el paquete de skills a una versión con plantillas nuevas.

---

## ✅ ¿Cómo saber si funcionó?
- Existen los archivos `docs/agents/issue-tracker.md` y `docs/agents/domain.md`.
- Tu archivo `CLAUDE.md` o `AGENTS.md` incluye el bloque `## Agent skills`.
- Al ejecutar `/to-tickets` o `/triage`, el agente publica y clasifica **sin preguntarte dónde colocar las cosas ni inventar etiquetas**.
- Ningún archivo `.md` interno de las propias skills fue modificado.