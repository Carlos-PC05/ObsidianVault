---
aliases:
  - Impeccable Onboard
  - Onboarding de Proyecto
  - Configurar Impeccable
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/onboard/
---
# `/impeccable onboard` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable onboard` es el comando encargado de **analizar un proyecto existente e inicializar la base de conocimiento de diseño** requerida por la suite Impeccable. Escanea el código fuente, la configuración de estilos y la estructura del proyecto para generar o actualizar automáticamente los archivos clave de contexto (`PRODUCT.md` y `DESIGN.md`).

---

## 🎯 Objetivo Principal

* **Extracción de Contexto:** Detecta automáticamente los tokens de diseño (paletas de color, fuentes, escalas de espaciado, componentes UI) ya presentes en el proyecto.
* **Documentación Centralizada:** Crea o alinea los archivos `PRODUCT.md` y `DESIGN.md` para que los demás comandos de Impeccable entiendan la identidad visual del sistema.
* **Adopción Inmediata:** Permite integrar Impeccable en proyectos legacy o en desarrollo activo sin necesidad de redactar manualmente toda la especificación de diseño.

---

## 🔍 ¿Cuándo utilizarlo?

* **Primera Vez en un Proyecto:** Al instalar o comenzar a usar Impeccable en un repositorio que no cuenta con archivos de contexto de diseño.
* **Tras Cambios Mayores de Estilo:** Cuando se actualiza la paleta de colores, la librería de UI o el framework de CSS (p. ej., Tailwind CSS, CSS Modules, Stitches) y se necesita sincronizar el contexto de la IA.
* **Normalización de Equipos:** Para alinear a todos los desarrolladores y agentes de IA bajo los mismos estándares de producto y diseño extraídos del código real.

---

## 📋 Flujo de Trabajo

1. **Escaneo del Repositorio:** Examina archivos de configuración (`tailwind.config.js`, variables CSS, temas), componentes existentes y paquetes instalados.
2. **Inferencia de Reglas:** Identifica patrones recurrentes de maquetación, paleta cromática dominante, tipografías y reglas de interacción.
3. **Generación de Archivos Core:** Produce los documentos `PRODUCT.md` (visión, tono y propósito) y `DESIGN.md` (tokens visuales, componentes y convenciones).

---

## 💡 ¿En qué se diferencia de otros comandos?

| Comando | Intención | Resultado |
| :--- | :--- | :--- |
| `/impeccable onboard` | **Configuración Inicial:** Inspecciona el proyecto y genera los archivos de contexto (`DESIGN.md` / `PRODUCT.md`). | La base de conocimiento lista para que la IA entienda el proyecto. |
| `/impeccable audit` | **Verificación:** Inspecciona el código buscando fallos frente al contexto ya establecido. | Reporte de inconsistencias, errores de accesibilidad o malas prácticas. |

---

## 🚀 Uso Rápido

```bash
# Inicializar y extraer el contexto de diseño del proyecto actual
/impeccable onboard
```
