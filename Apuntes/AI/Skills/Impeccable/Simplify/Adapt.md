---
aliases:
  - Impeccable Adapt
  - Adaptabilidad Responsive
  - Diseño Adaptativo
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/adapt/
---
# `/impeccable adapt` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable adapt` es el comando especializado en **optimizar la adaptabilidad y el comportamiento responsive** de la interfaz. Garantiza que los componentes, layouts y vistas se ajusten de manera fluida y elegante a cualquier tamaño de pantalla, dispositivo (móvil, tablet, desktop, pantallas ultra-anchas) o modo de interacción (táctil vs. ratón).

---

## 🎯 Objetivo Principal

* **Adaptabilidad Multidispositivo:** Asegura una experiencia de usuario consistente tanto en dispositivos móviles pequeños como en monitores de alta resolución.
* **Optimización de Interacción:** Ajusta áreas de toque (*touch targets*), comportamientos de navegación y visibilidad de elementos según el contexto del dispositivo.
* **Manejo Inteligente de Breakpoints:** Aplica patrones responsive modernos (p. ej., Flexbox, CSS Grid, unidades fluidas) sin depender exclusivamente de saltos de pantalla rígidos.

---

## 🔍 ¿Cuándo utilizarlo?

* **Fallas en Dispositivos Móviles:** Cuando elementos de la pantalla se desbordan, el texto se vuelve ilegible o las tablas/vistas complejas se rompen en móviles.
* **Desperdicio de Espacio en Pantallas Grandes:** Cuando la interfaz se estira excesivamente o deja grandes vacíos en monitores de escritorio.
* **Adaptación de Navegación:** Para transformar menús complejos en cajones de navegación (*drawers*), barras inferiores (*bottom bars*) o hamburguesas según el espacio disponible.

---

## 📋 Flujo de Trabajo

1. **Evaluación de Puntos de Ruptura (*Breakpoints*):** Analiza el comportamiento actual del componente en diferentes dimensiones de viewport.
2. **Aplicación de Patrones Responsive:** Reestructura la maquetación aplicando columnas apilables, tamaños fluidos e visibilidad condicional.
3. **Verificación de Usabilidad Táctil:** Garantiza que los botones e inputs tengan áreas de clic/toque adecuadas en pantallas táctiles (mínimo 44x44px).

---

## 📐 Principios Clave que Aplica

> [!check] Reglas de Adaptabilidad Impeccable
> * **Mobile-First / Fluid Design:** Diseña pensando en la fluidez del contenido antes que en tamaños de pantalla fijos.
> * **Preservación de Jerarquía:** Asegura que los elementos críticos de la interfaz permanezcan accesibles y visibles en dispositivos pequeños.
> * **Ergonomía Táctil:** Aumenta espaciados y tamaños de botones en modos móviles para prevenir toques accidentales.

---

## 🚀 Uso Rápido

```bash
# Optimizar el comportamiento responsive de un componente o vista
/impeccable adapt [componente o archivo]
```
