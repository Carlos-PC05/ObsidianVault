---
alias:
  - /grill-with-docs
  - Grill With Docs
tags:
  - ai/skills
  - dev/tools
  - workflow
  - ai/the-main-flow
source: https://www.aihero.dev/skills-grill-with-docs
---
> **Comando:** `/grill-with-docs`  
> **Dependencias obligatorias:** `grilling` y `domain-modeling` (deben estar instaladas para que funcione).

---

## 📌 ¿Qué hace?
Es la skill de **entrevista inicial orientada al código**. El agente te hace preguntas por rondas para clarificar una idea o arquitectura antes de empezar a programar. 

A diferencia de otras skills de interrogatorio, esta **crea y actualiza archivos reales en disco en tiempo real**:
- Mantiene un **glosario de dominio/vocabulario** en el proyecto.
- Documenta las **decisiones arquitectónicas difíciles** que cumplan ciertos criterios.

---

## ⏱️ ¿Cuándo usarla vs. otras alternativas?

| Tu situación | Skill a utilizar |
| :--- | :--- |
| Sin carpeta de trabajo / Idea abstracta fuera del repo | `/grill-me` |
| **Repo existente + Cambio abordable en 1 sola sesión** | `/grill-with-docs` |
| Proyecto masivo / Feature compleja que tomará **múltiples sesiones** | `/wayfinder` |
| Documentar un repo desde cero que no tiene nada de docs | `/grill-with-docs` *(ej. "ayúdame a documentar mi repo")* |
| Decisión bloqueada por conocimiento de otra persona | `/to-questionnaire` |

---

## 📝 El Rastro de Documentos (*Paper Trail*)

La skill genera información en tres niveles distintos:

1. **Glosario / Términos de Dominio (`CONTEXT.md`):**  
   Se guardan en `CONTEXT.md` (o `CONTEXT-MAP.md` en monorrepos) **en el instante preciso en que se resuelven** durante la charla. Es solo un glosario de términos del proyecto, **no** guarda detalles de implementación.
2. **Registro de Decisiones de Arquitectura (`docs/adr/`):**  
   Solo se genera un ADR si la decisión cumple **3 filtros estrictos**:
   - Difícil de revertir.
   - Sorprendente/inesperada sin contexto.
   - Implica un trade-off o compromiso real.
3. **Detalles de conversación:**  
   Todo lo demás que acuerden queda **únicamente en la conversación activa**. No se guarda en disco automáticamente.

---

## 🔗 Dónde encaja en el Flujo Principal

`/grill-with-docs` ➔ `/to-spec` ➔ `/to-tickets` ➔ `/implement` ➔ `/code-review`

> [!TIP] Siguiente Paso
> Al terminar la entrevista, no limpies el contexto (`/clear`). Pasa directamente a ejecutar `/to-spec` **en la misma conversación** para que la especificación capture las decisiones que no se guardaron como ADR.

---

## ⚠️ Problemas Frecuentes y Advertencias

- **Error de "No creó ningún archivo":**  
  Puede ser normal si ningún término fue nuevo y ninguna decisión cumplió los 3 filtros para ser ADR. Sin embargo, si estás en un orquestador multi-agente, hay un bug conocido donde la entrevista corre pero la escritura en disco falla.
- **Error de "Hace todas las preguntas de golpe":**  
  Ocurre si no tienes instaladas las sub-skills `grilling` y `domain-modeling`. La skill en sí es una redirección de una sola línea a ambas.
- **Drift/Desfase en equipos:**  
  Está diseñada para **un solo editor humano**. En equipos grandes, mantener los ADRs y el glosario a mano puede generar desactualización rápida si no se audita en CI.

---

## ✅ ¿Cómo saber si funcionó?
- El archivo `CONTEXT.md` se va actualizando **término a término** durante la conversación (no de golpe al final).
- El agente lee el código fuente existente para responder sus propias dudas antes de preguntártelas a ti.
- Te desafía o corrige si usas un término de forma incoherente con el glosario preexistente.