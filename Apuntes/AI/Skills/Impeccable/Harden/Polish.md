---
aliases:
  - Impeccable Polish
  - Pulido de UI
  - Acabado Profesional
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/polish/
---
# `/impeccable polish` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable polish` es el comando dedicado a **aplicar los retoques finales y perfeccionar cada detalle visual y de interacción** de la interfaz. Actúa como el filtro de calidad definitivo que ajusta alineaciones milimétricas, consistencia de micro-espaciados, estados interactivos y finuras estéticas para elevar una UI funcional a un acabado de nivel profesional.

---

## 🎯 Objetivo Principal

* **Refinamiento Meticuloso:** Corrige pequeñas desalineaciones, inconsistencias de *padding/margin* y desequilibrios ópticos.
* **Perfeccionamiento de Estados:** Garantiza que todos los elementos interactivos cuenten con estados claros y elegantes (`hover`, `focus-visible`, `active`, `disabled`).
* **Elevación de Calidad Perceptiva:** Aplica ese "1% extra" de cuidado en sombras, transparencias y bordes que distingue a una app común de una aplicación de primer nivel.

---

## 🔍 ¿Cuándo utilizarlo?

* **Fase Final de Implementación:** Cuando un componente o pantalla ya está construido y funciona bien, pero necesita ese toque final de elegancia visual.
* **Revisión Pre-Entrega:** Para asegurar que nada luzca "casi bien", sino impecable antes de enviar un PR o hacer un release.
* **Ajustes de Interacción Sutiles:** Cuando los botones o tarjetas responden a las acciones del usuario de forma brusca o incoherente.

---

## 📋 Flujo de Trabajo

1. **Inspección de Detalle:** Revisa minuciosamente los estilos del componente en busca de desalineaciones o incoherencias visuales menores.
2. **Consultar Estándares:** Verifica los tokens de `DESIGN.md` para aplicar los valores exactos de micro-espaciado, colores de foco y transiciones de estado.
3. **Ajuste Fino:** Aplica retoques precisos en tipografía, alineaciones ópticas, bordes y estados de interacción sin alterar la maquetación estructural.

---

## 💡 ¿En qué se diferencia de `/impeccable audit`?

| Comando | Intención | Resultado |
| :--- | :--- | :--- |
| `/impeccable audit` | **Diagnosticar:** Evalúa el código y entrega un informe de errores, accesibilidad e inconsistencias. | Un reporte de hallazgos y sugerencias. |
| `/impeccable polish` | **Ejecutar y Perfeccionar:** Modifica directamente el código para corregir detalles y elevar la calidad visual. | Un componente o vista con acabado profesional y pulido. |

---

## 🚀 Uso Rápido

```bash
# Perfeccionar y pulir los detalles finales de un componente o archivo
/impeccable polish [componente o archivo]
```
