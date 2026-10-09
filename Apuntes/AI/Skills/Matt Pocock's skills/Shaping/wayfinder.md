---
alias:
  - Skill /wayfinder
tags:
  - ai/skills
  - dev/planning
  - dev/architecture
  - ai/shaping
source: https://www.aihero.dev/skills-wayfinder
type: skill-note
---
> [!abstract] **Propósito**  
> Mapea y orienta dentro de una base de código cuando vas a implementar una característica o cambio amplio. Explora el proyecto para **descubrir los archivos, dependencias, puntos de extensión y riesgos relevantes**, produciendo un **mapa de implementación (*wayfinding map*)** estructurado.

---
# 🎯 ¿Cuándo usarla?

- **Invocación:** Llama explícitamente a `/wayfinder` cuando necesites entender *dónde* y *cómo* encaja un cambio en un repositorio extenso antes de empezar a escribir código o especificaciones detalladas.
- **Ideal para:** Funcionalidades complejas que tocan múltiples módulos o sistemas desconocidos.

| Situación | Skill recomendada |
| :--- | :--- |
| Sabes qué quieres hacer pero no dónde ni qué afectará en el repo | `/wayfinder` |
| Tienes claras las partes de la base de código pero quieres detallar la arquitectura/diseño | `/to-spec` |
| Quieres resolver dudas y requisitos ambiguos mediante preguntas | `/grill-with-docs` o `/grill-me` |
| La tarea es ridículamente pequeña y localizada | Ir directo a `/implement` |

---
## 🧭 ¿Qué hace un recorrido de `/wayfinder`?

1. **Lectura del contexto:** Examina la solicitud y los archivos raíz del proyecto (arquitectura, convenciones, árbol de directorios).
2. **Exploración de la base de código:** Rastrea las rutas críticas, puntos de entrada, tipos, servicios y archivos que se verán impactados directa o indirectamente.
3. **Generación del Mapa:** Crea un documento de orientación con:
   - **Archivos a modificar/crear:** Rutas exactas implicadas.
   - **Patrones a seguir:** Convenciones del repo a respetar.
   - **Riesgos y dependencias ocultas:** Efectos secundarios o contratos de API/datos que podrían romperse.
   - **Puntos de extensión:** Dónde enganchar la nueva funcionalidad.

---
## 💡 Conceptos Clave y Flujo

```text
/wayfinder ──► /to-spec ──► /to-tickets ──► /implement ──► /code-review
```

