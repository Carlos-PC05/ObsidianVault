---
alias:
  - Skill /prototype
  - AI Hero Prototype
tags:
  - ai/skills
  - dev/prototyping
  - dev/design
  - ai/shaping
source: https://www.aihero.dev/skills-prototype
type: skill-note
---
> [!abstract] **Propósito**  
> Escribe **código desechable (*throwaway code*)** exclusivamente para responder una pregunta concreta de diseño o comportamiento ("¿se siente bien este modelo de estado?" o "¿cómo debería verse esta pantalla?"). **No incluye tests, persistencia, manejo de errores complejo ni abstracciones**.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Puedes llamarla manualmente con `/prototype`, o el agente puede sugerirla cuando un debate sobre diseño no se resuelve mediante conversación.
- **Detiene la especulación:** En lugar de adivinar o debatir durante sesiones largas de *grilling*, se construye una prueba rápida, se prueba y se toma la decisión.

| Situación | Skill recomendada |
| :--- | :--- |
| Dudas sobre UX/UI o modelo de estado que no puedes validar en texto | `/prototype` |
| Quieres descubrir por qué un código existente ya construido falla | `/diagnosing-bugs` |
| Quieres un mapeo completo de arquitectura de una feature grande | `/wayfinder` |
| La idea está clara y lista para pasar a código de producción | `/implement` |

---

## 🔀 Dos Formatos de Salida (Según la Pregunta)

1. **Prueba de Lógica / Modelo de Estado** (`"¿Se siente bien esta lógica?"`):
   - Genera un **único archivo HTML autónomo** (sin build step ni servidor) que se abre al hacer doble clic.
   - Incluye un panel interactivo con botones para manipular el estado, escenarios guiados por pestañas y registro visual del estado tras cada clic.
   - La lógica interna usa funciones puras para poder extraerse directamente al código real tras validarse.

2. **Prueba de Interfaz / Layout** (`"¿Cómo debería verse esto?"`):
   - Crea varias **variaciones radicales de UI** dentro de una ruta real del proyecto.
   - Permite alternar fácilmente entre variantes mediante una barra inferior flotante o parámetros URL (`?variant=1`).
   - Se evalúa contra datos reales para juzgar la densidad visual correctamente.

---

## 💡 Gestión del Resultado (*Artifacts & Branching*)

- **La respuesta (El aprendizaje):** La conclusión y decisión tomada se documentan de forma duradera en un commit message, un documento de arquitectura (ADR) o en la issue de implementación.
- **El código del prototipo:** **Nunca se fusiona a `main`**. Se guarda en una rama desechable (ej. `prototype/feature-name`) para que quede como evidencia ejecutable sin ensuciar la base de código ni dejar deuda técnica.

---

## ⚠️ Reglas y Restricciones Generales

> [!warning] **Evita "endurecer" (*harden*) el prototipo**
> En el momento en que agregues tests, conectes la base de datos real o manejes edge cases fuera de la pregunta, **has dejado de prototipar**. 

- **Código desechable pero conservado:** Se conserva la rama fuera de `main` para que cualquiera pueda reejecutar la prueba en el futuro.
- **Sin infraestructura:** Mantiene el estado en memoria y prioriza la velocidad de respuesta sobre la calidad de ingeniería.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=prototype
```

