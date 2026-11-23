---
tags:
  - ai/skills
  - ai/android-review
  - android
source: https://github.com/liuyi0808/android-review/blob/main/skills/security-audit/SKILL.md
---
# Security Audit

## **Propósito**

Auditar la seguridad de Android usando las ocho áreas de OWASP MASVS v2.0.

## 🎯 ¿Cuándo usarla?

Al revisar almacenamiento sensible, autenticación, criptografía, red, WebView o componentes exportados.

## 🔎 Qué revisa

- **STORAGE y CRYPTO:** almacenamiento, copias de seguridad, secretos, claves y algoritmos.
- **AUTH y NETWORK:** sesiones, biometría, TLS y validación de certificados.
- **PLATFORM y CODE:** intents, WebView, proveedores de contenido, logs y configuración de release.
- **RESILIENCE y PRIVACY:** manipulación, ingeniería inversa, minimización y consentimiento.
- Buscar evidencias concretas de secretos embebidos, tráfico inseguro o exposición de datos y evaluar el riesgo según el contexto.

## 📄 Resultado

Cada hallazgo incluye severidad, categoría MASVS, archivo, evidencia, impacto, mitigación y referencias. Las recomendaciones deben adaptarse al modelo de amenazas y documentación actual.

## 🔗 Relacionado

* [[Android Review]]
