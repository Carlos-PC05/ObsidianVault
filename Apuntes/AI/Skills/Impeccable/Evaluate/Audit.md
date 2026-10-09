---
aliases:
  - Impeccable Audit
  - Auditoría de UI
  - Revisión de Diseño
tags:
  - dev/ai
  - design-system
  - impeccable
  - code-review
  - ui-ux
source: https://impeccable.style/docs/audit/
---
# `/impeccable audit` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable audit` es el comando encargado de **realizar una revisión exhaustiva y diagnóstica** del código de interfaz existente. Evalúa la calidad visual, la coherencia con el sistema de diseño (`DESIGN.md`), la accesibilidad (WCAG) y la presencia de *"AI slop"* o antipatrones de UI, entregando un informe detallado con hallazgos y un plan de corrección estructurado.

---

## 🎯 Objetivo Principal

* **Diagnóstico de Calidad Visual:** Inspecciona el código fuente en busca de inconsistencias de diseño, tokens no utilizados y malas prácticas estéticas.
* **Verificación de Accesibilidad:** Detecta problemas de contraste de color, faltas de semántica HTML y fallos de usabilidad.
* **Control de Sistema de Diseño:** Revisa que no existan valores *hardcodeados* (colores, espaciados, bordes) que eludan los tokens definidos en `DESIGN.md`.

---

## 🔍 ¿Cuándo utilizarlo?

* **Antes de un Release/PR:** Para asegurar que los nuevos componentes o vistas cumplan con los estándares de calidad del proyecto.
* **Evaluación de Código Legado:** Al asumir un proyecto o componente antiguo para identificar deuda técnica de UI.
* **Detección de Clichés de IA:** Para encontrar y catalogar degradados genéricos, malas jerarquías o estructuras sobrecargadas producidas por agentes de IA.

---

## 📋 Flujo de Trabajo

1. **Escaneo del Código:** La IA analiza los archivos objetivo (componentes, páginas o hojas de estilo).
2. **Cruce con Contexto:** Compara el código contra las reglas de `PRODUCT.md`, `DESIGN.md` y los estándares globales de Impeccable.
3. **Generación del Reporte:** Presenta un desglose claro organizado por gravedad (crítico, advertencia, sugerencia) sin modificar el código directamente.

---

## 📋 Áreas de Inspección

> [!check] Qué evalúa el Reporte
> * **Color & Contraste:** Cumplimiento de WCAG AA/AAA, uso de negros/grises tintados y paleta consistente.
> * **Tipografía & Jerarquía:** Escala tipográfica correcta, pesos legibles y ritmo vertical.
> * **Espaciado & Layout:** Alineación a la rejilla, consistencia en *paddings/margins* y comportamiento responsive.
> * **Fidelidad al Sistema:** Sustitución de valores arbitrarios por tokens y componentes reutilizables.

---

## 🚀 Uso Rápido

```bash
# Auditar un componente o ruta específica
/impeccable audit [archivo o carpeta]
```
