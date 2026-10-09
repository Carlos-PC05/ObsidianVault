---
tags:
  - ai/skills
  - ai/ponytail
---

## **Propósito**

Realiza una **code review enfocada exclusivamente en detectar over-engineering y complejidad innecesaria**.

A diferencia de una review tradicional, no intenta encontrar principalmente bugs.

Su objetivo es responder:

> **¿Qué código podemos eliminar o simplificar de este cambio?**

---

## 🎯 ¿Cuándo usarla?

Especialmente después de implementar:

* Una feature.
* Un refactor.
* Una corrección relativamente grande.
* Una nueva abstracción.
* Nuevas dependencias.
* Nuevas capas arquitectónicas.

Ejemplo:

```text
implementación
      ↓
tests
      ↓
/ponytail-review
      ↓
simplificación
```

---

## 🔎 Qué busca

Clasifica los hallazgos mediante cinco tags:

| Tag       | Significado                                                           |
| --------- | --------------------------------------------------------------------- |
| `delete:` | Código muerto, flexibilidad no utilizada o funcionalidad especulativa |
| `stdlib:` | Código manual que puede sustituirse por la librería estándar          |
| `native:` | Código/dependencia sustituible por funcionalidad nativa               |
| `yagni:`  | Abstracción que todavía no está justificada                           |
| `shrink:` | Misma lógica expresable con bastante menos código                     |

---

## 📄 Formato de salida

Ejemplo:

```text
L12-38: stdlib: validator class de 27 líneas. Sustituir por la funcionalidad estándar.
```

En múltiples archivos:

```text
repository.py:L88: yagni: AbstractRepository tiene una única implementación. Inline hasta que exista una segunda.
```

Al finalizar proporciona una estimación:

```text
net: -N lines possible.
```

Si el código ya es suficientemente simple:

```text
Lean already. Ship.
```

---

## ⚠️ Límites

Esta skill **NO revisa**:

* Bugs de correctness.
* Vulnerabilidades de seguridad.
* Problemas de rendimiento.

Debe complementarse con una code review normal.

Además:

```text
/ponytail-review
```

**solo genera recomendaciones. No aplica automáticamente los cambios.**

---