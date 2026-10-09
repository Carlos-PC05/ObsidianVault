---
tags:
  - ai/skills
  - ai/ponytail
---

## **Propósito**

Recopila todos los comentarios:

```text
ponytail:
```

del repositorio y genera un **ledger de deuda técnica deliberadamente pospuesta**.

Su objetivo es evitar que:

```text
"lo simplificamos ahora y ya lo mejoraremos"
```

termine convirtiéndose en:

```text
"nadie volvió a acordarse".
```

---

## 🎯 ¿Cuándo usarla?

Después de haber utilizado Ponytail durante cierto tiempo.

Especialmente útil:

* Antes de una release.
* Durante mantenimiento.
* Durante una revisión de deuda técnica.
* Antes de planificar un nuevo sprint.
* Cuando quieres revisar qué decisiones minimalistas tienen límites conocidos.

Ejemplo:

```text
/ponytail-debt
```

---

## 🔍 Qué busca

Escanea comentarios como:

```python
# ponytail: global lock, use per-account locks if throughput becomes a problem
```

o:

```javascript
// ponytail: linear scan, replace with index if dataset exceeds 100k rows
```

Conceptualmente realiza algo parecido a:

```bash
grep -rnE '(#|//) ?ponytail:' .
```

ignorando directorios como:

```text
node_modules/
.git/
build outputs
```

---

## 📒 Ledger

Cada marcador se convierte en una entrada similar a:

```text
<file>:<line>, <simplificación>.
ceiling: <limitación>.
upgrade: <condición para revisarla>.
```

Ejemplo conceptual:

```text
cache.py:42
simplificación: cache global
ceiling: alto contention con muchos usuarios
upgrade: introducir locks por cuenta si throughput se convierte en problema
```

---

## 🚨 `no-trigger`

Si encuentra:

```text
ponytail: <algo pendiente>
```

pero no existe ninguna condición concreta para revisarlo, lo marca como:

```text
no-trigger
```

Esto indica mayor riesgo de que esa deuda permanezca indefinidamente.

---

## 📊 Resultado

Finaliza con:

```text
N markers, M with no trigger.
```

Si no existe deuda registrada:

```text
No ponytail: debt. Clean ledger.
```

---

## ⚠️ Límites

Por defecto:

```text
lee + informa
```

pero **no modifica archivos**.

Si se solicita explícitamente, puede persistir el resultado en algo como:

```text
PONYTAIL-DEBT.md
```

---
