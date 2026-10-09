---
alias:
  - Skill /resolving-merge-conflicts
  - AI Hero Resolving Merge Conflicts
tags:
  - ai/skills
  - dev/git
  - dev/workflow
  - matt-pocock
source: "https://www.aihero.dev/skills-resolving-merge-conflicts"
type: skill-note
---
> [!abstract] **Propósito**  
> Resuelve conflictos de integración en Git (`merge`, `rebase` o `cherry-pick`) **preservando la intención técnica de ambas partes**, garantizando que el código resulte sintácticamente válido, pase el typechecker y mantenga la suite de tests en verde.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Usa `/resolving-merge-conflicts` cuando un comando de Git se detenga debido a marcadores de conflicto (`<<<<<<<`, `=======`, `>>>>>>>`), o cuando necesites integrar cambios de `main` en tu rama y preveas colisiones complejas.
- **Solución segura:** Evita que el LLM elija arbitrariamente un lado del conflicto borrando cambios válidos sin verificar.

| Situación | Skill recomendada |
| :--- | :--- |
| Conflictos de Git activos al hacer `merge`, `rebase` o `pull` | `/resolving-merge-conflicts` |
| La integración funciona sin conflictos pero quieres validar si rompe lógica | `/code-review` |
| Ajustar la arquitectura tras una integración grande | `/improve-codebase-architecture` |

---

## 🔄 Proceso de Resolución (4 Pasos)

1. **Identificación y Análisis:** Localiza todos los archivos en conflicto (`git status`) y analiza la intención de los cambios tanto de la rama actual (*ours*) como de la rama entrante (*theirs*).
2. **Fusión Lógica (No solo textual):** Combina el código resolviendo los marcadores de conflicto respetando las intenciones de ambas ramas, no descartando código a ciegas.
3. **Verificación Estricta:** Ejecuta el comprobador de tipos (`typecheck`) y los tests relevantes para asegurar que la combinación no introdujo errores de sintaxis o lógica.
4. **Finalización del Proceso Git:** Continúa o concluye la operación (`git merge --continue`, `git rebase --continue`, etc.) o deja los archivos preparados para el commit final.

---

## 💡 Conceptos Clave

- **Análisis de Intención:** A diferencia de una herramienta de diff básica, analiza *para qué* se hizo cada cambio en ambas ramas antes de decidir cómo unirlos.
- **Integridad post-resolución:** No considera resuelto un conflicto hasta que la app compile y pase los tests; la ausencia de marcadores `<<<<<<<` no es suficiente.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **Cuidado con las decisiones de producto implícitas**  
> Si dos ramas introducen cambios contradictorios en la lógica de negocio que no pueden coexistir técnicamente, la skill debe solicitar aclaración en lugar de inventar una regla de negocio.

- **Verifica el estado de Git antes de continuar:** Asegúrate de estar en medio de la operación de Git deseada antes de invocarla.
- **Mapeo de tests:** Siempre ejecuta la suite de pruebas tras resolver los marcadores para detectar fallos silenciosos por incompatibilidad de funciones o firmas cambiadas.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=resolving-merge-conflicts
```

