---
tags:
  - ai/skills
  - ai/ponytail
---

## **Propósito**

Muestra un pequeño **scoreboard con los resultados de los benchmarks publicados por Ponytail**.

Sirve para visualizar el impacto observado de la filosofía Ponytail sobre:

* Líneas de código.
* Coste.
* Velocidad.

---

## 🎯 ¿Cuándo usarla?

Cuando quieras consultar rápidamente:

```text
/ponytail-gain
```

o entender qué impacto obtuvo Ponytail en sus benchmarks.

No analiza el repositorio.

---

## 📊 Métricas

Los benchmarks publicados comparan cinco tareas cotidianas:

```text
email validator
debounce
CSV sum
countdown timer
rate limiter
```

utilizando tres modelos:

```text
Haiku
Sonnet
Opus
```

El scoreboard original resume aproximadamente:

```text
Lines of code

no-skill   ████████████████████ 100%
ponytail   ██▌················  6–20%

reducción: 80–94%
```

```text
Cost

no-skill   ████████████████████ 100%
ponytail   █████▌············· 23–53%

reducción: 47–77%
```

Y para velocidad:

```text
3–6× faster
```

---

## ⚠️ Concepto importante: Honestidad del benchmark

Estas métricas son:

```text
benchmark medians
```

NO métricas calculadas sobre tu repositorio.

Ponytail explícitamente evita afirmar cosas como:

```text
"Ponytail te ha ahorrado 3.412 líneas en este proyecto"
```

porque el código alternativo nunca llegó a escribirse y, por tanto, no existe un baseline real contra el que comparar.

Para información específica del proyecto:

```text
/ponytail-debt
```

muestra deuda real contabilizable, mientras:

```text
/ponytail-audit
```

muestra código que todavía podría reducirse.

---

## ⚠️ Límites

Es completamente:

```text
read-only
```

y:

```text
one-shot
```

No:

* Cambia el modo Ponytail.
* Modifica código.
* Escribe archivos.
* Persiste configuración.

---
