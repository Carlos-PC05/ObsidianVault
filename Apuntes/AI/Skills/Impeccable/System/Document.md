---
aliases:
  - Impeccable Document
  - Generar DESIGN.md
  - Extraer Sistema de Diseño
tags:
  - dev/ai
  - design-system
  - impeccable
  - documentation
source: https://impeccable.style/docs/document/
---
# `/impeccable document` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable document` es el comando encargado de **inspeccionar y extraer el sistema de diseño existente** de un proyecto. Escanea la base de código actual (componentes, estilos, variables CSS y configuración) para generar de forma automatizada el archivo `DESIGN.md` en la raíz del repositorio. Sirve como fuente de verdad visual para que los agentes de IA no destruyan ni alteren la identidad establecida.

---

## 🎯 Objetivo Principal

* **Documentación Automática:** Analiza la arquitectura de UI sin requerir que redactes manualmente las guías de estilo.
* **Preservación de Tokens:** Mapea automáticamente variables CSS, clases de Tailwind, tokens de diseño y componentes clave del código.
* **Alineación con Agentes de IA:** Crea el archivo `DESIGN.md` para que los comandos de iteración y edición (`/polish`, `/layout`, `/typeset`, etc.) respeten el lenguaje visual ya implementado en el proyecto.

---

## 🔍 ¿Qué analiza el comando?

Al ejecutarse, la IA escanea el repositorio buscando:

1. **Tokens de Color:** Definiciones de temas (oscuro/claro), paletas primarias, neutras tintadas, estados y niveles de contraste.
2. **Tipografía & Escalas:** Fuentes importadas, jerarquías de texto, pesos (`font-weight`) y tamaños predefinidos.
3. **Layout & Espaciado:** Rejillas, contenedores, márgenes por defecto y tokens de espaciado.
4. **Librería de Componentes:** Patrones recurrentes de botones, tarjetas, inputs, modales y su estructura general.

---

## 📁 Archivo Generado

> [!file] Resultado principal
> * **`DESIGN.md`**: Guardado en la raíz del proyecto. Contiene la especificación completa del sistema de diseño extraído, estructurado de forma legible tanto para humanos como para context windows de LLMs.

---

## 💡 ¿En qué se diferencia de `/impeccable init`?

| Comando | Propósito | Cuándo usarlo |
| :--- | :--- | :--- |
| `/impeccable init` | Entrevista inicial + creación de `PRODUCT.md` y estructura base. | Al iniciar la configuración de Impeccable en el proyecto. |
| `/impeccable document` | Escaneo profundo del código para extraer y actualizar `DESIGN.md`. | Cuando el proyecto ya tiene código UI/tokens definidos o el sistema de diseño ha evolucionado. |

---

## 🚀 Uso Rápido

```bash
# Ejecutar la extracción del sistema de diseño dentro de tu cliente de IA
/impeccable document
```
