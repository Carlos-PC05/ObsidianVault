---
alias:
  - Skill /domain-modeling
  - AI Hero Domain Modeling
tags:
  - ai/skills
  - dev/domain-driven-design
  - dev/architecture
  - ai/reference
source: https://www.aihero.dev/skills-domain-modeling
type: skill-note
---
> [!abstract] **Propósito**  
> Diseña e implementa el **modelo de dominio del negocio** definiendo entidades, agregados, objetos de valor (*value objects*), invariantes y transiciones de estado mediante el sistema de tipos del lenguaje, **independientemente de marcos de trabajo, bases de datos o librerías de UI**.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Llama a `/domain-modeling` cuando necesites modelar la lógica core del negocio, definir estados y eventos complejos, o modelar las reglas de dominio antes de acoplarlas a una base de datos o API.
- **Enfoque en Tipos Fuerte:** Ideal para hacer que los estados inválidos sean irrepresentables en tiempo de compilación.

| Situación | Skill recomendada |
| :--- | :--- |
| Definir reglas de negocio core, entidades, variantes de estado y tipos estrictos | `/domain-modeling` |
| Diseñar la arquitectura de carpetas, capas y módulos del repositorio | `/codebase-design` |
| Prototipar o validar visualmente un modelo de estado en UI | `/prototype` |
| Crear la especificación técnica completa de una característica | `/to-spec` |

---

## 📐 Aspectos que Define

1. **Entidades y Objetos de Valor:** Identifica qué conceptos tienen identidad propia frente a los que son puramente valores inmutables.
2. **Invariantes del Dominio:** Define reglas de negocio que **nunca deben romperse** (ej. *"un pedido pagado no puede agregar ítems"*).
3. **Máquinas de Estado / Uniones Discriminadas:** Representa flujos de estado de forma explícita mediante tipos para evitar estados imposibles o inconsistentes.
4. **Vocabulario Ubicuo:** Alinea los nombres de clases, tipos y métodos con el lenguaje real usado por los expertos del negocio.

---

## 💡 Conceptos Clave

- **Aislamiento del Dominio:** La lógica del dominio no sabe si se ejecutará en un servidor, un navegador, PostgreSQL o memoria. Es código puro e independiente.
- **Making Illegal States Unrepresentable:** Aprovecha el sistema de tipos para que los fallos de lógica de negocio se detecten en tiempo de compilación/typecheck.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **Cero acoplamiento a la infraestructura**  
> No incluye llamadas a bases de datos, librerías HTTP, frameworks web ni librerías de UI. Todo eso pertenece a la capa de infraestructura o aplicación.

- **Fácilmente testeable:** Al ser funciones puras y tipos puros, los tests del dominio son extremadamente rápidos y directos.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=domain-modeling
```