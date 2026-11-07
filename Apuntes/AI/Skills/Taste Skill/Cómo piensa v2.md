---
aliases:
  - Cómo piensa Taste Skill
  - Taste Skill v2
  - Brief inference
  - Section 0
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - anti-slop
source: https://www.tasteskill.dev/docs#how-it-works
---
# Cómo piensa v2 — Taste Skill

> [!abstract] Resumen Ejecutivo
> v2 no empieza escribiendo HTML. Primero **lee el brief** e infiere la dirección correcta a partir de señales como "minimalista", "editorial", "SaaS" o "portfolio". Después declara una lectura de diseño en una línea y elige composición, tipografía, paleta y movimiento. La documentación resume este comportamiento en varias secciones numeradas del `SKILL.md`.

---

## 🎯 Objetivo Principal

* **Leer antes de generar:** página, vibe, audiencia, referencias y activos de marca antes de tocar código.
* **Declarar una lectura:** una frase de diseño en lugar de caer en el degradado púrpura y las tres tarjetas centradas.
* **Mapear el brief a un sistema:** si el brief suena a Material, Fluent, Carbon, Polaris, Atlassian, Primer, GOV.UK, USWDS, Bootstrap, Radix, shadcn o Tailwind, usar el paquete oficial.
* **Auditar antes de rediseñar:** en proyectos existentes, primero se registra el estado actual y se declara el modo.

---

## 🧩 Secciones que resumen el comportamiento

| Sección | Qué hace |
| :--- | :--- |
| **0 · Brief inference** | Lee tipo de página, vibe words, audiencia, referencias y activos de marca. Declara una lectura de diseño de una línea. |
| **2 · Brief to design system map** | Si el brief encaja con un sistema conocido, usa el paquete oficial. Si es solo estética, lo etiqueta honestamente como aproximación web. |
| **4 · The locks** | Tres reglas que nunca se relajan: color, forma y tema. Ver [[Locks y Hero]]. |
| **5 · Animation skeletons** | Patrones canónicos para sticky-stack, horizontal-pan y scroll-reveal stagger. Fuera los scroll listeners hechos a mano y los bucles rAF que tocan estado de React. |
| **8 · Dark mode protocol** | Dual-mode por defecto. WCAG AA, paridad de jerarquía y fidelidad de marca en ambos temas. Off-black y off-white, nunca valores puros. |
| **9 · Big bans** | Lista estricta de patrones que hacen que los frontends generados se parezcan entre sí. Ver [[Anti-Slop]]. |
| **11 · Redesign protocol** | Auditoría primero. Modos greenfield, preserve u overhaul. La estructura de URLs, las etiquetas de navegación y los nombres de campos de formulario nunca cambian en silencio. |
| **12 · Block library schema** | Esquema de bloques reales y con fuente: hero, feature, social-proof, pricing, CTA, footer, portfolio, transition, navigation. |
| **14 · Pre-flight check** | Cada checkbox debe pasar honestamente antes de entregar. Sin checkbox, no hay entrega. |

---

## 🧠 La lógica del brief

> [!important] El brief manda
> v2 infiere la **varianza de layout**, el **movimiento** y la **densidad** de palabras como "minimalist", "editorial", "SaaS" o "portfolio". No aplica la misma receta a un restaurante que a un dashboard.

Si el brief es vago, el agente decide y declara su lectura en una línea. Solo pregunta cuando falta un dato que bloquea el trabajo, por ejemplo el nombre del producto.

---

## 🎨 Del brief al sistema de diseño

* **Brief con sistema conocido:** Material, Fluent, Carbon, Polaris, Atlassian, Primer, GOV.UK, USWDS, Bootstrap, Radix, shadcn o Tailwind → se usa el paquete oficial.
* **Brief solo estético:** se declara como aproximación web honesta, no como implementación oficial.
* **Marca existente:** se pasa `--keep palette,type` y se mapean los valores de marca a los roles del sheet.

---

## 🎬 Movimiento y modo oscuro

* Los esqueletos de animación son canónicos: sticky-stack, horizontal-pan y scroll-reveal stagger.
* `window.addEventListener('scroll')` está prohibido; se usa Motion `useScroll`, GSAP ScrollTrigger, IntersectionObserver o CSS scroll-driven animations.
* Dark mode dual por defecto: contraste WCAG AA, misma jerarquía y misma marca en los dos temas.
* Off-black y off-white, nunca `#000` ni `#fff` puros.

---

## 🔁 Rediseño

> [!warning] No rompas lo que funciona
> En un rediseño, el protocolo es auditoría primero. Se registra el antes, se declara el modo (**Preserve**, **Overhaul** o **Greenfield-with-content-preserved**) y solo después se implementa. Las URLs, las etiquetas de navegación y los nombres de campos no cambian sin aprobación explícita.

---

## ✅ Pre-flight

Antes de entregar, cada casilla de la Section 14 debe marcarse como **Pass** o **Fail** con una justificación de una línea. Cualquier **Fail** bloquea la entrega.

---

## 🔗 Relacionado

* [[03 Sortear la dirección]]
* [[04 Encontrar la idea]]
* [[Anti-Slop]]
* [[Locks y Hero]]
* [[Casos Especiales]]
