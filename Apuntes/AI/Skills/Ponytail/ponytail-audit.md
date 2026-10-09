---
tags:
  - ai/skills
  - ai/ponytail
---

## **Propósito**

Es equivalente a `/ponytail-review`, pero en lugar de analizar únicamente un diff, realiza una **auditoría del repositorio completo**.

Busca:

> **¿Qué partes de este codebase podrían eliminarse o simplificarse?**

---

## 🎯 ¿Cuándo usarla?

Especialmente útil:

* Antes de un refactor importante.
* En proyectos antiguos.
* Después de muchas iteraciones con agentes de IA.
* Cuando sospechas que ha crecido demasiado la arquitectura.
* Antes de añadir nuevas capas.
* Cuando quieres reducir deuda de complejidad.

Ejemplo:

```text
/ponytail-audit
```

---

## 🔎 Qué busca

Entre otras cosas:

* Dependencias que sustituyen funcionalidad estándar.
* Interfaces con una sola implementación.
* Factories con un único producto.
* Wrappers que únicamente delegan.
* Archivos que solo exportan una cosa innecesariamente.
* Feature flags muertos.
* Configuración no utilizada.
* Código equivalente disponible en la stdlib.

Utiliza los mismos tags que `/ponytail-review`:

```text
delete:
stdlib:
native:
yagni:
shrink:
```

---

## 🔄 Flujo

```text
Repositorio completo
        ↓
Escaneo de complejidad
        ↓
Clasificación de findings
        ↓
Ordenar por mayor reducción posible
        ↓
Informe
```

Los resultados se ordenan intentando mostrar primero las oportunidades de reducción más importantes.

---

## 📄 Salida

Cada finding sigue aproximadamente:

```text
<tag> <qué eliminar>. <reemplazo>. [ruta]
```

Y termina con:

```text
net: -N lines, -M deps possible.
```

Si no encuentra nada significativo:

```text
Lean already. Ship.
```

---

## ⚠️ Límites

Solo analiza:

```text
over-engineering + complejidad
```

No está diseñada para encontrar:

* Bugs.
* Vulnerabilidades.
* Problemas de performance.

Además:

```text
/ponytail-audit
```

**no modifica el código.**

Solo genera el informe.

---
