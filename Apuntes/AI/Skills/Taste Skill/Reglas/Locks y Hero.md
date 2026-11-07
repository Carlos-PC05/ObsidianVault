---
aliases:
  - Taste Skill Locks
  - Locks
  - Hero discipline
  - Disciplina del hero
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - art-direction
source: https://www.tasteskill.dev/docs#locks
---
# Locks y Hero — Taste Skill

> [!abstract] Resumen Ejecutivo
> Los **locks** son tres reglas que el agente nunca relaja y que mantienen la página visualmente coherente de principio a fin. La **disciplina del hero** son reglas duras para lo que se ve antes de hacer scroll: el hero debe caber en el viewport inicial.

---

## 🔒 Los tres locks

### Color Consistency Lock

**Un accent en toda la página.** Un sitio de grises cálidos no recibe de repente un CTA azul en la sección 7.

### Shape Consistency Lock

**Un sistema de radios por página.** Todo afilado, todo suave o todo pill, con una excepción documentada cuando se mezclan.

### Page Theme Lock

**Light, dark o auto, elegido una vez a nivel de página.** Sin cambios de tema a mitad de página.

---

## 🦸 Disciplina del hero

| Regla | Límite |
| :--- | :--- |
| **Titular** | Máximo **dos líneas** en desktop. |
| **Subtexto** | Máximo **20 palabras** y **cuatro líneas**. |
| **CTA principal** | Visible **sin hacer scroll**. |
| **Navegación** | Una sola línea en desktop, altura máxima **80 px**. |

---

## 🧠 Por qué importan

> [!important] Coherencia de extremo a extremo
> Los locks evitan que una página se deshaga por partes: el color, la forma y el tema se deciden una vez y se respetan. La disciplina del hero evita que el visitante tenga que hacer scroll para saber qué es esto o qué puede hacer.

---

## ✅ Checklist

```text
[ ] Un solo accent en toda la página
[ ] Un solo sistema de radios
[ ] Un solo tema (light / dark / auto)
[ ] Titular del hero <= 2 líneas en desktop
[ ] Subtexto <= 20 palabras y <= 4 líneas
[ ] CTA visible sin scroll
[ ] Navegación en una línea y <= 80 px de alto
```

---

## 🔗 Relacionado

* [[House Rules]]
* [[Anti-Slop]]
* [[06 Construir]]
* [[07 Gate]]
