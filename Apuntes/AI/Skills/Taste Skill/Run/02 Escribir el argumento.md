---
aliases:
  - Taste Skill Paso 2
  - Escribir el argumento
  - Claims con prueba
tags:
  - dev/ai
  - design-system
  - taste-skill
  - taste-skill/run
  - frontend
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# 02 — Escribir el argumento

> [!abstract] Resumen Ejecutivo
> Antes de diseñar, la página se escribe como una lista de **claims con su prueba**, una línea por sección. Los claims deciden qué secciones existen y en qué orden. Cualquier sección que no tenga un claim se elimina. Así se evita la secuencia stock de hero, logos, tres features, testimonio, pricing y CTA.

---

## 🎯 Objetivo Principal

* **Justificar cada sección:** si no hay claim, no hay sección.
* **Ordenar por argumento:** el orden lo dicta lo que el visitante necesita creer, no una plantilla.
* **Alimentar el draw:** los roles resultantes se pasan a `draw.mjs` con `--sections`.

---

## 📋 Flujo de Trabajo

1. **Escribe la página como claims:** una frase por sección que diga qué afirma la página.
2. **Añade la prueba:** cada claim necesita algo que lo sostenga (una foto, un número, una frase real, una captura, un dato del brief).
3. **Elimina lo que no tenga claim:** fuera secciones decorativas o heredadas de la plantilla.
4. **Ordena:** el argumento decide el orden; no el orden habitual de una landing.
5. **Pasa los roles al draw:** `--sections features,showcase,proof,pricing,faq,cta`.

---

## 🧾 Formato

```text
Sección     | Claim                                      | Prueba
----------- | ------------------------------------------ | -----------------------------
Hero        | Es una panadería de masa madre en Triana.  | Foto del obrador y del barrio
Origen      | Solo usan harina molida a la piedra.       | Nombre del molino y foto
Producto    | Hay tres panes, no treinta.                | Lista real con precios
Horario     | Abren solo de jueves a domingo.            | Horario impreso
```

---

## 💡 Regla clave

> [!important] Una sección sin claim es una sección que sobra
> El argumento es lo que evita que la página sea una sucesión de bloques porque "toda landing lleva tres features".

---

## 🔗 Relacionado

* [[01 Leer el brief]]
* [[03 Sortear la dirección]]
* [[05 Escribir el copy]]
