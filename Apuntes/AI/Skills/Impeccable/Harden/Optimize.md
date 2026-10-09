---
aliases:
  - Impeccable Optimize
  - Optimización de UI
  - Rendimiento Visual
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/optimize/
---
# `/impeccable optimize` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable optimize` es el comando enfocado en **maximizar la velocidad, la fluidez y la eficiencia de renderizado** de la interfaz. Inspecciona el código de componentes en busca de refinamientos en el marcado, manejo inteligente de activos visuales, reducción de re-renders innecesarios y optimización de CSS para lograr una UI ligera y altamente reactiva.

---

## 🎯 Objetivo Principal

* **Eficiencia de Renderizado:** Elimina estructuras DOM sobrecargadas o anidamientos excesivos que ralentizan el dibujado de la pantalla (*layout thrashing*).
* **Gestión de Recursos e Imágenes:** Asegura el uso correcto de formatos eficientes (como WebP/AVIF), estrategias de carga diferida (*lazy loading*) y dimensiones explícitas para evitar saltos de maquetación (*Cumulative Layout Shift - CLS*).
* **Fluidez en Interacciones:** Optimiza animaciones y transiciones para que se ejecuten mediante aceleración por hardware (`transform`, `opacity`) a 60 FPS estables.

---

## 🔍 ¿Cuándo utilizarlo?

* **Lentitud o Parpadeos en la UI:** Cuando la interfaz se siente pesada, tarda en responder al scroll o experimenta caídas de frames durante transiciones.
* **Manejo de Listas o Tablas Densas:** Al trabajar con componentes que renderizan un gran número de elementos simultáneamente.
* **Cumplimiento de Core Web Vitals:** Para corregir problemas de LCP (Largest Contentful Paint) o CLS derivados de componentes mal estructurados.

---

## 📋 Flujo de Trabajo

1. **Análisis de Rendimiento UI:** Inspecciona el marcado HTML/JSX, las clases CSS y la lógica de estado del componente.
2. **Identificación de Cuellos de Botella:** Detecta imágenes sin optimizar, estilos repintados costosos (`box-shadow` complejos, `backdrop-filter` mal aplicados) y nodos DOM redundantes.
3. **Refactorización Eficiente:** Simplifica el árbol de elementos, optimiza consultas de estilos y aplica técnicas avanzadas de carga e interacción fluida.

---

## ⚡ Principios Clave que Aplica

> [!check] Reglas de Optimización Impeccable
> * **Zero Layout Shift (CLS 0):** Asigna espacios reservados y ratios de aspecto explícitos para evitar desplazamientos al cargar contenido o recursos.
> * **Propiedades de Alto Rendimiento:** Prioriza animaciones basadas exclusivamente en `transform` y `opacity` para delegar el trabajo a la GPU.
> * **Estructura DOM Magra:** Reemplaza capas de divs innecesarias (*wrapper hell*) por maquetaciones directas utilizando CSS Grid o Flexbox.

---

## 🚀 Uso Rápido

```bash
# Optimizar el rendimiento y eficiencia visual de un componente o vista
/impeccable optimize [componente o archivo]
```
