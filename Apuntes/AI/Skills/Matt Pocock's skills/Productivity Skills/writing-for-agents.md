---
alias:
  - Skill /writing-for-agents
  - AI Hero Writing For Agents
tags:
  - ai/skills
  - dev/prompting
  - dev/documentation
  - ai/productivity
source: https://www.aihero.dev/skills-writing-for-agents
type: skill-note
---
> [!abstract] **Propósito**  
> Formatea y estructura documentación, instrucciones o guías dentro del repositorio para que sean **altamente legibles y ejecutables por agentes de IA**, reduciendo la ambigüedad, previniendo alucinaciones y optimizando el consumo de contexto.

---

## 🎯 ¿Cuándo usarla?

- **Invocación:** Usa `/writing-for-agents` cuando quieras crear o refactorizar archivos de documentación orientados a la IA (como `CODING_STANDARDS.md`, `CLAUDE.md`, reglas de linter, o guías de arquitectura).
- **Optimización de contexto:** Transforma textos largos o ambiguos en instrucciones directas y procesables por modelos de lenguaje.

| Situación | Skill recomendada |
| :--- | :--- |
| Quieres escribir o mejorar documentación/reglas leídas por agentes | `/writing-for-agents` |
| Quieres redactar una especificación técnica de una característica | `/to-spec` |
| Quieres explicar un concepto a un desarrollador humano | `/teach` |

---

## 📝 Principios de Redacción para Agentes

1. **Imperativos e Instrucciones Claras:** Sustituye descripciones ambiguas por mandatos directos y comprobables (ej. *"Usa siempre X en lugar de Y"*).
2. **Estructura Semántica y Delimitadores:** Utiliza encabezados, listas y bloques de código explícitos para marcar límites de contexto.
3. **Casos Positivos y Negativos (Do's & Don'ts):** Muestra ejemplos concretos de código aceptado y código rechazado.
4. **Criterios de Verificación:** Define cómo el agente puede comprobar si cumplió la regla (ej. *"Corre `npm run lint` al finalizar"*).

---

## 💡 Conceptos Clave

- **Documentación Ejecutable:** Trata la documentación como si fuera un programa/instrucción para el agente, no un ensayo para humanos.
- **Eficiencia de Tokens:** Elimina la paja informativa, adjetivos floridos e introducciones innecesarias que solo gastan ventana de contexto.

---

## ⚠️ Puntos de Atención y Reglas

> [!warning] **Escribir para IA ≠ Escribir para Humanos**  
> Los agentes necesitan instrucciones deterministas y ejemplos delimitados, no explicaciones narrativas extensas.

- **Fácilmente parseable:** Prioriza formatos estándar en Markdown limpio sobre maquetaciones complejas o imágenes.

---

## 📦 Instalación

```bash
npx skills@latest add mattpocock/skills --skill=writing-for-agents
```