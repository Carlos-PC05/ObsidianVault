---
aliases:
  - Impeccable CLI
  - Comando Impeccable
  - Flujo Impeccable
tags:
  - dev/ai
  - design-system
  - impeccable
  - prompt-engineering
source: https://impeccable.style/docs/impeccable/
---
# `/impeccable` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable` es el **comando principal y punto de entrada general** de la suite Impeccable. Funciona como un orquestador inteligente que evalúa la solicitud actual del usuario, consulta el contexto del proyecto (`PRODUCT.md` y `DESIGN.md`) y selecciona o aplica automáticamente los comandos, reglas y flujos de trabajo de diseño adecuados para resolver la tarea de UI/UX.

---

## 🎯 Objetivo Principal

* **Orquestación Automática:** Actúa como el motor central que determina qué técnica de diseño o refactorización aplicar según la petición.
* **Aplicación de Estándares:** Garantiza que cualquier cambio visual o funcional cumpla estrictamente con el sistema de diseño y las reglas anti-*slop*.
* **Punto de Entrada Unificado:** Permite solicitar mejoras de diseño, adiciones de componentes o revisiones sin necesidad de memorizar cada subcomando específico.

---

## 🔍 ¿Cuándo utilizarlo?

* **Tareas Amplias de UI:** Cuando deseas solicitar un trabajo de diseño o implementación sin especificar un subcomando preciso (`/polish`, `/layout`, `/extract`, etc.).
* **Iteración General:** Al requerir que la IA asuma el rol de diseñador principal de UI/UX para evaluar y ejecutar mejoras en una vista o componente.
* **Consultas de Diseño:** Para pedir recomendaciones estéticas, de arquitectura de información o de jerarquía alineadas al proyecto.

---

## 📋 Flujo de Trabajo

1. **Lectura de Contexto:** Lee los archivos `PRODUCT.md` y `DESIGN.md` para recuperar la personalidad, la audiencia y los tokens de diseño.
2. **Evaluación de la Petición:** Analiza el componente o archivo objetivo y determina las acciones necesarias (ajuste de tipografía, estructura, accesibilidad, etc.).
3. **Ejecución y Guiado:** Aplica las modificaciones respetando los principios del sistema y sugiriendo subcomandos especializados si la tarea lo requiere.

---

## 💡 Relación con la Suite

> [!info] Hub de la Suite
> Mientras que los subcomandos (`/impeccable init`, `/impeccable document`, `/impeccable polish`, etc.) realizan tareas altamente especializadas, `/impeccable` es la orden genérica para canalizar cualquier necesidad de diseño manteniendo la coherencia global.

---

## 🚀 Uso Rápido

```bash
# Solicitar un trabajo o revisión general de UI
/impeccable [descripción de la tarea o componente a mejorar]
```
