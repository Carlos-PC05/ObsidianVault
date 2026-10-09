---
alias:
  - Skill /diagnosing-bugs
  - AI Hero Diagnosing Bugs
tags:
  - ai/skills
  - dev/debugging
  - dev/testing
  - matt-pocock
source: "https://www.aihero.dev/skills-diagnosing-bugs"
type: skill-note
---
> [!abstract] **Propósito**  
> Encuentra la **causa raíz de un fallo** mediante un proceso científico y riguroso. **Aísla el bug escribiendo un test de reproducción que falle (Red)** antes de intentar cualquier arreglo, evitando parches a ciegas o suposiciones no verificadas.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Usa `/diagnosing-bugs` cuando tengas un fallo reportado, un comportamiento inesperado o una prueba que falla y no sepas exactamente qué lo está causando.
- **Detiene la adivinación:** Impide modificar código de producción hasta haber aislado la causa exacta mediante pruebas.

| Situación | Skill recomendada |
| :--- | :--- |
| Comportamiento roto o fallo imprevisto sin causa clara | `/diagnosing-bugs` |
| Construir una característica nueva acordada mediante TDD | `/implement` o `/tdd` |
| Revisar si un diff o PR introduce errores o incumple estándares | `/code-review` |
| Deuda técnica estructural o inconsistencias en todo el repo | `/improve-codebase-architecture` |

---

## 🔬 Método de Diagnóstico (4 Pasos)

1. **Recolección de Evidencia:** Analiza logs, trazas de error, reportes y el comportamiento esperado vs. el comportamiento actual.
2. **Formulación de Hipótesis:** Genera explicaciones comprobables de por qué ocurre el fallo.
3. **Test de Reproducción (*Red Test*):** Escribe el test más pequeño y específico posible que **reproduzca el bug y falle**.
4. **Localización del Problema:** Rastrea la ejecución hasta señalar la línea, función o estado defectuoso exacto.

---

## 💡 Conceptos Clave

- **Reproducción Primero:** Si no hay un test que falle, no hay diagnóstico. El test que falla es la prueba de que el bug existe y la red de seguridad para confirmar cuando esté resuelto.
- **Aislamiento de Variables:** Descarta causas sospechosas una a una en lugar de cambiar múltiples partes del código a la vez.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **Diagnosticar ≠ Corregir**  
> El objetivo principal de esta skill es **encontrar la causa raíz y confirmarla con un test**, no aplicar la solución final de forma precipitada. Una vez identificado y aislado el fallo, la corrección se vuelve trivial.

- **Evita cambiar código a ciegas:** No edites archivos de producción con la esperanza de "ver si se arregla". Modifica solo lo necesario para instrumentar logs o aislar la prueba.
- **Mantén la prueba focalizada:** El test de reproducción debe probar únicamente la condición del fallo, no toda la aplicación.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=diagnosing-bugs
```

