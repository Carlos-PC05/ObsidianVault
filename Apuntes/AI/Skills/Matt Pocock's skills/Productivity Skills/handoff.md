---
alias:
  - Skill /handoff
  - AI Hero Handoff
tags:
  - ai/skills
  - dev/context
  - dev/workflow
  - ai/productivity
source: https://www.aihero.dev/skills-handoff
type: skill-note
---
> [!abstract] **Propósito**  
> Genera un **resumen de traspaso estructurado (*handoff document*)** de la sesión actual. Permite pausar el trabajo, cambiar de rama o reiniciar la ventana de contexto sin perder el hilo de lo realizado, las decisiones tomadas y los siguientes pasos pendientes.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Llama a `/handoff` cuando la ventana de contexto esté sobrecargada, estés al final de la jornada laboral, o vayas a pasar la tarea a otra sesión o desarrollador.
- **Evita la pérdida de memoria:** Evita repetir la exploración del proyecto cuando inicias una conversación nueva.

| Situación | Skill recomendada |
| :--- | :--- |
| Vas a reiniciar la sesión/limpiar contexto y quieres guardar el estado actual | `/handoff` |
| Quieres estructurar las tareas pendientes en tickets individuales | `/to-tickets` |
| Quieres revisar el diff final antes de cerrar el trabajo | `/code-review` |

---

## 📋 Estructura del Traspaso

El documento generado por `/handoff` suele incluir:

1. **Objetivo Actual:** Qué se estaba intentando construir o resolver.
2. **Estado del Trabajo:** Qué se ha completado hasta el momento (commits, tests pasados, archivos creados).
3. **Decisiones Clave:** Elecciones de diseño o arquitectura tomadas durante la sesión.
4. **Siguientes Pasos (Accionables):** Lista clara de lo que se debe hacer inmediatamente al reanudar la tarea.
5. **Comandos / Contexto Útil:** Comandos de terminal relevantes, estados de tests que fallan o decisiones de depuración.

---

## 💡 Conceptos Clave

- **Puente entre Sesiones:** Facilita la regla de "limpiar contexto frecuentemente" en agentes de IA sin perder el progreso.
- **Restauración Rápida:** La nueva sesión solo necesita leer el archivo o bloque de `handoff` para recuperar el 100% de la orientación.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **Crea un punto de guardado, no ejecuta código**  
> Es una skill de **documentación y estado de flujo**, no ejecuta tests ni modifica código de la aplicación.

- **Conciso y Accionable:** Enfocado únicamente en lo necesario para continuar, evitando rodeos o transcripciones completas del historial del chat.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=handoff
```