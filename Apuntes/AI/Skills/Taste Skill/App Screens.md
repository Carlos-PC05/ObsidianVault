---
aliases:
  - Taste Skill App Screens
  - Pantallas de app
  - App screen rules
tags:
  - dev/ai
  - design-system
  - taste-skill
  - frontend
  - app-ui
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/references/app-screens.md
---
# App Screens — Taste Skill

> [!abstract] Resumen Ejecutivo
> Una **app screen** es una pantalla en la que se trabaja, no una página que se lee. Tiene su propio run, su propio draw (`draw.mjs --app`), sus propias house rules y su propio inspector (`inspect.mjs --app`). El run de página no aplica. Hay que leer `references/app-screens.md` entero antes de empezar.

---

## 🎯 Objetivo Principal

* **Distinguir página de app:** dashboard, admin, analytics, settings, billing, inbox, wallet, lista con filtros, editor o builder son app screens.
* **Aplicar reglas propias:** los bordes tranquilos, las etiquetas pequeñas y los paneles repetidos son normales en una app; los degradados, glows, iconos en cuadrados tintados, gráficos arcoíris y números de vanidad inventados no.
* **Construir fragmentos reales:** cuando una página de producto muestra su app, los fragmentos se construyen como piezas reales de esa app.

---

## 📋 Flujo de Trabajo

1. **Lee `references/app-screens.md` entero.** El run de página no se aplica.
2. **Reparte el estilo de app:**

```bash
node <skill>/scripts/draw.mjs --app --kind <kind> --brief "<the product>" \
  --keep palette,type --out .taste-skill/app
```

3. **Construye la pantalla** con `assets/app.css` para paneles, tablas y controles, y `assets/charts.js` para los gráficos.
4. **Usa datos reales que sumen.** Un hallazgo declarado, recortado y escalado como la página trata una imagen.
5. **Inspecciona con app:**

```bash
node <skill>/scripts/inspect.mjs <url> --app
```

---

## 🧩 Sitio de producto que muestra su app

Un sitio de producto que muestra su app es una **página**; los fragmentos de interfaz que aparecen en ella siguen las reglas de app screen para sus interiores.

* Reparte el estilo de app del producto una vez, en su propia carpeta, manteniendo los colores y la tipografía de la página.
* Construye cada fragmento que muestra la página (la pantalla viva del hero, el recorte de una feature, un gráfico) como una pieza real de esa app.
* Cuando una petición mezcla las dos cosas (una landing y el dashboard que hay detrás), construye cada una con su propio run y una paleta y tipografía compartidas.

---

## ⚖️ Qué es normal y qué no

| Normal en una app | Prohibido |
| :--- | :--- |
| Bordes tranquilos | Degradados |
| Etiquetas pequeñas | Glows |
| Paneles repetidos | Iconos en cuadrados tintados |
| Tablas y controles densos | Gráficos arcoíris |
| Datos reales que suman | Números de vanidad inventados |

---

## 💡 Regla clave

> [!important] Un fragmento que parece un producto real es la imagen más fuerte que tiene una web de software.
> Barras grises y números lorem son lo más débil. Si el fragmento no parece una app de verdad, no está terminado.

---

## 🔗 Relacionado

* [[Taste Skill]]
* [[06 Construir]]
* [[07 Gate]]
* [[Casos Especiales]]
