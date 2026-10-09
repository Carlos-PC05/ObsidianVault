---
aliases:
  - Impeccable Layout
  - Estructura y Espaciado
  - Grid y Composición
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/layout/
---
# `/impeccable layout` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable layout` es el comando especializado en **organizar, estructurar y armonizar la maquetación espacial** de la interfaz. Ajusta rejillas (*grids*), alineaciones, contenedores, ritmos verticales y márgenes responsive para garantizar que la composición sea fluida, legible y mantenga una jerarquía espacial impecable en cualquier tamaño de pantalla.

---

## 🎯 Objetivo Principal

* **Estructura y Ritmo Espacial:** Aplica escalas de espaciado consistentes para evitar paddings o márgenes arbitrarios o desproporcionados.
* **Diseño Responsive Fluido:** Garantiza que los contenedores y elementos flex/grid se adapten de forma natural a distintas resoluciones sin romper la experiencia.
* **Organización Visual:** Mejora la relación de agrupadmiento y proximidad entre elementos (*Leyes de la Gestalt*) para que la lectura sea intuitiva.

---

## 🔍 ¿Cuándo utilizarlo?

* **Pantallas Desalineadas o Sobrecargadas:** Cuando los elementos visuales se sienten amontonados, dispersos o sin un alineamiento claro.
* **Fallas Responsive:** Cuando la vista se rompe en pantallas pequeñas o se estira de forma antiestética en monitores ultra-anchos.
* **Refactorización de Rejillas:** Al estructurar dashboards, listas de tarjetas, formularios complejos o vistas multicolumna.

---

## 📋 Flujo de Trabajo

1. **Lectura de Sistema:** Consulta las reglas de espaciado y layout configuradas en `DESIGN.md` (p. ej. escala basada en 4px/8px).
2. **Análisis de Proximidad:** Examina el componente o vista objetivo para identificar zonas con mala distribución espacial.
3. **Ajuste y Estructuración:** Aplica contenedores, utilidades Flexbox/Grid y reglas de *responsive design* para equilibrar la composición.

---

## 📐 Principios Clave que Aplica

> [!check] Reglas de Layout Impeccable
> * **Escala Espacial Unificada:** Reemplaza valores *hardcodeados* (como `margin: 13px`) por la escala estándar del sistema de diseño.
> * **Ley de Proximidad:** Mantiene los elementos relacionados visualmente más cerca entre sí que de otros bloques de información.
> * **Límites de Ancho (*Max-Width*):** Evita que las líneas de texto o contenedores se expandan indefinidamente, protegiendo la legibilidad.

---

## 🚀 Uso Rápido

```bash
# Corregir y optimizar la maquetación de un componente o página
/impeccable layout [componente o archivo]
```
