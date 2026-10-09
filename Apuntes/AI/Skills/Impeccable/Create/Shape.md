---
aliases:
  - Impeccable Shape
  - Definir Forma
  - Geometría y Radio
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/shape/
---
# `/impeccable shape` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable shape` es el comando especializado en **definir y armonizar la geometría, bordes y esquinas redondeadas (`border-radius`)** de la interfaz. Garantiza que todos los elementos contenedores, botones, tarjetas, inputs y modales mantengan un lenguaje visual coherente en sus formas geométricas, evitando mezclas inconsistentes de esquinas o bordes fuera del sistema de diseño.

---

## 🎯 Objetivo Principal

* **Coherencia Geométrica:** Establece y aplica escalas unificadas para el rediseño de bordes (`border-radius`, espesor de trazados, sombras proyectadas).
* **Jerarquía de Esquinas:** Aplica la regla de proporcionalidad donde los elementos contenedores padres tienen radios mayores que los elementos hijos alojados en su interior (*nested radii*).
* **Alineación con la Identidad:** Traduce la personalidad descrita en `PRODUCT.md` / `DESIGN.md` en formas tangibles (p. ej., bordes completamente rectos para tonos sobrios/técnicos, o bordes suaves/redondeados para tonos amigables).

---

## 🔍 ¿Cuándo utilizarlo?

* **Inconsistencia de Bordes:** Cuando encuentras botones redondos junto a tarjetas con esquinas rectangulares duras sin justificación de diseño.
* **Ajuste de Esquinas Anidadas:** Para corregir el choque visual que ocurre cuando el radio de un elemento interno no encaja armónicamente con el borde de su contenedor.
* **Refinamiento de Superficies:** Al ajustar bordes, delimitadores, divisores (`dividers`) o sombras para definir la elevación y estructura geométrica de la UI.

---

## 📋 Flujo de Trabajo

1. **Inspección de Tokens:** Lee las variables o clases relativas a bordes y formas definidas en `DESIGN.md` (p. ej. `rounded-sm`, `rounded-lg`, etc.).
2. **Evaluación de Estructura:** Analiza la relación jerárquica entre contenedores e ítems internos.
3. **Normalización:** Refactoriza las clases/estilos de los elementos seleccionados para asegurar que todas las esquinas y contornos sigan la escala geométrica del proyecto.

---

## ⚙️ Principios Clave que Aplica

> [!check] Reglas de Geometría
> * **Proporción Anidada:** Mantiene el cálculo de radios concéntricos ($r_{padre} = r_{hijo} + padding$) para evitar solapamientos o huecos no naturales.
> * **Eliminación de Mezclas Arbitrarias:** Reemplaza valores *hardcodeados* (como `border-radius: 7px`) por las variables o clases estándar del sistema.
> * **Intencionalidad:** Asegura que los bordes marcados o acentuados respondan a la función del componente dentro de la jerarquía visual.

---

## 🚀 Uso Rápido

```bash
# Aplicar o corregir la geometría y bordes de un componente o vista
/impeccable shape [componente o archivo]
```
