---
tags:
  - ai/skills
  - ai/ios-agent-skills
  - ios
source: https://github.com/SH-OH/ios-agent-skills/blob/main/skills/xcode-build-resolver/SKILL.md
---
# Xcode Build Resolver

## **Propósito**

Diagnosticar y corregir errores de compilación de Swift y Xcode con cambios acotados.

## 🎯 ¿Cuándo usarla?

Ante errores de tipos, dependencias, linking, firma o configuración de build.

## 🔎 Qué cubre

- Leer el error completo y reunir el contexto antes de modificar código.
- Intentar una comprobación rápida de tipos cuando sea aplicable; ejecutar el build completo cuando haga falta.
- Resolver causas como inferencia de tipos, opcionales, conformidad de protocolos y property wrappers.
- Revisar scheme, firma y dependencias si el error apunta a configuración.
- Verificar el build y evitar limpiar, suprimir avisos o cambiar arquitectura como primera reacción.

## 📄 Resultado

Informe de errores corregidos y verificación del build.

## 🔗 Relacionado

* [[iOS Agent Skills]]
