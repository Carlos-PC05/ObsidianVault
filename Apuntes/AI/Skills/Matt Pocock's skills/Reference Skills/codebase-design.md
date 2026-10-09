---
alias:
  - Skill /codebase-design
  - AI Hero Codebase Design
tags:
  - ai/skills
  - dev/architecture
  - dev/design
  - ai/reference
source: https://www.aihero.dev/skills-codebase-design
type: skill-note
---
> [!abstract] **Propósito**  
> Diseña o rediseña las **fronteras, abstracciones y organización de carpetas/módulos** de una base de código. Define cómo deben estructurarse los componentes y flujos de datos antes de escribir código masivo, garantizando escalabilidad y bajo acoplamiento.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Llama a `/codebase-design` al iniciar un proyecto nuevo, al agregar un módulo/subsistema grande, o cuando la estructura de carpetas actual empiece a volverse caótica y difícil de mantener.
- **Definición de Arquitectura:** Se enfoca en las reglas estructurales de alto nivel (límites de módulos, contratos de API internos, patrones de diseño).

| Situación | Skill recomendada |
| :--- | :--- |
| Quieres definir la estructura de carpetas y arquitectura de un sistema | `/codebase-design` |
| Quieres corregir desviaciones o incoherencias arquitectónicas en un repo existente | `/improve-codebase-architecture` |
| Quieres investigar cómo encaja un cambio en la base de código actual | `/wayfinder` |
| Quieres escribir la spec técnica de una feature concreta | `/to-spec` |

---

## 📐 Aspectos que Define

1. **Estructura de Carpetas y Módulos:** Organización de archivos por características (*feature-driven*), capas o dominios.
2. **Límites y Contratos (*Seams & Boundaries*):** Define qué módulos son públicos, cuáles son privados y cómo interactúan entre sí.
3. **Flujo de Datos y Dependencias:** Establece las reglas de importación (ej. *"los componentes de UI nunca importan directamente la base de datos"*).
4. **Convenciones de Diseño:** Elige patrones consistentes (inyección de dependencias, repositorios, adaptadores, etc.) para todo el proyecto.

---

## 💡 Conceptos Clave

- **Macro vs. Micro:** Mientras que `/implement` o `/tdd` se enfocan en las funciones y archivos individuales, `/codebase-design` opera a nivel de sistema o subsistema.
- **Guía de Mantenibilidad:** Establece las bases que luego leerán skills como `/code-review` e `/improve-codebase-architecture` para evaluar la calidad del proyecto.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **No escribe la implementación fina**  
> Define los planos, contratos e interfaces principales de la arquitectura, pero no implementa la lógica de negocio detallada de cada ticket.

- **Mantén la simplicidad:** Evita sobreingeniería preliminar; diseña la arquitectura necesaria para el problema actual permitiendo evolución futura.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=codebase-design
```
