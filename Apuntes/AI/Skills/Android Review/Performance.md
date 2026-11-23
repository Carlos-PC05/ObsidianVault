---
tags:
  - ai/skills
  - ai/android-review
  - android
source: https://github.com/liuyi0808/android-review/blob/main/skills/performance/SKILL.md
---
# Performance

## **Propósito**

Auditar rendimiento Android y priorizar causas con acciones concretas.

## 🎯 ¿Cuándo usarla?

Ante arranque lento, jank, fugas, ANR, consumo de batería o antes de una release.

## 🔎 Qué revisa

- **Arranque:** buscar inicializaciones síncronas y SDKs que bloqueen el hilo principal.
- **Compose:** revisar recomposiciones, estabilidad y claves en listas.
- **Memoria y ANR:** buscar referencias que retengan actividades, scopes sin cancelar y operaciones bloqueantes.
- **Listas:** evaluar composición perezosa y paginación cuando corresponda.
- **Baseline Profiles:** comprobar su existencia; la skill indica no omitir esta categoría.
- **Batería y red:** revisar trabajo en segundo plano, ubicación, sensores y caché.

La skill usa como referencias orientativas arranque en frío inferior a 500 ms y ANR inferior al 0,47 %. Medir en dispositivos y contextos reales antes de concluir.

## 📄 Resultado

Informe `[PERF-*]` por impacto, con causa, corrección, métricas y categorías omitidas justificadas.

## 🔗 Relacionado

* [[Android Review]]
