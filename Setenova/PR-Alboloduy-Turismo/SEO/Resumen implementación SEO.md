---
tags:
  - SEO
  - Alboloduy
---
# Resumen didáctico: rama `feat/seo-retoques-frontend`

> **En una frase:** esta rama convierte la web de Turismo de Alboloduy —hasta ahora una SPA que los buscadores apenas veían— en un sitio **SEO-friendly y multidioma real**, con HTML prerenderizado por ruta e idioma, metadatos gestionados desde React y una política editorial explícita de qué se indexa y qué no.
>
> **Cifras:** 23 commits propios frente a `develop`, ~84 ficheros, ~6.500 líneas nuevas. Incluye ~20 tests nuevos y 923 líneas de documentación didáctica en `docs/SEO_IMPLEMENTACION.md`.

---

## 1. El problema de partida (por qué existe esta rama)

La web era una **SPA** (aplicación de una sola página) hecha con Vite + React: el servidor devolvía **el mismo HTML casi vacío** para cualquier URL, y todo el contenido se pintaba después con JavaScript en el navegador.

Consecuencia doble:

1. **SEO roto:** Google y redes sociales llegaban antes de que JavaScript pintara nada → veían un `<div id="root"></div>` vacío. Sin títulos por página, sin descripciones, sin datos estructurados.
2. **Multidioma "invisible":** existían traducciones (es/en/de/fr) pero **la URL no cambiaba**: `/restaurantes` se veía igual en los 4 idiomas. Un buscador no puede indexar 4 idiomas bajo 1 sola URL.

## 2. La solución en dos capas (la idea madre)

| Capa | Para quién | Qué hace |
|---|---|---|
| **Prerenderizado estático** | Rastreadores y primera visita | En el build, se genera un `index.html` real por cada ruta × idioma |
| **Componente `Seo` en runtime** | Navegación interna (SPA) | Cuando navegas dentro de la app, actualiza título, canonical, etc. sin recargar |

> 🎓 **Analogía del restaurante:** antes el camarero te traía una servilleta en blanco y cocinaba todo al momento (lento y el crítico gastronómico se iba). Ahora hay **cartas impresas ya en cada mesa** (HTML prerenderizado) y, si pides otro plato sin cambiar de mesa (navegación SPA), el camarero actualiza la pizarra del día al vuelo (componente `Seo`).

---

## 3. Las 6 ideas clave implementadas

### 3.1 URLs con prefijo de idioma (`localeRoutes.ts`)

- Español: **sin prefijo** (`/restaurantes`) — es el idioma canónico.
- Resto: `/en/restaurantes`, `/de/restaurantes`, `/fr/restaurantes`.
- Dos funciones puras son la fuente única de verdad: `analizarRuta(url)` → `{ idioma, ruta }` y `rutaConIdioma(ruta, idioma)` → URL final.
- Nuevo componente **`LocalizedLink`**: como `Link` de React Router, pero añade solo el prefijo de idioma actual. El Navbar cambió todos sus enlaces a este componente.

### 3.2 Bootstrap seguro de idiomas (`main.tsx` + ADR-0001) — *la joya técnica*

**El problema:** las traducciones de en/de/fr se cargan bajo demanda (lazy import). Si React arrancaba antes de llegar el idioma de la URL, pasaban dos cosas malas: el usuario veía un flash en español, y React intentaba **hidratar** HTML prerenderizado en inglés con un árbol en español → error de hidratación.

> 🎓 **Analogía de la hidratación:** hidratar es "tomar posesión" del HTML estático sin repintarlo, como un relevo donde el corredor nuevo coge el testón exactamente donde estaba. Si el testón es de otra carrera (otro idioma), el relevo se cae.

**La solución (decisión documentada en el ADR):**
1. La **URL es la fuente de verdad del idioma**.
2. `main.tsx` **espera** (`await cambiarIdioma(idiomaInicial)`) antes de decidir cómo arrancar.
3. Solo usa `hydrateRoot` si el HTML existente habla el mismo idioma (`<html lang>` coincide); si no, monta una raíz nueva con `createRoot` (repintado limpio, cero errores).
4. Si la descarga del idioma falla al abrir directo: **fallback controlado a español** interactivo, misma URL, y se marca `noindex,follow` para que Google nunca indexe esa versión degradada como si fuera la inglesa.

### 3.3 Cargador de idiomas awaitable y deduplicado (`src/i18n/i18n.js`)

Dos micro-patrones muy explicables:

- **Caché de promesas:** un `Map` guarda la promesa de carga por idioma. Si 3 componentes piden "de" a la vez, **comparten el mismo viaje**, no se descarga 3 veces.
  > 🎓 Como compartir taxi: quien llega luego al mismo destino se sube en el que ya va de camino.
- **Promesa rechazada sale de la caché:** si la descarga falló, se borra la entrada para que un reintento **vuelva a intentarlo de verdad** (no quede atrapado eternamente en un error cacheado).
- **Contador anti-carreras** (`ultimaSolicitudIdioma`): si pulsas EN→DE muy rápido, la respuesta vieja de EN llega tarde y **se descarta** porque su número ya no es el último.

### 3.4 `GuardIdioma` en `App.tsx` — transiciones SPA atómicas

Un único guardián envuelve toda la app (contenido + navbar + footer + SEO juntos):

- **Mientras carga el idioma:** muestra solo un estado neutro accesible (`role="status"`, `aria-busy`) → jamás ves media página en dos idiomas.
- **Si la navegación falla:** conserva la **última URL válida** y vuelve a ella con `navigate(..., { replace: true })` — nunca `navigate(-1)` (el botón atrás es sagrado, no se usa para recuperación). Muestra un aviso con botón **Reintentar**, que relanza el cargador común.
- El selector de banderas del Navbar ya no llama a una API de i18n: simplemente **navega a la misma ruta con otro prefijo**, y el guard reacciona. Coherencia total: la manda la URL.

### 3.5 Componente `Seo` (`seo.tsx`, 582 líneas) — el `<head>` gestionado desde React

Sin librerías externas (no react-helmet): un componente que devuelve `null` y solo toca el `<head>`:

- **Resuelve qué página es** (dedicada, provisional, ficha de detalle o 404) y aplica:
  - `title` limitado a **60 caracteres** y `description` a **155** (con corte limpio por palabra + `…`)
  - `meta robots` (`index,follow` / `noindex,follow`)
  - `link canonical` absoluto
  - **Open Graph + Twitter Cards** (cómo se ve al compartir en WhatsApp/X)
  - **`hreflang`** para las 4 variantes + `x-default`
  - **JSON-LD** (datos estructurados schema.org)
- Todo nodo que crea lleva `data-seo-managed="true"` → al desmontar se limpia (sin basura acumulada entre páginas).
- El JSON-LD serializa escapando `<`, `>`, `&` para evitar inyección.

**Qué le dice el JSON-LD a Google según la página** (esto impresiona en una explicación):
| Página | Schema |
|---|---|
| Portada | `TouristDestination` con coordenadas geográficas y atracciones incluidas |
| Ficha restaurante | `Restaurant` con dirección, teléfono y GeoCoordinates sacadas del campo mapa |
| Ficha alojamiento | `LodgingBusiness` |
| Cualquier atracción | `TouristAttraction` |
| Fiestas | `Event` con `eventSchedule` **recurrente anual** (`byMonth`, `byMonthDay`) — así no hay que actualizar fechas cada año |
| Todas | `WebSite` + `Organization` + `BreadcrumbList` |

### 3.6 Gobierno editorial: `publicado` vs `provisional`

La decisión de qué entra en el sitemap ya **no depende de adivinar frases o imágenes**, es un dato explícito:

- Tipo `EstadoContenido = "publicado" | "provisional"` en `contentStatus.ts`.
- Cada ruta del menú declara su estado en `ESTADOS_RUTAS_NAV` y cada ficha en los datos (`estadoContenido`).
- Ejemplo real: **la Sala Minera sigue `provisional`** porque tiene imagen genérica → decisión editorial consciente.

De ahí salen **dos catálogos derivados de la misma fuente**:
```
Catálogo público  = TODAS las rutas × 4 idiomas  → se prerenderizan
Catálogo indexable= solo las "publicado"          → van al sitemap.xml
```
Las provisionales existen, son visitables y prerenderizadas, pero llevan `noindex,follow`, **sin canonical ni alternates** (no se presenta degradación como si fuera versión oficial).

---

## 4. El pipeline de build (cómo se fabrica todo esto)

```bash
bun run build
# equivale a:
bun run seo:generate   # 1. Genera sitemap.xml + robots.txt en public/
tsc -b                 # 2. Type-check
vite build             # 3. Build normal (un plugin propio sustituye los
                       #    placeholders __SEO_SITE_URL__/__SEO_IMAGE_URL__
                       #    del index.html por el dominio real)
bun run seo:prerender  # 4. Playwright recorre todas las rutas y escribe
                       #    dist/<ruta>/index.html + dist/404.html
```

Detalles finos del prerenderizador (`scripts/prerender.ts`) que demuestran rigor:
- Levanta un mini-servidor HTTP local que sirve `dist/` y abre **Chromium headless** (Playwright).
- **No usa heurísticas** ("¿ya hay texto?"): espera señales explícitas — atributos `data-aplicacion-montada` y `data-idioma-listo` que pone la propia app, ausencia del spinner del guard, título y canonical presentes, y `lang` correcto. Solo entonces captura el DOM.
- Normaliza las URLs absolutas del servidor temporal para que los assets queden con rutas relativas.
- El Dockerfile instala Chromium en la imagen para poder prerenderizar durante el `docker build`.

### Y en producción, ¿quién sirve eso?

**Nginx** cambió una línea filosóficamente importante:
```nginx
# Antes: try_files $uri $uri/ /index.html;   ← TODO era la SPA
# Ahora:
try_files $uri/index.html $uri =404;
```
Ahora cada URL prerenderizada se sirve de disco (rapidísimo), y lo que no existe es un **404 HTTP auténtico** (con su `404.html`), no una página falsa. Los rastreadores lo agradecen.

---

## 5. Galería de fotos: accesibilidad localizada

`GaleriaFotos.tsx` se rehízo para cumplir el mismo estándar en los 4 idiomas:
- Apertura por teclado, **atrapamiento de foco** dentro del visor, cierre con `Escape`, flechas para navegar fotos, clic en fondo para cerrar.
- Al cerrar, el foco **vuelve al botón que la abrió** y se restaura el `overflow` original del body.
- Etiquetas e instrucciones traducidas; tipografía Figtree para los textos de apoyo (Lora queda reservada a titulares); animaciones respetan `prefers-reduced-motion`.

---

## 6. Pruebas (cómo se demostró que funciona)

- **Unitarias (Vitest):** el cargador i18n (concurrencia, fallo, reintento), el guard (estado neutro, rollback), el componente `Seo` (metadatos por tipo de página, reciprocidad de alternates), catálogos (público > indexable, sin duplicados, provisionales excluidos), sitemap y prerenderizado.
- **E2E (Playwright contra la imagen Docker/Nginx):** carga directa de `/en|de|fr/restaurantes` verificando HTTP 200, contenido localizado, `html[lang]`, canonical y ausencia de errores de consola/hidratación; y que una provisional responde `noindex,follow` sin canonical.

---

## 7. Chuleta para explicar en 60 segundos

> "La web era una SPA: los buscadores veían un HTML vacío. Esta rama hace dos cosas. Primero, **prerenderiza**: en el build, un Chromium visita cada ruta en sus 4 idiomas y guarda el HTML ya pintado; Nginx lo sirve directamente y lo que no existe da un 404 real. Segundo, **multidioma con URL propia**: `/en/restaurantes`, etc.; el español queda sin prefijo como canónico. Lo delicado era arrancar React sin romper ese HTML: ahora espera a tener el idioma cargado y solo 'hidrata' si el idioma coincide, con fallback a español marcado `noindex` si la descarga falla. Cada página además escribe sus metadatos (título, descripción, Open Graph, hreflang y JSON-LD de schema.org: Restaurant, Event, TouristDestination…). Y hay un gobierno editorial: solo lo declarado 'publicado' entra en el sitemap; lo 'provisional' se ve pero lleva `noindex,follow`."

## 8. Preguntas trampa y respuestas rápidas

- **¿Por qué hidratación condicional y no siempre `createRoot`?** Porque hidratar conserva el HTML estático (más rápido, sin parpadeo); montar de cero pierde esa ventaja. Se monta de cero solo cuando el markup no es compatible.
- **¿Por qué no cargar los 4 idiomas en el bundle inicial?** Eliminaría la carrera, pero metería ~120 KiB de JS que la mayoría no usa. Se prefiere lazy + espera explícita.
- **¿Qué pasa si falla la descarga de un idioma?** Primera carga: fallback español + `noindex,follow` (Google no indexa una versión degradada). Navegación interna: vuelves a la última URL válida con aviso y reintento; el reintento funciona porque las promesas rechazadas no se cachean.
- **¿Por qué el sitemap no incluye todas las páginas?** Porque el sitemap es una recomendación a buscadores; solo se recomienda lo publicado. Las provisionales siguen descubribles por enlaces, pero con `noindex,follow`.
- **¿Dónde se documenta todo?** `docs/SEO_IMPLEMENTACION.md` (manual de 19 secciones, con mantenimiento futuro), `docs/adr/0001-bootstrap-idiomas-localizados.md` (la decisión de arquitectura) y `docs/specs/SPEC_CIERRE_PR_SEO_DEVELOP.md` (el porqué de cada cierre).
