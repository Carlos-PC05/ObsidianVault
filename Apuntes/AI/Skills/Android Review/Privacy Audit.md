---
tags:
  - ai/skills
  - ai/android-review
  - android
source: https://github.com/liuyi0808/android-review/blob/main/skills/privacy-audit/SKILL.md
---
# Privacy Audit

## **Propósito**

Comparar lo que la aplicación recoge con lo que comunica su política y sus avisos.

## 🎯 ¿Cuándo usarla?

Para auditorías de privacidad de apps Android de préstamos, antes de publicar o tras cambios en datos y SDKs.

## 🔎 Qué revisa

La comparación tiene tres fuentes:

```text
Código y SDKs ↔ avisos dentro de la app ↔ política de privacidad
```

1. Inspeccionar primero el código: manifest efectivo, dependencias, peticiones, modelos, permisos, SDKs y textos de divulgación.
2. Pedir al usuario que elija la fuente de la política; no asumir automáticamente la URL encontrada.
3. Obtener la política y comparar datos, finalidad, destinatarios y consentimiento con la implementación y los avisos.
4. Documentar discrepancias, permisos sensibles y evidencia en el informe.

La revisión parte de los países reales de distribución y presta atención a datos de contactos, ubicación, SMS y registros de llamadas.

## 📄 Resultado

Informe de auditoría con evidencia y discrepancias. Complementa [[Play Store]] y [[Security Audit]].

## 🔗 Relacionado

* [[Android Review]]
