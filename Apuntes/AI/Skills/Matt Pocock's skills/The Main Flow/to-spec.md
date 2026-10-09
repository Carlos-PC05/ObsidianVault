---
alias:
  - /to-spec
  - To Spec
  - Former /to-prd
tags:
  - ai/skills
  - dev/tools
  - workflow
  - ai/the-main-flow
source: https://www.aihero.dev/skills-to-spec
---
> **Comando:** `/to-spec`  
> *(Nota: En la v1.1 reemplazó a la antigua skill `/to-prd`)*

---

## 📌 ¿Qué hace?
Sintetiza y **convierte la conversación que acabas de tener en una especificación escrita (*spec*)**, publicándola como un ticket/issue único en tu gestor de tareas.

> [!IMPORTANT]
> **No hace preguntas de entrevista ni toma decisiones nuevas.** Su único objetivo es consolidar decisiones que ya se tomaron (durante `/grill-with-docs` o `/wayfinder`), usando el vocabulario de tu proyecto (`CONTEXT.md`) y las decisiones de arquitectura (`ADRs`).

---

## ⏱️ ¿Cuándo usarla?

| Tu situación | Qué ejecutar |
| :--- | :--- |
| No has decidido ni discutido nada todavía | `/grill-with-docs` |
| Ya decidiste y el cambio **cabe en 1 sola sesión** | Saltar `/to-spec` e ir directo a `/implement` |
| **Ya decidiste y el desarrollo abarcará MÚLTIPLES sesiones** | `/to-spec` y luego `/to-tickets` |
| Tienes un mapa de decisiones completado en `/wayfinder` | `/to-spec #<id_del_issue_mapa>` |

---

## 🧵 "Costuras" antes que prosa (*Seams before prose*)
Antes de redactar la especificación completa, `/to-spec` te propondrá **las "costuras" (*seams*) o puntos límite donde se probará la funcionalidad** (idealmente solo 1 por cambio). 

- **¿Por qué es vital?** Las skills de testing (`/tdd`) y revisión (`/code-review`) usan estas costuras para validar el código. Si no se acuerdan aquí, la revisión señalará fallos de arquitectura o testeo.

---

## 🔗 Dónde encaja en el Flujo Principal

`/grill-with-docs` ➔ **`/to-spec`** ➔ `/to-tickets` ➔ `/implement` ➔ `/code-review`

> [!TIP] Higiene de Contexto
> **No ejecutes `/clear` ni `/compact` entre `/to-spec` y `/to-tickets`**. Ejecútalas en la misma conversación para evitar que la especificación sea truncada si el gestor de tareas devuelve un archivo muy largo.

---

## ⚠️ Problemas Frecuentes y Advertencias

- **Etiqueta `ready-for-agent` inesperada:**  
  La especificación creada incluye la etiqueta `ready-for-agent`. Esto significa "documento completo sin necesidad de más triaje". **Atención:** Si usas agentes autónomos en segundo plano (AFK) que buscan esta etiqueta, intentarán construir toda la spec de un solo golpe. Quita la etiqueta o excluye specs padre si usas automatizaciones AFK.
- **La especificación no se actualiza sola:**  
  Es una captura estática de un momento del proyecto. Si al implementar aprendes algo nuevo, no necesitas actualizar la spec; los aprendizajes duraderos deben ir a `CONTEXT.md` o a un `ADR`.
- **Trabajo duplicado:**  
  La skill **no busca** si ya existen otros tickets similares en el gestor antes de redactar. Debes revisar tu backlog previamente.

---

## ✅ ¿Cómo saber si funcionó?
- Empieza a redactar de inmediato en lugar de hacerte una nueva ronda de preguntas.
- Te propone primero los puntos/pistas de prueba (*seams*) para confirmar con él.
- Utiliza los términos reales de tu dominio en lugar de plantilla genérica de producto.
- La sección **"Fuera de alcance" (*Out of scope*)** contiene limitaciones reales que decidiste descartar.