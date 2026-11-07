---
aliases:
  - Taste Skill Paso 3
  - Sortear la dirección
  - draw.mjs
  - Direction sheet
tags:
  - dev/ai
  - design-system
  - taste-skill
  - taste-skill/run
  - frontend
  - art-direction
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# 03 — Sortear la dirección

> [!abstract] Resumen Ejecutivo
> `scripts/draw.mjs` reparte **tres de veinte direcciones investigadas** y luego imprime la ficha completa de la elegida: tipo, paleta, hero y composiciones de sección. La dirección es una forma de componer, no la idea. Se adopta entera, sin mezclarla con el layout que ibas a hacer de todos modos. Solo se redibuja una ranura por una razón que puedas explicar.

---

## 🎯 Objetivo Principal

* **Evitar el default de categoría:** cuando un modelo inventa un look, elige siempre el mismo.
* **Repartir, no inventar:** la librería está destilada de 247 sitios reales.
* **Elegir con el inventario en la mano:** la dirección debe poder llevar algo que solo tiene este cliente.

---

## 📋 Flujo de Trabajo

1. **Lista los sectores y roles disponibles:**

```bash
node <skill>/scripts/draw.mjs --list sectors
node <skill>/scripts/draw.mjs --list roles
```

2. **Reparte tres direcciones:**

```bash
node <skill>/scripts/draw.mjs --sector <sector> --brief "<one line about the subject>" \
  --sections <roles in the order of your argument> [--tone <tone>] [--have <assets>] [--keep palette,type] \
  --options 3
```

3. **Elige una:** contrástala con el inventario del paso 1. ¿Qué forma de componer puede llevar algo único de este cliente? Escribe una línea en tus notas explicando por qué.
4. **Reparte la ficha completa:**

```bash
node <skill>/scripts/draw.mjs <the same arguments, without --options> --direction <chosen id>
```

5. **Adóptala:** no la mezcles con tu layout habitual. Si se siente desconocida, bien: el layout familiar es lo que la skill existe para prevenir.

---

## ⚙️ Parámetros

| Flag | Qué hace |
| :--- | :--- |
| `--sector` | Sector más cercano. Se lista con `--list sectors`. |
| `--sections` | Roles en el orden del argumento: `features`, `showcase`, `how-it-works`, `testimonial`, `proof`, `stats`, `pricing`, `faq`, `cta`, `footer`, `intro`, `about`, `work`, `gallery`, `list`, `contact`. El hero va implícito. |
| `--tone` | Opcional: `quiet`, `bold`, `warm`, `technical`, `playful`, `luxury`, `editorial`. Solo si el brief lo pide claramente. |
| `--have` | Activos que existen de verdad: `photography`, `product-photo`, `product-ui`, `illustration`, `video`. Si no hay, se omite. |
| `--keep palette,type` | Cuando la marca ya fija colores o tipografías. El draw solo reparte composición. |

---

## 🎨 La ficha de dirección

Contiene la dirección de arte completa del build:

* **Composición:** cómo se organizan las secciones.
* **Tipografía:** familia, escala, tracking y leading.
* **Paleta:** ground, ink, accent y proporciones.
* **Hero:** blueprint del primer pantallazo.
* **Secciones:** una composición distinta por rol.
* **Escenas:** si toca scroll scenes, son parte de la composición.

---

## 🔁 Redibujar

Solo por una razón que puedas declarar:

```bash
# Ver alternativas para un rol que no aguanta el contenido real
node <skill>/scripts/draw.mjs ... --alternatives <role>

# Tomar una alternativa
node <skill>/scripts/draw.mjs ... --redraw section:<role>=<id>

# Redibujar una ranura
node <skill>/scripts/draw.mjs ... --redraw hero|type|palette|photos|scenes|direction

# Reimprimir la ficha actual
node <skill>/scripts/draw.mjs --show
```

Razones válidas:

* Contradice una restricción de marca declarada.
* Necesita un activo que no existe.
* La sección no puede contener el contenido real.

> [!warning] Razones inválidas
> Redibujar para conseguir algo "más cómodo" o más parecido a lo que ibas a hacer. Cada redraw se registra en el informe.

---

## 🖼️ Paleta y sujeto

Una paleta es un conjunto de proporciones (un ground, un ink, un accent, campos) tanto como un conjunto de colores.

* Si los colores chocan con el material del sujeto (pasteles para un whisky de turba, neón para una cabaña de montaña), redibuja la paleta una vez.
* Si el sujeto tiene colores inconfundibles propios (su material, la pintura de su calle, el paisaje de sus ventanas), mantén las proporciones repartidas y toma los colores del sujeto. Dilo en el informe.

---

## 💡 Regla clave

> [!important] Una dirección no es una idea
> La dirección decide **cómo** se compone. La idea decide **qué** hace que la página sea de este sujeto. Eso es el paso 4.

---

## 🔗 Relacionado

* [[02 Escribir el argumento]]
* [[04 Encontrar la idea]]
* [[06 Construir]]
* [[Casos Especiales]]
