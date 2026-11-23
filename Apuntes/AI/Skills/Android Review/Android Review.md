---
tags:
  - ai/skills
  - ai/android-review
  - android
source: https://github.com/liuyi0808/android-review
---
# Android Review

> [!abstract] Resumen
> Conjunto de siete skills para revisar aplicaciones Android con Kotlin y Jetpack Compose. Organiza auditorías especializadas y produce hallazgos con evidencia y acciones propuestas.

## 📦 Skills del conjunto

| Skill | Nombre | Función |
| --- | --- | --- |
| [[Architecture]] | `architecture` | Revisar la estructura de una app Android y sus límites entre capas. |
| [[Compose UI]] | `compose-ui` | Revisar interfaces Jetpack Compose y sus patrones de estado, efectos y accesibilidad. |
| [[Kotlin Quality]] | `kotlin-quality` | Detectar problemas de corrección, seguridad de tipos y estilo idiomático en Kotlin. |
| [[Performance]] | `performance` | Auditar rendimiento Android y priorizar causas con acciones concretas. |
| [[Play Store]] | `play-store` | Revisar preparación y cumplimiento de Google Play antes de publicar. |
| [[Privacy Audit]] | `privacy-audit` | Comparar lo que la aplicación recoge con lo que comunica su política y sus avisos. |
| [[Security Audit]] | `security-audit` | Auditar la seguridad de Android usando las ocho áreas de OWASP MASVS v2.0. |

## 🧭 Cómo elegir

- Para diseño y mantenimiento diario: [[Architecture]], [[Compose UI]] y [[Kotlin Quality]].
- Para problemas de fluidez, consumo o arranque: [[Performance]].
- Para datos y amenazas: [[Security Audit]]; para contrastar código, avisos y política en préstamos: [[Privacy Audit]].
- Antes de publicar una app de préstamos: [[Play Store]], complementada con privacidad y seguridad.

## ⚙️ Instalación y uso

El repositorio admite Claude Code y Codex CLI. Para Codex, según el README:

```bash
codex plugin marketplace add https://github.com/liuyi0808/android-review
codex plugin add android-review@android-review
```

Reinicia Codex y comprueba que aparecen las siete skills. También pueden activarse describiendo la tarea, por ejemplo: «Revisa la arquitectura de este proyecto Android» o «Audita la privacidad de esta app de préstamos».

> [!note] Contexto y vigencia
> Las notas resumen la versión instalada 1.9.0 y el README del repositorio. Las políticas de Google Play y sus fechas pueden cambiar; comprueba las fuentes oficiales antes de una auditoría o publicación.

## 🔗 Fuente

* [Repositorio Android Review](https://github.com/liuyi0808/android-review)
