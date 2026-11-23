---
tags:
  - ai/skills
  - ai/ios-agent-skills
  - ios
source: https://github.com/SH-OH/ios-agent-skills/blob/main/skills/ios-security-review/SKILL.md
---
# iOS Security Review

## **Propósito**

Revisar vulnerabilidades de apps iOS con referencia a OWASP Mobile Top 10.

## 🎯 ¿Cuándo usarla?

Al tratar credenciales, red, autenticación biométrica, pagos o datos sensibles.

## 🔎 Qué cubre

- Usar Keychain para secretos y revisar accesibilidad y ciclo de vida de los datos.
- Revisar App Transport Security (ATS), TLS y excepciones de red.
- Evaluar autenticación biométrica y validación de compras sin confiar únicamente en el cliente.
- Buscar secretos embebidos y datos sensibles en logs o UserDefaults.
- Revisar Privacy Manifest y requisitos aplicables de App Store.

## 📄 Resultado

Informe por severidad con evidencia, impacto y correcciones; incluye checklist de seguridad.

## 🔗 Relacionado

* [[iOS Agent Skills]]
