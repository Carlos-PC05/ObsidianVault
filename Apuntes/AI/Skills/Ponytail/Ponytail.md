---
tags:
  - ai/skills
  - ai/ponytail
---


Ponytail es un conjunto de skills orientadas a combatir el **over-engineering** durante el desarrollo de software.

Su filosofía principal es:

> **La mejor solución es la solución más pequeña que funciona correctamente.**

Prioriza **YAGNI**, reutilización del código existente, librería estándar, capacidades nativas de la plataforma, dependencias ya instaladas y, finalmente, la mínima cantidad de código necesaria.

Las skills disponibles son:

| Skill              | Función principal                                           |
| ------------------ | ----------------------------------------------------------- |
| `/ponytail`        | Activa el modo de desarrollo minimalista                    |
| `/ponytail-review` | Revisa un diff buscando over-engineering                    |
| `/ponytail-audit`  | Audita todo el repositorio buscando complejidad innecesaria |
| `/ponytail-debt`   | Localiza deuda técnica marcada por Ponytail                 |
| `/ponytail-gain`   | Muestra métricas del benchmark de Ponytail                  |
| `/ponytail-help`   | Muestra la referencia rápida de comandos                    |

---

# 🐴 `/ponytail`

## **Propósito**

Fuerza al agente a buscar **la solución más sencilla, corta y mantenible que realmente resuelva el problema**.

Actúa como un desarrollador senior que intenta reducir código, abstracciones, dependencias y complejidad antes de añadir nuevas piezas al sistema.

No significa escribir código descuidado. La filosofía es:

> **Lazy means efficient, not careless.**

Ponytail intenta escribir **menos código después de entender completamente el problema**.

---

## 🎯 ¿Cuándo usarla?

Puede utilizarse prácticamente durante cualquier tarea de programación:

* Implementar una feature.
* Corregir un bug.
* Refactorizar.
* Diseñar una solución.
* Elegir una librería.
* Decidir si añadir una dependencia.
* Simplificar código existente.
* Evitar arquitecturas innecesariamente complejas.

Especialmente útil cuando sospechas que el agente está cayendo en:

* Over-engineering.
* Abstracciones prematuras.
* Demasiado boilerplate.
* Dependencias innecesarias.
* Interfaces con una única implementación.
* Factories con un único producto.
* Configuración para valores que nunca cambian.
* Código preparado para hipotéticos requisitos futuros.

---

## ⚙️ Modos de intensidad

Ponytail dispone de tres niveles:

| Nivel     | Comando           | Comportamiento                                                           |
| --------- | ----------------- | ------------------------------------------------------------------------ |
| **Lite**  | `/ponytail lite`  | Implementa lo solicitado, pero menciona una alternativa más simple       |
| **Full**  | `/ponytail`       | Aplica estrictamente la escalera de simplificación. **Modo por defecto** |
| **Ultra** | `/ponytail ultra` | YAGNI extremo. Intenta eliminar requisitos y código antes de añadir nada |

El nivel permanece activo durante la sesión hasta cambiarlo.

Para desactivarlo:

```text
stop ponytail
```

```text
normal mode
```

o:

```text
/ponytail off
```

---

## 🔄 La escalera de Ponytail

Antes de escribir código, el agente intenta resolver el problema siguiendo este orden:

```text
1. ¿Realmente necesita existir?
        ↓
2. ¿Ya existe algo equivalente en el codebase?
        ↓
3. ¿Lo resuelve la Standard Library?
        ↓
4. ¿Lo resuelve una funcionalidad nativa de la plataforma?
        ↓
5. ¿Una dependencia ya instalada lo resuelve?
        ↓
6. ¿Puede resolverse en una línea?
        ↓
7. Escribir el mínimo código necesario
```

La regla fundamental es:

> **Stop at the first rung that holds.**

Si una solución situada más arriba en la escalera resuelve correctamente el problema, no se continúa descendiendo.

---

## 💡 Conceptos Clave

### 1. YAGNI

**You Aren't Gonna Need It.**

Si una necesidad es puramente especulativa, Ponytail evita implementarla.

Ejemplos:

```text
Interface con una única implementación
→ eliminar interface
```

```text
Factory con un único producto
→ instanciar directamente
```

```text
Configuración para un valor constante
→ usar directamente el valor
```

---

### 2. Reutilizar antes de crear

Antes de implementar algo nuevo, Ponytail debe comprobar si el proyecto ya contiene:

* Helpers.
* Utilities.
* Types.
* Funciones similares.
* Patrones equivalentes.

Reimplementar algo que ya existe unas carpetas más allá se considera complejidad innecesaria.

---

### 3. Standard Library primero

Siempre que sea posible:

```text
stdlib > librería externa > implementación propia
```

Ejemplo conceptual:

```python
@lru_cache(maxsize=1000)
```

antes que implementar una infraestructura propia de caché si `functools.lru_cache` cubre el caso.

---

### 4. Capacidades nativas antes que dependencias

Ejemplos:

```html
<input type="date">
```

antes que instalar una librería completa de date pickers.

O:

```text
DB constraint
```

antes que implementar la misma garantía únicamente mediante lógica de aplicación.

---

### 5. Bug fix = Root Cause

Ponytail no interpreta "cambio mínimo" como "parchear el síntoma".

Antes de modificar una función compartida debe comprobar sus callers.

Por ejemplo:

```text
Guard en cinco endpoints
```

es peor que:

```text
Guard en la función compartida utilizada por los cinco endpoints
```

El segundo cambio es realmente más pequeño y corrige la causa raíz.

---

### 6. Eliminación antes que adición

Cuando existen varias soluciones válidas:

```text
deletion > addition
boring > clever
short diff > large diff
```

Pero solo **después de entender correctamente el flujo afectado**.

---

### 7. Comentarios `ponytail:`

Cuando el agente realiza deliberadamente una simplificación que tiene un límite conocido debe documentarla.

Ejemplo:

```python
# ponytail: global lock, use per-account locks if throughput becomes a problem
```

La estructura conceptual es:

```text
ponytail: <limitación actual>, <condición o camino de mejora>
```

Estos comentarios posteriormente pueden encontrarse con `/ponytail-debt`.

---

## 🧪 Testing

Ponytail también aplica minimalismo a las pruebas.

Para lógica no trivial —branches, loops, parsers, dinero, seguridad, etc.— debe existir al menos **una comprobación ejecutable** que falle si la lógica se rompe.

Puede ser:

```text
1 pequeño test
```

o incluso:

```python
assert ...
```

No introduce automáticamente:

* Frameworks adicionales.
* Fixtures complejas.
* Suites gigantes.
* Tests por cada función trivial.

Pero tampoco elimina controles necesarios simplemente por reducir código.

---

## 🚫 Qué NO debe simplificar

Ponytail no debe sacrificar:

* Validación en trust boundaries.
* Seguridad.
* Prevención de pérdida de datos.
* Error handling importante.
* Accesibilidad básica.
* Requisitos explícitamente solicitados.

Tampoco debe evitar comprender el problema para producir un diff pequeño.

```text
Understand first → simplify second.
```

---
# 🔄 Flujo de trabajo recomendado

Las skills forman un flujo bastante coherente:

```text
                 desarrollo
                     │
                     ▼
               /ponytail
                     │
           solución mínima viable
                     │
                     ▼
             tests / checks
                     │
                     ▼
           /ponytail-review
                     │
          ¿podemos quitar algo?
                     │
                     ▼
                  commit
```

Para mantenimiento del proyecto:

```text
            repositorio
                │
                ▼
        /ponytail-audit
                │
        complejidad existente
                │
                ▼
        simplificación/refactor
```

Y para deuda deliberadamente pospuesta:

```text
ponytail: comments
        │
        ▼
 /ponytail-debt
        │
        ▼
 ledger de deuda
```

---

# 🧠 Cómo encaja con otras Skills de desarrollo

Ponytail no sustituye skills como:

```text
/implement
/tdd
/code-review
```

Tiene otra responsabilidad.

Conceptualmente:

```text
/spec
   │
   ▼
/tickets
   │
   ▼
/implement + /ponytail
   │
   ▼
/tdd
   │
   ▼
/code-review
   │
   ├──── correctness
   ├──── security
   └──── performance
   │
   ▼
/ponytail-review
   │
   └──── complejidad innecesaria
```

Por tanto:

```text
code-review ≠ ponytail-review
```

Una intenta comprobar que el cambio es **correcto**.

La otra intenta comprobar que el cambio es **más complejo de lo necesario**.

Las dos son complementarias.

---

# 📌 Chuleta rápida

| Quiero...                                                       | Skill              |
| --------------------------------------------------------------- | ------------------ |
| Que el agente implemente de la forma más sencilla posible       | `/ponytail`        |
| Seguir los requisitos pero recibir alternativas más simples     | `/ponytail lite`   |
| Aplicar YAGNI de forma agresiva                                 | `/ponytail ultra`  |
| Revisar si el código que acabo de escribir está over-engineered | `/ponytail-review` |
| Revisar todo el proyecto buscando complejidad innecesaria       | `/ponytail-audit`  |
| Ver qué simplificaciones se dejaron pendientes                  | `/ponytail-debt`   |
| Ver los benchmarks de Ponytail                                  | `/ponytail-gain`   |
| Recordar los comandos disponibles                               | `/ponytail-help`   |

---

# ⭐ Combinación práctica

Para desarrollo agéntico, una combinación especialmente útil sería:

```text
/implement
    +
/ponytail full
```

durante la implementación, seguido de:

```text
tests
      ↓
/code-review
      ↓
/ponytail-review
```

De esta forma existen **dos filtros distintos**:

```text
Code Review
→ ¿Está bien implementado?

Ponytail Review
→ ¿Era necesario implementar tanto?
```

Y periódicamente:

```text
/ponytail-audit
```

para evitar que la complejidad acumulada del proyecto crezca sin control.
