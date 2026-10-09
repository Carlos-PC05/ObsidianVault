---
aliases:
  - Impeccable Colorize
  - Sistema de Color
  - Armonía Cromática
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/colorize/
---
# `/impeccable colorize` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable colorize` es el comando especializado en **diseñar, refinar y aplicar la paleta de colores** en la interfaz. Sustituye tonos genéricos o desalineados por un sistema de color intencional y armonioso, utilizando grises/negros tintados, acentos con significado funcional y garantizando niveles estrictos de contraste y accesibilidad (WCAG).

---

## 🎯 Objetivo Principal

* **Eliminar "AI Slop" Cromático:** Elimina los esquemas de color clichés generados por IA (como degradados púrpura/azul sin contexto o fondos oscuros planos).
* **Introducir Tintado de Color:** Aplica matices sutiles del color de marca a los tonos neutros (grises, fondos y superficies) para aportar coherencia y calidez visual.
* **Color Funcional y Semántico:** Asigna colores con propósito para estados (éxito, error, advertencia, información) e interacciones principales (acciones primarias vs. secundarias).

---

## 🔍 ¿Cuándo utilizarlo?

* **Gama de Colores Plana o Inconsistente:** Cuando los elementos de la interfaz usan grises puros (`#000000`, `#ffffff`, `#888888`) que hacen que el diseño se sienta frío o desarticulado.
* **Problemas de Contraste y Accesibilidad:** Cuando el texto o los iconos son difíciles de leer sobre sus fondos.
* **Implementación de Modo Oscuro/Claro:** Para estructurar y armonizar la transición entre temas asegurando la legibilidad en ambos.

---

## 📋 Flujo de Trabajo

1. **Lectura de Identidad:** Consulta los tokens de color primarios y secundarios definidos en `DESIGN.md` o `PRODUCT.md`.
2. **Auditoría Cromática:** Identifica valores *hardcodeados*, colores que incumplen contraste o zonas con falta de jerarquía semántica.
3. **Aplicación del Sistema:** Refactoriza las clases o variables de color aplicando superficies tintadas, contraste ajustado y acentos intencionales.

---

## 🎨 Principios Clave que Aplica

> [!check] Reglas de Color Impeccable
> * **Neutros Tintados:** Los fondos y grises llevan un pequeño porcentaje del matiz de la marca para mantener la armonía global.
> * **Garantía WCAG:** Asegura un ratio de contraste mínimo de 4.5:1 para texto normal y 3:1 para componentes y texto grande.
> * **Jerarquía de Acento:** Reserva el color más saturado/llamativo exclusivamente para la acción principal (CTA) y elementos focalmente críticos.

---

## 🚀 Uso Rápido

```bash
# Refinar la paleta y el uso del color en un componente o vista
/impeccable colorize [componente o archivo]
```
