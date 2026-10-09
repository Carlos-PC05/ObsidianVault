---
aliases:
  - Impeccable Typeset
  - Tipografía e Jerarquía
  - Escala Tipográfica
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/typeset/
---
# `/impeccable typeset` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable typeset` es el comando especializado en **perfeccionar la tipografía, la escala de textos y la jerarquía de lectura** de la interfaz. Ajusta tamaños, pesos de fuente (`font-weight`), alturas de línea (`line-height`), espaciados entre letras (`letter-spacing`) y anchos de columna para garantizar la máxima legibilidad, ritmo vertical y pulido visual.

---

## 🎯 Objetivo Principal

* **Jerarquía Clara de Lectura:** Define niveles inequívocos entre títulos (`h1`-`h6`), subtítulos, cuerpo de texto, etiquetas y textos secundarios.
* **Optimización de Legibilidad:** Configura la altura de línea y el ancho de bloque (*measure*) ideales para evitar líneas demasiado largas o apretadas.
* **Eliminación de Fuens Genéricas y Desalineadas:** Sustituye el uso desordenado o genérico de tipos de letra por las combinaciones y escalas tipográficas definidas en `DESIGN.md`.

---

## 🔍 ¿Cuándo utilizarlo?

* **Páginas Densas en Texto:** En artículos, documentación, blogs, paneles de detalles o tablas informativas.
* **Títulos y Encabezados "Planos":** Cuando los titulares no destacan o se confunden con el texto de cuerpo por falta de contraste en escala o peso.
* **Pobre Ritmo Vertical:** Cuando los saltos de línea y márgenes entre párrafos o encabezados se sienten desproporcionados o incoherentes.

---

## 📋 Flujo de Trabajo

1. **Lectura de Escala:** Consulta los tokens tipográficos y fuentes de marca registradas en `DESIGN.md`.
2. **Análisis Editorial:** Evalúa la estructura de los contenidos escritos del componente o página objetivo.
3. **Refactorización Tipográfica:** Aplica clases y reglas para armonizar tamaños, pesos, alturas de línea y márgenes entre bloques de texto.

---

## 📐 Principios Clave que Aplica

> [!check] Reglas Tipográficas Impeccable
> * **Escala Modular:** Utiliza proporciones consistentes para saltos de tamaño de fuente en lugar de px/rem arbitrarios.
> * **Ancho de Lectura Óptimo:** Limita la longitud de las líneas de texto entre 45 y 75 caracteres para reducir la fatiga de lectura.
> * **Altura de Línea Proporcional:** Aplica mayor `line-height` a textos pequeños/cuerpo y menor `line-height` a titulares grandes para evitar vacíos visuales.

---

## 🚀 Uso Rápido

```bash
# Perfeccionar la tipografía y jerarquía de texto en un componente o vista
/impeccable typeset [componente o archivo]
```
