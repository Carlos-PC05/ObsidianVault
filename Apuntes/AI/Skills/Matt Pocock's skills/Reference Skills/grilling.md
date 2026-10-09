---
alias:
  - Skill /grilling
  - AI Hero Grilling
tags:
  - ai/skills
  - dev/discovery
  - dev/requirements
  - ai/reference
source: https://www.aihero.dev/skills-grilling
type: skill-note
---
> [!abstract] **Propósito**  
> Actúa como la **skill base/meta-concepto de interrogatorio** que orquesta el proceso de extracción de requisitos. Somete las ideas, borradores o specs del usuario a un cuestionamiento exhaustivo e incisivo para detectar supuestos no probados, *edge cases* y ambigüedades antes de escribir código.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Invocada mediante el concepto general de "grilling" o al enrutar hacia sus variantes específicas. Úsala cuando tengas un concepto borroso o inacabado y quieras exponer sus puntos débiles antes de pasar a la fase de diseño técnico.
- **Detiene la especulación:** Evita que el agente asuma soluciones por su cuenta cuestionando cada aspecto del problema.

| Situación                                                                          | Skill recomendada   |
| :--------------------------------------------------------------------------------- | :------------------ |
| Quieres un interrogatorio interactivo en tiempo real                               | `/grill-me`         |
| Quieres interrogar la idea contrastándola contra la base de código/docs existentes | `/grill-with-docs`  |
| Quieres convertir las dudas en un cuestionario asíncrono para enviar               | `/to-questionnaire` |
| Ya terminaste la fase de grilling y quieres redactar el spec                       | `/to-spec`          |

---

## 🔄 Dinámica del "Grilling"

1. **Ataque a la Ambigüedad:** Cuestiona afirmaciones genéricas (ej. *"debe ser rápido"*, *"manejar errores"*).
2. **Pruebas de Esquina (*Edge Cases*):** Plantea escenarios límite de concurrencia, permisos, estados vacíos o fallos de red.
3. **Clarificación de Reglas de Negocio:** Obliga a decidir comportamientos explícitos en lugar de dejar decisiones a la interpretación del modelo.
4. **Cierre por Agotamiento de Dudas:** Concluye únicamente cuando el espacio del problema está completamente delimitado.

---

## 💡 Conceptos Clave

- **Meta-patrón de Descubrimiento:** Es el fundamento sobre el que se construyen `/grill-me` y `/grill-with-docs`.
- **Inversión del Control:** La IA toma el rol de interrogador/auditor técnico y el usuario toma el rol de experto de dominio.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **No es un generador de código**  
> El objetivo del *grilling* es únicamente **descubrir información y cerrar ambigüedades**. No genera implementaciones ni modifica archivos del proyecto durante el interrogatorio.

- **Preguntas acotadas:** Las preguntas deben realizarse de forma secuencial o en bloques digestibles para no abrumar la sesión.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=grill-me
```