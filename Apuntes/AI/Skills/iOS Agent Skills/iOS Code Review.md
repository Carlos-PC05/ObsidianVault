---
tags:
  - ai/skills
  - ai/ios-agent-skills
  - ios
source: https://github.com/SH-OH/ios-agent-skills/blob/main/skills/ios-code-review/SKILL.md
---
# iOS Code Review

## **Propósito**

Revisar cambios Swift/iOS en seguridad, memoria, concurrencia y calidad.

## 🎯 ¿Cuándo usarla?

Antes de integrar cambios o al revisar un diff y su impacto en el código relacionado.

## 🔎 Qué cubre

- Inspeccionar el diff y los usos afectados por cambios de API.
- Buscar secretos, exposición de datos y prácticas inseguras.
- Comprobar ciclos de retención y gestión de memoria.
- Revisar aislamiento de concurrencia, Sendable y actualizaciones de UI en MainActor.
- Detectar force unwraps inseguros, complejidad y oportunidades de mejora.

## 📄 Resultado

Informe con hallazgos críticos, altos y medios, archivos revisados y veredicto.

## 🔗 Relacionado

* [[iOS Agent Skills]]
