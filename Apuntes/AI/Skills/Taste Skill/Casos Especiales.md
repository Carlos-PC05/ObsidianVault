---
aliases:
  - Taste Skill Casos Especiales
  - Special cases
  - Rediseño
  - Marca existente
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - redesign
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# Casos Especiales — Taste Skill

> [!abstract] Resumen Ejecutivo
> No todos los trabajos son una página nueva. Esta nota cubre marca o design system existente, rediseño/restyle/review de una página o app, componentes o secciones sueltas, ausencia de checkout o backend de formularios, falta de Chrome/Node 22 y falta de red.

---

## 🎯 Objetivo Principal

* **No romper lo que funciona:** en un rediseño, conservar stack, contenido, rutas y comportamiento.
* **Adaptar la skill al contexto:** marca existente, componente suelto, restricciones de entorno.
* **Ser honesto con los límites:** si no se pudo inspeccionar o buscar imágenes, decirlo.

---

## 🏷️ Marca o design system existente

* Pasa `--keep palette,type`.
* Mapea los colores y las caras de la marca a los roles del sheet.
* Toma solo la composición del draw.
* Las house rules siguen aplicando.
* Si la marca exige un dispositivo prohibido (su logo va en un degradado, su accent es violeta), conserva el activo de marca, dilo en el informe y pasa `--allow <rule>` al inspector solo para esa regla.

---

## 🔁 Rediseño, restyle o review de una página o app existente

### 1. Registra el antes

* Corre el proyecto tal como está y inspecciónalo en su propia carpeta:

```bash
node <skill>/scripts/inspect.mjs <url> --out .taste-skill/before
```

* Con `--app` si es una app screen.
* Pasa `lint.mjs` al código fuente.
* Lee el código: framework, método de estilos (Tailwind v3 o v4, CSS modules, styled-components, CSS plano), componentes y lo que debe sobrevivir: rutas, formularios, datos, comportamiento, activos de marca.

### 2. Un review se detiene aquí

* Critica las capturas del antes con `references/critique.md`.
* Lista los hallazgos con las medidas del inspector.
* Nombra los tres cambios que más moverían la página.

### 3. Conserva el stack, el contenido y lo que funciona

* Sin migración de framework o librería de estilos.
* Revisa el archivo de dependencias antes de añadir nada.
* **Restyle:** conserva el layout y toma tipo, color y componentes de una dirección repartida (`--keep` lo que la marca fije).
* **Rediseño:** reparte una dirección completa y reconstruye las secciones desde sus blueprints dentro de los componentes existentes.
* En ambos casos, continúa desde el paso 2 del run con el contenido real de la página.

### 4. Orden de trabajo

De mayor efecto y menor riesgo a menor:

1. **Tipo:** caras y escala.
2. **Color:** un ground, un ink, un accent.
3. **Frame:** márgenes, ritmo de secciones, título en la primera pantalla y encabezado en cada sección.
4. **Estados:** hover, focus, loading, empty, error.
5. **Componentes stock:** tarjetas con icono, pill badges, blobs de degradado, card grids, sustituidos por los blueprints repartidos.

### 5. Demuéstralo

* Inspecciona otra vez en `.taste-skill/after`.
* Compara antes y después en cada ancho.
* Haz clic en cada enlace, formulario y menú que funcionaba antes.
* Reporta las medidas del antes y el después lado a lado.

> [!warning] No cambies en silencio
> La estructura de URLs, las etiquetas de navegación y los nombres de campos de formulario no cambian sin aprobación explícita.

---

## 🧩 Componente o sección suelta

* Sáltate el draw salvo que no haya dirección que seguir.
* Haz que encaje con el diseño que lo rodea.
* Aplica house rules y leyes.
* Inspecciona el resultado igualmente.

---

## 💳 Sin checkout ni backend de formularios

* El botón principal va al destino real cuando el brief lo nombra.
* Si no, abre un email con asunto y cuerpo escritos (`mailto:` con las elecciones del visitante rellenadas).
* Para una tienda, apunta a `#checkout` y el informe dice que hay que cablearlo antes de lanzar.
* **Nunca** finjas un formulario de pago.

---

## 🖥️ Sin Chrome o sin Node 22

* Corre `lint.mjs` (Node 18+).
* Toma capturas con la herramienta de navegador que tengas.
* Critica esas capturas.
* Di en el informe que el inspector no pudo correr.

---

## 🌐 Sin red

* Si `assets.mjs` no alcanza ninguna fuente, dibuja las imágenes.
* Di en el informe que las colecciones abiertas no se pudieron buscar.

---

## 🔗 Relacionado

* [[03 Sortear la dirección]]
* [[06 Construir]]
* [[07 Gate]]
* [[10 Informe]]
* [[App Screens]]
