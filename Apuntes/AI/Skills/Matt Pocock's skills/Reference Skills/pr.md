---
alias:
  - Skill /pr
  - PR Body
tags:
  - ai/skills
  - dev/code-review
  - dev/workflow
  - ai/reference-skills
source: /home/carlos/.agents/skills/pr/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**  
> Redacta el **cuerpo de una pull request** con una vista clara del cambio, evidencia concreta de antes y después, y una evaluación del riesgo de fusión. Usa el vocabulario del dominio definido en `GLOSSARY.md` y mantiene la prosa breve.

---

## 🎯 ¿Cuándo usarla?

| Situación | Skill recomendada |
| :--- | :--- |
| Necesitas redactar la descripción de una PR | `/pr` |
| Quieres comprobar estándares y cumplimiento de requisitos | [[code-review]] |
| Quieres implementar toda una spec y preparar su PR | [[implement-spec]] |

---

## 📋 Plantilla del Cuerpo de la PR

```markdown
## Summary

<diagrama, esquema de diff o árbol>

## Evidence

- **Before:** <captura/salida/test que falla>
  **After:** <captura/salida/test que pasa>

## Merge Danger

**Door:** <one-way o two-way>

<descripción opcional>

**Blast Radius:** <descripción en una palabra>

<posibles consecuencias, opcional>
```

---

## 🧩 Summary: Elegir la Vista Más Pequeña

| Qué quieres explicar | Representación recomendada |
| :--- | :--- |
| Lógica o algoritmo | Pseudocódigo |
| Flujo de ejecución | Árbol de llamadas |
| Estructura de UI | Árbol de componentes con estados y límites relevantes |
| Responsabilidades de archivos o refactor amplio | Árbol de archivos poco profundo |
| Interacción o flujo de datos | Diagrama Mermaid |
| Cambio sobre una estructura conocida | Bloque `diff` |
| Bloque mayoritariamente nuevo o forma final copiable | Bloque completo de código |

Ejemplo de cambio de comportamiento:

```diff
 on(save)
-  write content
+  if content is unchanged
+    return cached result
+  write content
+  invalidate cache
```

- Coloca cada visual junto a la explicación que apoya.
- Incluye solo llamadas, archivos, props, estados y límites necesarios para entender el cambio.
- Puedes combinar vistas cuando ayuden, sin usar todas por defecto.

---

## 🧪 Evidence: Antes y Después

- **Capturas:** Evidencia preferida cuando el cambio es visual y el entorno lo permite.
- **Ejecución:** Tests y salidas de consola muestran que el cambio funciona.
- **Comparación concreta:** Identifica el test o comportamiento que fallaba y ahora pasa; puede mostrarse mediante pseudocódigo.

> [!tip] **Evidencia verificable**  
> Sustituye afirmaciones genéricas por una captura, salida o prueba concreta del comportamiento anterior y posterior.

---

## ⚖️ Merge Danger: Reversibilidad e Impacto

| Campo | Qué describe |
| :--- | :--- |
| **Door: two-way** | El cambio permite volver atrás con poco coste |
| **Door: one-way** | Incluye acciones destructivas o decisiones difíciles de revertir |
| **Blast Radius** | Alcance potencial del impacto; etiqueta de una palabra y explicación opcional |

Considera consecuencias como roturas para consumidores, cambios de layout o problemas de adaptación móvil, según lo que toque la PR.

---

## ⚠️ Puntos de Atención

- **Sin preámbulos:** Empieza por `Summary` y conserva los tres encabezados de la plantilla.
- **Lenguaje del dominio:** Consulta `GLOSSARY.md` para usar los términos del proyecto.
- **Alcance:** Esta skill guía la redacción del cuerpo; no define un flujo de creación o fusión de PRs.

---

## 📚 Créditos

Adaptada de la skill [show-me de Dex Horthy (Humanlayer)](https://github.com/humanlayer/skills/blob/main/plugins/show-me/skills/show-me/SKILL.md).
