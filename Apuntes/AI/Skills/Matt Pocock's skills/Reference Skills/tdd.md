---
alias:
  - Skill /tdd
  - AI Hero TDD
tags:
  - ai/skills
  - dev/tdd
  - dev/testing
  - ai/reference
source: https://www.aihero.dev/skills-tdd
type: skill-note
---
> [!abstract] **Propósito**  
> Guía la implementación de características o corrección de bugs siguiendo estrictamente el ciclo **Test-Driven Development (Red-Green-Refactor)**. Escribe pruebas exclusivamente en los **límites/costuras públicas (*seams*)** antes de escribir cualquier código de producción.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Llama directamente a `/tdd` cuando tengas un comportamiento concreto que implementar o un test que escribir antes del código.
- **Uso interno:** Es la skill que `/implement` ejecuta internamente en cada paso de su ciclo de construcción.

| Situación | Skill recomendada |
| :--- | :--- |
| Quieres desarrollar un comportamiento puntual paso a paso con TDD estricto | `/tdd` |
| Tienes una spec/ticket completo y quieres que la IA aplique TDD y commits automáticos | `/implement` |
| Quieres reproducir y aislar un fallo existente con un test en rojo | `/diagnosing-bugs` |
| Quieres verificar si el código final cumple con los estándares | `/code-review` |

---

## 🔄 El Bucle TDD (Red-Green-Refactor)

1. **Acordar la Costura (*Seam*):** Identifica el límite público accesible antes de escribir la prueba. Si la costura no está clara, exige confirmación antes de proceder.
2. **Fase Roja (*Red*):** Escribe el test más pequeño posible que exprese el comportamiento deseado y verifica que **falle por la razón correcta** (sin errores de sintaxis ajenos).
3. **Fase Verde (*Green*):** Escribe el código de producción mínimo e imprescindible para hacer pasar el test.
4. **Refactorización (*Refactor*):** Limpia el código y elimina duplicaciones asegurando que la suite de tests se mantenga completamente en verde.

---

## 💡 Conceptos Clave

- **Desarrollo en Costuras (*Seams*):** Las pruebas se enfocan en las interfaces públicas, no en la implementación privada. Esto permite refactorizar internamente sin romper las pruebas.
- **Sin Código Sin Test:** Se prohíbe escribir código de producción que no esté respaldado por un test que haya fallado previamente.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **Pruebas duraderas vs. Pruebas frágiles**  
> Si la costura elegida para el test es interna o acoplada a detalles de implementación, el test se romperá con cualquier refactorización futura.

- **Iteraciones pequeñas:** Avances en tajadas verticales pequeñas (*red-green-refactor*) ejecutando el comprobador de tipos (`typecheck`) e invocando los tests individuales en cada iteración.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=tdd
```