---
alias:
  - Skill /improve-codebase-architecture
  - AI Hero Improve Codebase Architecture
tags:
  - ai/skills
  - dev/architecture
  - dev/refactoring
  - matt-pocock
source: "https://www.aihero.dev/skills-improve-codebase-architecture"
type: skill-note
---
> [!abstract] **Propósito**  
> Diagnostica y corrige la **degradación de arquitectura (*drift*) en todo el repositorio**, alineando la base de código con las convenciones del proyecto y reduciendo la deuda técnica mediante **refactorizaciones sistemáticas guiadas por tests**.

---
## 🎯 ¿Cuándo usarla?

- **Invocación:** Llama manualmente a `/improve-codebase-architecture` cuando la base de código haya acumulado incoherencias, patrones duplicados o cuando una evolución arquitectónica no se haya aplicado de forma uniforme en todas las carpetas.
- **Enfoque global:** A diferencia de la revisión de un único `diff`, analiza la base de código de punta a punta.

| Situación | Skill recomendada |
| :--- | :--- |
| Incoherencia arquitectónica o deuda técnica global en el repo | `/improve-codebase-architecture` |
| Revisar únicamente los cambios de un ticket o PR específico | `/code-review` |
| Corregir un bug puntual sin cambiar la arquitectura | `/diagnosing-bugs` |
| Mapear cómo impactará un cambio nuevo en el proyecto | `/wayfinder` |

---

## 🔄 Proceso de Trabajo (4 Fases)

1. **Auditoría y Detección:** Analiza el proyecto en busca de divergencias frente a las reglas documentadas (`CODING_STANDARDS.md`, etc.), patrones obsoletos y dependencias mal acopladas.
2. **Estrategia y Plan de Refactor:** Propone un plan estructurado dividiendo los cambios en pasadas o reestructuraciones pequeñas y controladas.
3. **Refactorización Segura (Test-First/Test-Backed):** Aplica las transformaciones asegurando que la suite de tests existente se mantenga en verde en cada paso.
4. **Verificación Global:** Pasa el typechecker y ejecuta la suite completa de tests para garantizar que no haya regresiones.

---

## 💡 Conceptos Clave

- **Eje en las Convenciones Locales:** Toma como fuente de verdad los estándares propios del proyecto. Si no existen, aplica un baseline de buenas prácticas orientadas a mantenibilidad y bajo acoplamiento.
- **Acción Gradual y Controlada:** No realiza reescrituras masivas sin red de seguridad. Avanza paso a paso para evitar romper la aplicación.

---

## ⚠️ Puntos de Atención y Consejos

> [!warning] **Evita los bucles infinitos de refactor**
> Las refactorizaciones arquitectónicas globales pueden encontrar mejoras de forma ilimitada. Define un alcance claro para la sesión y detente una vez corregidos los patrones objetivo.

- **No añade funcionalidades nuevas:** Su único objetivo es mejorar la estructura, legibilidad y mantenibilidad del código existente.
- **Cobertura de tests indispensable:** Requiere una suite de pruebas confiable para asegurar que los cambios estructurales no alteren el comportamiento.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=improve-codebase-architecture
```