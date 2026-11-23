---
tags:
  - ai/skills
  - ai/android-review
  - android
source: https://github.com/liuyi0808/android-review/blob/main/skills/architecture/SKILL.md
---
# Architecture

## **Propósito**

Revisar la estructura de una app Android y sus límites entre capas.

## 🎯 ¿Cuándo usarla?

Al diseñar una feature, revisar ViewModels, repositorios, casos de uso, Hilt o modularización.

## 🔎 Qué revisa

- **Capas:** presentación, dominio y datos; el dominio no debe depender de detalles de Android o de datos.
- **Presentación:** ViewModel por pantalla, estado de UI inmutable y responsabilidades claras.
- **Dominio:** casos de uso de propósito acotado e interfaces de repositorio.
- **Datos:** separar DTO, entidades y modelos de dominio mediante mapeos.
- **Hilt y módulos:** revisar scopes, bindings, dependencias y ciclos.
- **Errores:** traducir errores de infraestructura a errores comprensibles para el dominio.

## 📄 Resultado

Hallazgos con identificadores `[ARCH-*]`, severidad, ubicación y propuesta de corrección.

## 🔗 Relacionado

* [[Android Review]]
