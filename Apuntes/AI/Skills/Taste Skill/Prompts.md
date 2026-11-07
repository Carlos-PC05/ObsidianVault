---
aliases:
  - Taste Skill Prompts
  - Prompts v2
  - Prompt greenfield
  - Prompt rediseño
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - prompt-engineering
source: https://www.tasteskill.dev/guide
---
# Prompts — Taste Skill

> [!abstract] Resumen Ejecutivo
> La guía oficial trae dos prompts listos para pegar en v2: uno para **crear una web nueva** y otro para **rediseñar una existente**. Ambos incluyen las auditorías que la skill espera. Se carga la skill una vez al inicio de la sesión y luego se usa uno de los dos.

---

## 🎯 Objetivo Principal

* **Arrancar sin ambigüedad:** el brief se rellena antes de ejecutar.
* **Incluir las auditorías:** em-dash, pre-flight, repetición de layouts y disciplina del hero.
* **Parar en los puntos de control:** el agente declara y espera OK antes de construir.

---

## 1️⃣ Crear una web nueva

Para un build desde cero. Pega, rellena el brief, ejecuta.

```text
I have loaded tasteskill v2 (experimental) as my only source of design rules.

Brief:
- Page kind: <landing / portfolio / marketing>
- Product: <name and one-line description>
- Audience: <who reads this, concrete adjectives>
- Vibe words: <2 to 4 concrete adjectives, e.g. "minimalist, editorial, restrained">
- References: <real URLs or product names that anchor the aesthetic>
- Avoid: <explicit slop patterns the brief should NOT default to>

Step 1. Declare your design read in one sentence and the three dial values with one-line reasoning each. Stop.

Step 2 (after my OK). Ship a single Next.js page with at least 8 sections. Pick the sections that actually fit the product. At least 4 different layout families across the page. Use real images (gen-tool first, then Picsum-seed). Lock one theme for the whole page.

Step 3. Run in writing:
- Em-dash audit (zero em-dashes U+2014 or en-dashes U+2013 anywhere)
- Pre-Flight Check (Section 14, every box marked Pass or Fail with one-line justification)
- Section-Layout-Repetition audit (list each section's layout family)
- Hero discipline audit (headline lines, subtext words, CTA visibility)

Any Fail blocks completion.
```

---

## 2️⃣ Rediseñar una web existente

Para un sitio existente. El agente audita primero, declara el modo y luego implementa sin romper URLs ni marca.

```text
I have loaded tasteskill v2 (experimental) as my only source of design rules.

Brief:
- Site: <URL or repo path>
- Mode: <preserve brand / overhaul / unsure>
- Audience: <who reads this>
- What works today: <2 to 3 specifics you want kept>
- What is broken today: <2 to 3 specifics you want fixed>
- SEO constraint: <which routes, headings, or anchors must not change>

Step 1. Run the Section 11 audit (Section 11.B in the skill):
- Brand tokens currently in use (primary, accent, type stack, radii)
- Information architecture (page tree, nav, conversion paths)
- Patterns to preserve (signature interactions, recognisable hero, copy voice)
- Patterns to retire (slop tells, broken layouts, dead links)
- Inferred dial reading of the current site (DESIGN_VARIANCE, MOTION_INTENSITY, VISUAL_DENSITY)
- SEO baseline (ranking pages, titles, anchors)
Post the audit in writing. Stop.

Step 2 (after my OK). Declare the mode (Preserve, Overhaul, or Greenfield-with-content-preserved) and which modernisation levers from Section 11.D you will apply, in priority order. Stop.

Step 3 (after my OK). Implement the changes. Keep URL structure, primary nav labels, form field names, brand logo, and legal copy unchanged unless I explicitly approve a change.

Step 4. Run in writing:
- Em-dash audit
- Pre-Flight Check (Section 14)
- Preservation audit: list every URL, nav label, form field, and anchor changed. Should be empty unless I approved.
- Brand fidelity audit: confirm the existing brand accent color, type stack, and logo treatment survived the redesign.

Any Fail blocks completion.
```

---

## 📌 Recordatorios rápidos

* **Cero em-dashes** en cualquier parte. Solo guion.
* Titular del hero **máximo 2 líneas**. Subtexto **máximo 20 palabras**. CTA visible **sin scroll**.
* Navegación **máximo 80 px** de alto, una línea en desktop.
* Bento grid: **N ítems = N celdas**. Sin celdas vacías.
* **Un tema** para toda la página, sin cambios light/dark a mitad.
* Imágenes reales, nada de screenshots falsos hechos con divs, nada de ilustraciones SVG hechas a mano.
* Sin eyebrows con numeración, sin etiquetas de versión en el hero, sin scroll cues, sin tiras de locale, sin puntos de estado decorativos.
* Si `MOTION_INTENSITY` es mayor que 4, la página anima de verdad. Si no, quita el dial.

---

## 🔗 Relacionado

* [[Taste Skill]]
* [[Instalación]]
* [[03 Sortear la dirección]]
* [[07 Gate]]
* [[10 Informe]]
