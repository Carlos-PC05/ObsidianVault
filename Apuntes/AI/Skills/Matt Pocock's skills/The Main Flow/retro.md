---
alias:
  - AI Hero Retro
tags:
  - ai/skills
  - dev/quality
  - dev/workflow
  - ai/the-main-flow
source: /home/carlos/.agents/skills/retro/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**  
> Analiza una sesión de programación y propone mejoras en el **entorno del agente** para que las siguientes ejecuciones sean más eficaces: navegación, comprobaciones automáticas, instrucciones y acceso a información.

---

## 🎯 ¿Cuándo usarla?

- **Invocación manual:** Debes pedirla explícitamente con `/retro` (`disable-model-invocation: true`).
- **Sesión analizada:** Puedes indicar una sesión concreta. Si no lo haces, analiza la actual.

| Situación | Skill recomendada |
| :--- | :--- |
| El agente perdió tiempo, cometió errores o carecía de información | `/retro` |
| Quieres evaluar el código contra estándares y requisitos | [[code-review]] |
| Vas a continuar el trabajo en otra sesión | [[handoff]] |

---

## 🔄 Flujo de Ejecución

1. **Guía de escritura:** Consulta [[writing-for-agents]] para redactar instrucciones útiles para agentes.
2. **Fuentes primarias:** Lee la sesión indicada, incluyendo sus registros si es necesario.
3. **Diagnóstico del entorno:** Busca oportunidades de mejora y comprueba qué mecanismos ya existen en el repositorio.
4. **Resultado:** Presenta los candidatos **ordenados por gravedad**.

---

## 🔎 Categorías de Mejora

| Categoría | Qué busca | Mejora posible |
| :--- | :--- | :--- |
| **Navegación** | Información difícil de localizar o dependencias ocultas | Punteros a archivos y documentación |
| **Comprobaciones automáticas** | Errores detectables, checks desconectados o ausencia de protección | Conectar lint, tipos o tests a pre-commit o CI |
| **Estándares de código** | Fallos que la revisión no detectó | Check determinista o criterio de revisión |
| **AGENTS.md global o del repo** | Instrucciones excesivas o mal ubicadas | Mover contenido a checks, estándares o referencias |
| **Economía de herramientas** | Llamadas caras o salidas que consumen demasiado contexto | Simplificar herramientas y consultas |
| **Instrucciones sin efecto** | Texto que no modifica el comportamiento | Eliminar instrucciones inútiles |
| **Acceso a información** | Datos esenciales inaccesibles | Logs del servidor o acceso de solo lectura a servicios |

---

## 💡 Conceptos Clave

### 1. Checks antes que reglas

- Una infracción **mecánica** (API prohibida, import incorrecto, ubicación de archivo) debe convertirse en una **comprobación determinista**.
- Antes de crearla, revisa los comandos del proyecto y su CI: un check existente pero desconectado es el hallazgo.
- Reserva `CODING_STANDARDS.md` para **juicios de criterio** que no pueda sustituir una herramienta.

### 2. Cada documento tiene una función

| Recurso | Uso recomendado |
| :--- | :--- |
| `AGENTS.md` / `CLAUDE.md` | Pocos punteros de navegación; se cargan en el contexto de todos los agentes |
| `CODING_STANDARDS.md` | Criterios que aplica el agente revisor; si supera 1.000 líneas, enlazar documentación |
| Documentación | Referencias duraderas; buscar las existentes antes de crear otras |
| Skills | Referencias descubribles y comandos invocados por el usuario |

> [!tip] **Reparto de contexto**  
> El implementador soporta la exploración, el código y la depuración. El revisor tiene menos presión de contexto y debe encargarse de aplicar los estándares.

---

## ⚠️ Puntos de Atención

- **Mejora el entorno:** El resultado son propuestas para futuras sesiones, no una revisión del diff.
- **No reinventa protecciones:** Comprueba primero qué lint, tests y CI existen y si funcionan.
- **Entrega candidatos:** El flujo descrito termina presentando propuestas; no incluye aplicarlas automáticamente.
