---
tags:
  - ai/skills
  - ai/android-review
  - android
source: https://github.com/liuyi0808/android-review/blob/main/skills/kotlin-quality/SKILL.md
---
# Kotlin Quality

## **Propósito**

Detectar problemas de corrección, seguridad de tipos y estilo idiomático en Kotlin.

## 🎯 ¿Cuándo usarla?

Al revisar corrutinas, Flow, nulabilidad, modelos de datos o colecciones.

## 🔎 Qué revisa

- **Corrutinas:** concurrencia estructurada, cancelación, tratamiento de excepciones y dispatcher apropiado; evitar `GlobalScope`.
- **Flow:** encapsular flujos mutables, recogerlos según el ciclo de vida y revisar la posición de `catch` y `flowOn`.
- **Nulabilidad:** justificar `!!` y `lateinit`, usar casts seguros y evitar cadenas difíciles de seguir.
- **Tipos:** usar modelos y clases selladas cuando expresen mejor los estados posibles.
- **Colecciones:** elegir operaciones claras y evitar trabajo o mutabilidad innecesarios.

## 📄 Resultado

Tras el triaje y la búsqueda de patrones, genera hallazgos `[KT-*]` priorizados.

## 🔗 Relacionado

* [[Android Review]]
