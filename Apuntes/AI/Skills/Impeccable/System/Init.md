---
aliases:
  - Impeccable Init
  - Interfaz Init
  - Configuración Impeccable
tags:
  - dev/ai
  - design-system
  - impeccable
  - prompt-engineering
source: https://impeccable.style/docs/init/
---
# `/impeccable init` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable init` es el comando de configuración inicial (*one-time setup*) del skill de IA **Impeccable**. Realiza una "entrevista de diseño" al usuario y escanea el proyecto para generar contexto fundamental (`PRODUCT.md` y opcionalmente `DESIGN.md`). Esto previene el *"AI slop"* (diseños genéricos y cliché de IA) alineando todas las iteraciones futuras de código UI al sistema de diseño y la personalidad real de tu producto.

---

## 🎯 Objetivo Principal

* **Establecer la base visual y estratégica:** Captura audiencia, tono, paleta de colores, tipografía y restricciones antes de generar o pulir UI.
* **Escanear el código existente:** Analiza tokens, estilos globales (Tailwind/CSS) y patrones de componentes dentro de la base de código.
* **Memoria persistente:** Genera archivos Markdown en la raíz del proyecto que son leídos automáticamente por los demás comandos de la suite Impeccable (`/polish`, `/critique`, `/audit`, etc.).

---

## 📋 Flujo de Trabajo (Entrevista Init)

Al ejecutar el comando dentro de un agente de IA compatible (Cursor, Claude Code, Gemini CLI, etc.), sigue estas fases:

1. **Clasificación de Superficie:** Determina si el sitio/app es **Brand** (landing page, sitio corporativo, portfolio) o **Product** (SaaS, dashboard, app web).
2. **Contexto de Usuario:** Define público objetivo, casos de uso principales y problema a resolver.
3. **Personalidad & Tono:** Pide 3 adjetivos descriptivos de la marca junto con ejemplos a imitar y enfoques a evitar (*anti-patrones*).
4. **Extracción de Tokens:** Analiza la base de código para mapear componentes, colores y reglas tipográficas existentes.

---

## 📁 Archivos de Contexto Creados

> [!file] Archivos Generados en la Raíz del Proyecto
> * `PRODUCT.md`: Define el propósito del producto, audiencia, voz de marca y restricciones estratégicas.
> * `DESIGN.md`: Documenta la guía de estilo, tokens, paletas tintadas, tipografías y reglas de layout.

---

## 🚫 Reglas Anti "AI Slop"

* ❌ **Sin clichés estéticos:** Prohíbe fondos oscuros genéricos con degradados púrpura/azul, fuentes por defecto sin personalizar (Inter/Arial) y tarjetas anidadas sin jerarquía.
* 🎨 **Color y Contraste:** Obliga el uso de grises/negros tintados (con matices de color) y asegura niveles mínimos de accesibilidad (WCAG).

---

## 🚀 Uso Rápido

```bash
# 1. Instalar la skill en el proyecto
npx impeccable install

# 2. Ejecutar la entrevista dentro del agente de IA
/impeccable init
```
