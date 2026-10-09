---
aliases:
  - Impeccable Extract
  - Extraer Componente
  - Refactorizar UI
tags:
  - dev/ai
  - design-system
  - impeccable
  - refactoring
source: https://impeccable.style/docs/extract/
---

# `/impeccable extract` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable extract` es el comando diseñado para **aislar, modularizar y extraer patrones de UI repetitivos** o código visual sobrecargado en componentes limpios y reutilizables. Analiza fragmentos de interfaz dentro de un archivo y los convierte en un nuevo componente independiente, manteniendo la consistencia del sistema de diseño y actualizando las referencias automáticamente.

---

## 🎯 Objetivo Principal

* **Modularización de Interfaces:** Elimina el código UI monolítico o duplicado extrayéndolo a componentes autónomos.
* **Mantenimiento del Sistema:** Asegura que los componentes extraídos sigan las convenciones, tokens e interfaces definidos en `DESIGN.md`.
* **Refactorización Limpia:** Reemplaza el bloque original por la instancia del nuevo componente sin romper dependencias ni props.

---

## 🔍 ¿Cuándo utilizarlo?

* **Archivos Sobrecargados:** Cuando una página o vista tiene cientos de líneas de marcado HTML/JSX difícil de leer.
* **Patrones Repetidos:** Cuando identificas elementos visuales duplicados (tarjetas, botones complejos, ítems de lista, controles de formulario).
* **Preparación para Escalar:** Cuando necesitas mover una sección específica de UI a una carpeta centralizada de componentes (`/components`).

---

## 📋 Flujo de Trabajo

1. **Selección/Indicación:** Señalas a la IA el fragmento de código, elemento o sección que deseas extraer.
2. **Análisis de Propiedades:** El comando analiza qué datos, callbacks o estado local consume ese bloque para definir su interfaz de *props/types*.
3. **Creación del Componente:** Genera el nuevo archivo de componente en la ubicación adecuada de tu proyecto.
4. **Reemplazo Automático:** Importa y sustituye el bloque original en el archivo fuente por el nuevo componente.

---

## ⚙️ Características Clave

> [!check] Buenas Prácticas Aplicadas
> * **Typing Riguroso:** Asigna tipos explícitos a las *props* del nuevo componente (TypeScript / PropTypes).
> * **Respeto de Tokens:** Mantiene las clases de utilidades (Tailwind/CSS) alineadas al sistema de diseño.
> * **Cero Regresiones:** Preserva la lógica de negocio y eventos existentes al aislar el código.

---

## 🚀 Uso Rápido

```bash
# Ejemplo de uso en el chat de tu agente de IA (Cursor, Claude Code, etc.)
/impeccable extract [sección o elemento a aislar]
```
