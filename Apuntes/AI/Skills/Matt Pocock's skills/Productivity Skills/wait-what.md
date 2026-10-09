---
alias:
  - Skill /wait-what
  - AI Hero Wait What
tags:
  - ai/skills
  - dev/debugging
  - dev/context
  - ai/productivity
source: https://www.aihero.dev/skills-wait-what
type: skill-note
---
> [!abstract] **Propósito**  
> Actúa como un **freno de emergencia (*circuit breaker*)** cuando el agente se desvía, entra en bucle o toma decisiones inesperadas. Detiene la ejecución actual, exige que el agente **explicite sus suposiciones** y reorienta el plan antes de que siga modificando código.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Llama inmediatamente a `/wait-what` cuando veas que el agente está editando archivos no relacionados, reescribiendo cosas sin pedir permiso, haciendo demasiadas suposiciones o atascado en un bucle.
- **Interrupción consciente:** Es la herramienta para tomar el control de la conversación sin perder el hilo del problema que se intentaba resolver.

| Situación | Skill recomendada |
| :--- | :--- |
| El agente tomó un rumbo equivocado o se desvió de la tarea | `/wait-what` |
| Quieres guardar el progreso actual para reiniciar la sesión limpiamente | `/handoff` |
| Quieres entender la causa raíz de un bug persistente | `/diagnosing-bugs` |
| Quieres reorientar qué skill o flujo deberías estar usando | `/wizard` |

---

## 🛑 ¿Qué hace `/wait-what` al ser invocada?

1. **Detención Inmediata:** Frena la ejecución de comandos o edición de código que estuviera llevando a cabo el agente.
2. **Exposición de Suposiciones:** Obliga al agente a listar brevemente:
   - Qué estaba intentando hacer y por qué.
   - Qué suposiciones no confirmadas tomó para tomar ese camino.
3. **Punto de Pausa:** Te devuelve el control para corregir las premisas erróneas, descartar el rumbo actual o ajustar la dirección.

---

## 💡 Conceptos Clave

- **Interrupción Temprana:** Evita que un error conceptual se propague en múltiples archivos o commits innecesarios.
- **Clarificación de Premisas:** Fuerza al agente a "pensar en voz alta" sobre el desacuerdo antes de continuar.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **No es una skill de depuración de código**  
> No busca errores en el software (`/diagnosing-bugs`), sino **corregir desviaciones en la lógica o comportamiento del propio agente de IA**.

- **Respuesta Breve y Directa:** No debe generar explicaciones largas ni disculpas; debe ir al grano exponiendo qué estaba haciendo y esperar instrucciones.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=wait-what
```