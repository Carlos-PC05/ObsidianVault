---
aliases:
  - Impeccable Animate
  - Animaciones UI
  - Transiciones Impeccable
tags:
  - dev/ai
  - design-system
  - impeccable
  - animation
source: https://impeccable.style/docs/animate/
---
# `/impeccable animate` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable animate` es el comando especializado en **añadir e orquestar animaciones, microinteracciones y transiciones con propósito** en la interfaz. Evita los movimientos distractores o exagerados, aplicando curvas de aceleración (*easing*), duraciones y comportamientos dinámicos que mejoran la retroalimentación al usuario y la fluidez perceptiva de la aplicación.

---

## 🎯 Objetivo Principal

* **Animación Funcional:** Implementa microinteracciones que comunican estado, guían la atención del usuario y aportan fluidez sin recargar la experiencia.
* **Consistencia de Movimiento:** Define y aplica tokens de duración, curvas *easing* y comportamientos de transición coherentes en todo el sistema de diseño.
* **Respeto a la Accesibilidad:** Incluye soporte nativo para `prefers-reduced-motion`, asegurando que las animaciones puedan desactivarse o reducirse para usuarios con sensibilidad al movimiento.

---

## 🔍 ¿Cuándo utilizarlo?

* **Retroalimentación de Estado:** Al añadir respuestas visuales a acciones del usuario (hover en botones, estados activo/presionado, indicadores de carga).
* **Transiciones de Pantalla y Modales:** Para suavizar la entrada y salida de componentes emergentes, menús desplegables, pestañas o modales.
* **Carga de Contenido:** Al implementar animaciones de revelado progresivo (*stagger*) o esqueletos de carga (*skeleton loaders*).

---

## 📋 Flujo de Trabajo

1. **Análisis del Componente:** Evalúa los elementos interactivos o los cambios de estado presentes en el código.
2. **Selección del Movimiento:** Consulta las pautas de `DESIGN.md` para elegir la duración (p. ej., 150ms-300ms) y la curva adecuada según la física del componente.
3. **Aplicación e Integración:** Añade las clases CSS, utilidades de Tailwind o bibliotecas de animación (como Framer Motion o CSS Transitions) respetando la accesibilidad.

---

## ⚡ Principios Clave que Aplica

> [!check] Reglas de Movimiento Impeccable
> * **Propósito sobre Adorno:** Cada animación debe tener una razón de ser (confirmar una acción, mostrar una relación espacial o reducir la carga percibida de espera).
> * **Rapidez y Duración:** Mantiene las transiciones breves y reactivas para evitar la sensación de lentitud o retraso (*lag*) en la app.
> * **Respeto a `prefers-reduced-motion`:** Garantiza alternativas estáticas o atenuadas automáticamente cuando el usuario lo tenga configurado en el SO.

---

## 🚀 Uso Rápido

```bash
# Añadir o refinar animaciones en un componente específico
/impeccable animate [componente o archivo]
```
