---
aliases:
  - Instalar Taste Skill
  - Instalación tasteskill
  - npx skills add taste-skill
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - setup
source: https://www.tasteskill.dev/docs#installation
---
# Instalación — Taste Skill

> [!abstract] Resumen Ejecutivo
> Taste Skill se instala como un archivo `SKILL.md` dentro del proyecto mediante `npx skills add`. Funciona con cualquier agente que sepa leer skills. El install por defecto trae **v2 experimental**; también existen la v1 legacy y el bundle completo con todas las variantes.

---

## 🎯 Objetivo Principal

* **Añadir la skill al proyecto:** un `SKILL.md` que el agente lee automáticamente, sin configuración extra.
* **Elegir variante:** v2 por defecto, v1 si un flujo antiguo depende de su comportamiento exacto, bundle completo si quieres todas.
* **Preparar el entorno de los scripts:** Node 22+, navegador Chromium y red para la búsqueda de imágenes.

---

## 📋 Requisitos

| Requisito | Detalle |
| :--- | :--- |
| Agente | Cursor, Claude Code, Codex, Antigravity, Gemini CLI, AI Studio, v0, Lovable, OpenCode, OpenClaw, Windsurf, Copilot o cualquiera que soporte `SKILL.md`. |
| Node | **22+** para `draw.mjs`, `inspect.mjs`, `scenes.mjs` y `assets.mjs`. |
| Navegador | Chrome, Edge o Chromium instalado para el inspector. |
| Red | Necesaria para `assets.mjs` (Openverse, Wikimedia Commons, Art Institute of Chicago, Met). No usa API keys. |

---

## 🚀 Instalación

### v2 experimental (recomendada)

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"
```

Añade un `SKILL.md` al proyecto. El agente lo lee automáticamente.

### v1 legacy

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend-v1"
```

Solo si v2 rompe algo específico de tu flujo.

### Bundle completo

```bash
npx skills add Leonxlnx/taste-skill
```

Instala todas las skills del repo.

### Codex

```bash
npx skills add Leonxlnx/taste-skill -a codex
```

Instala el bundle en el directorio de skills de Codex.

### Sin CLI

También puedes copiar cualquier `SKILL.md` al proyecto o pegarlo en una conversación de ChatGPT o Codex.

---

## 📁 Dónde queda

> [!file] Archivo instalado
> `SKILL.md` en la raíz del proyecto (o en la carpeta de skills del agente). El agente lo lee en cada ejecución, así que cualquier edición se aplica en la siguiente generación.

---

## ⚙️ Personalización

* El `SKILL.md` es **totalmente editable**: puedes añadir, quitar o modificar reglas.
* Si mantienes una guía de estilo propia, pégala al principio del `SKILL.md`; el agente la usará como fuente de verdad dominante.
* Para conservar marca o tipografía existentes, se pasa `--keep palette,type` al script de dirección.

---

## ⚠️ Estabilidad

> [!warning] v2 experimental
> El install por defecto es v2. El nombre de instalación (`design-taste-frontend`) se mantiene estable, pero la numeración de secciones y la redacción de reglas pueden cambiar antes de v2.0.0 stable.

---

## 🔗 Siguiente paso

Cargar la skill una vez al inicio de la sesión y usar uno de los dos prompts de [[Prompts]]: greenfield o rediseño.
