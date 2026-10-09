---
alias:
  - Skill /wizard
  - AI Hero Wizard
tags:
  - ai/skills
  - dev/workflow
  - dev/meta
  - ai/unkeep
source: https://www.aihero.dev/skills-wizard
type: skill-note
---
> [!abstract] **Propósito**  
> Actúa como el **orquestador interactivo y guía metabúsqueda** de todo el ecosistema de skills de AI Hero. Asesora sobre qué skill usar en cada momento, configura las herramientas en el proyecto y guía al usuario paso a paso a través del flujo de trabajo de desarrollo.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Llama a `/wizard` cuando estés atascado, no recuerdes el nombre de una skill específica o quieras una guía paso a paso sobre qué hacer a continuación en tu proyecto.
- **Punto de orientación:** Ideal para desarrolladores que están empezando con las skills de Matt Pocock o para navegar flujos de trabajo complejos sin memorizar todos los comandos.

| Situación | Skill recomendada |
| :--- | :--- |
| Duda general sobre qué flujo seguir o cómo configurar las skills | `/wizard` |
| Triaje y clasificación inicial de reportes o tareas | `/triage` |
| Rellenar vacíos y aclarar requisitos con preguntas directas | `/grill-with-docs` / `/grill-me` |

---

## 🔮 Funciones Principales

1. **Enrutamiento Inteligente:** Evalúa el estado actual de tu proyecto/conversación y te indica la skill exacta a utilizar (`/to-spec`, `/implement`, `/code-review`, etc.).
2. **Asistente de Configuración:** Ayuda a instalar y configurar el conjunto de skills de Matt Pocock en tu entorno o proyecto (`setup-matt-pocock-skills`).
3. **Guía de Flujos:** Explica el mapa general del ciclo de vida del software en el ecosistema AI Hero:
   ```text
   /triage ──► /grill-with-docs ──► /to-spec ──► /to-tickets ──► /implement ──► /code-review
   ```
   

