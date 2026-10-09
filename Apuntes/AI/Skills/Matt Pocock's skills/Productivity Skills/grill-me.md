---
alias:
  - Skill /grill-me
  - AI Hero Grill Me
tags:
  - ai/skills
  - dev/discovery
  - dev/planning
  - ai/productivity
source: https://www.aihero.dev/skills-grill-me
type: skill-note
---
> [!abstract] **Propósito**  
> Interroga activamente al usuario mediante un **cuestionario técnico directo y exhaustivo** para extraer requisitos, resolver ambigüedades y descubrir *edge cases* no considerados antes de empezar a escribir código o especificaciones.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Usa `/grill-me` cuando tengas una idea general, un problema o una característica definida solo "en la cabeza" y necesites validar todos sus aspectos técnicos antes de pasar a la fase de diseño.
- **Detiene la especulación:** Evita que el agente asuma o invente requisitos de negocio por su cuenta cuando la idea aún no está escrita en la base de código ni en documentos.

| Situación                                                                         | Skill recomendada  |
| :-------------------------------------------------------------------------------- | :----------------- |
| La idea está en tu cabeza y necesitas extraer todos los requisitos                | `/grill-me`        |
| La idea requiere alineación con la documentación o código existente del repo      | `/grill-with-docs` |
| Ya respondiste las dudas y quieres generar el documento de especificación técnica | `/to-spec`         |
| Quieres entender en qué parte del repo encaja la solución técnica                 | `/wayfinder`       |

---

## 🔄 Proceso de "Grilling"

1. **Análisis de la Promesa Inicial:** Lee la idea o descripción básica proporcionada por el usuario.
2. **Entrevista Adaptativa:** Formula preguntas incisivas sobre:
   - **Lógica de negocio y reglas de borde (*edge cases*).**
   - **Manejo de errores y estados de fallo.**
   - **UX / Experiencia de usuario deseada.**
   - **Límites y restricciones explícitas.**
3. **Consolidación:** Continúa haciendo preguntas de seguimiento hasta que no queden ambigüedades críticas sobre la funcionalidad deseada.

---

## 💡 Conceptos Clave

- **Inversión del Rol (El Agente pregunta, tú respondes):** En lugar de pedirle al modelo que adivine la solución, el modelo te somete a un interrogatorio para asegurarse de construir lo correcto.
- **Sin Dependencia del Repo:** A diferencia de `/grill-with-docs`, esta versión se enfoca puramente en la lógica y la intención de producto, sin contrastarla todavía contra archivos locales del repositorio.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **No escribe código ni archivos de spec en esta fase**  
> El objetivo es **aclarar la intención**, no implementar. Una vez terminado el interrogatorio, el resultado debe ser canalizado hacia `/to-spec`.

- **Formato dinámico:** Hace preguntas de forma incremental para no abrumar al usuario con un único bloque masivo e inmanejable de texto.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=grill-me
```