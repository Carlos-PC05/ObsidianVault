---
alias:
  - /ask-matt
  - Matt Pocock Router Skill
tags:
  - ai/skills
  - dev/tools
  - workflow
  - ai/getting-started
source: https://www.aihero.dev/skills-ask-matt
---
> **Comando:** `/ask-matt`

---

## 📌 ¿Qué hace?
Es el **enrutador y mapa mental** de todo el ecosistema de skills de Matt Pocock. 
Tú le describes la situación actual de tu proyecto (ej: *"tengo una idea pero no sé por dónde empezar"*, *"tengo un bug entrante"*, *"la sesión se está haciendo muy larga"*) y la skill te devuelve **el flujo exacto de comandos que debes ejecutar**, especificando dónde entran las decisiones humanas y qué hacer con el contexto del agente.

> [!IMPORTANT]
> **No ejecuta nada por ti.** Te recomienda la siguiente secuencia de comandos y se detiene. Tampoco detecta tus skills locales ni de otros autores: es un mapa exclusivo del conjunto de skills de Matt Pocock.

---

## ⏱️ ¿Cuándo usarla?
- Cuando no sabes qué skill usar para el estado actual de tu tarea.
- Cuando dudas entre dos skills similares (ej: `/grill-me` vs `/grill-with-docs`).
- Cuando necesitas saber en qué momento hacer `/clear`, `/compact` o `/handoff` para no saturar el contexto del agente.

---

## 🔄 Los 4 Tipos de Rutas / Flujos

1. **Flujo Principal (*Main Flow*):**
   De la idea a producción: `Grill` ➔ `Spec` ➔ `Tickets` ➔ `Implement` ➔ `Review`.
2. **Entradas Secundarias (*On-ramps*):**
   Para trabajo no planificado que luego se une al flujo principal (ej: reporte de bugs entrante, refactorización masiva).
3. **Skills Independientes (*Standalones*):**
   Herramientas puntuales que no requieren un flujo completo (ej: resolución de conflictos de merge, prototipado rápido).
4. **Capa de Vocabulario / Referencia:**
   Definiciones y normas de arquitectura/dominio que leen las demás skills.

---

## 🌳 El Árbol de Decisiones de Límite de Fase (*Phase Boundary*)

Al terminar una fase dentro de una sesión, `/ask-matt` te aconsejará qué hacer con la memoria/contexto siguiendo esta jerarquía: