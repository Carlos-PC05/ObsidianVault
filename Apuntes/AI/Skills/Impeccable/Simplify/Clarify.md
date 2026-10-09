---
aliases:
  - Impeccable Clarify
  - Claridad de Interfaz
  - Simplificar Jerarquía
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/clarify/
---
# `/impeccable clarify` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable clarify` es el comando enfocado en **eliminar la ambigüedad, la confusión visual y la sobrecarga cognitiva** en la interfaz. Analiza vistas o componentes complejos para simplificar la presentación de la información, guiando al usuario de forma intuitiva hacia su objetivo principal mediante la reestructuración de mensajes, etiquetas y elementos de interacción.

---

## 🎯 Objetivo Principal

* **Reducir la Carga Cognitiva:** Simplifica interfaces saturadas reorganizando la información en bloques lógicos y fáciles de procesar.
* **Eliminar la Ambigüedad:** Clarifica textos de botones (CTA), etiquetas de formulario, mensajes de estado y micro-copy para evitar dudas en el usuario.
* **Reforzar el Enfoque Principal:** Destaca la acción primaria de la pantalla y subordina los elementos secundarios para evitar distracciones.

---

## 🔍 ¿Cuándo utilizarlo?

* **Formularios o Configuración Complejos:** Cuando una pantalla tiene demasiados campos, opciones o controles sin una jerarquía clara.
* **Mensajes e Interacciones Confusas:** Cuando los usuarios dudan sobre qué botón presionar o qué ocurrirá tras realizar una acción.
* **Vistas Densas en Datos:** Al presentar dashboards o paneles donde la información crítica queda oculta entre detalles secundarios.

---

## 📋 Flujo de Trabajo

1. **Análisis de Intención:** Evalúa la pantalla para determinar cuál es el objetivo principal del usuario en esa vista.
2. **Evaluación de Puntos de Fricción:** Identifica elementos confusos, etiquetas vagas, redundancias o exceso de opciones activas a la vez.
3. **Reestructuración y Clarificación:** Reescribe textos clave, reagrupa controles e implementa revelado progresivo (*progressive disclosure*) si es necesario.

---

## 💡 Principios Clave que Aplica

> [!check] Reglas de Claridad Impeccable
> * **Copy Intencional y Directo:** Reemplaza etiquetas genéricas como "Aceptar" o "Enviar" por acciones descriptivas como "Guardar Cambios" o "Crear Cuenta".
> * **Revelado Progresivo:** Muestra solo la información o controles necesarios en el momento actual, ocultando opciones avanzadas hasta que se soliciten.
> * **Unicidad Focal:** Garantiza que cada pantalla tenga una única acción primaria claramente diferenciada de las secundarias.

---

## 🚀 Uso Rápido

```bash
# Simplificar y clarificar la interfaz o mensajes de un componente
/impeccable clarify [componente o vista]
```
