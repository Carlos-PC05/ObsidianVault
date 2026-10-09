---
tags:
  - SEO
  - Alboloduy
---
## 1. Propósito de este documento

Este documento explica, con detalle, la implementación SEO realizada en la rama `feat/seo-retoques-frontend`. No es una lista superficial de cambios ni un resumen de commits: pretende dejar por escrito el razonamiento que hay detrás de cada decisión, la relación entre las piezas del sistema, la forma correcta de mantenerlas y los puntos que todavía habrá que resolver antes de considerar el SEO completamente maduro.

La idea es que una persona que conozca el proyecto, pero que no haya participado en esta rama, pueda responder por sí misma a preguntas como estas:

- ¿Qué problema SEO existía en la aplicación?
- ¿Qué puede leer un buscador antes de ejecutar JavaScript y qué aparece después?
- ¿Cómo se decide el `title`, la descripción, el canonical y el JSON-LD de cada URL?
- ¿De dónde salen las rutas del sitemap?
- ¿Por qué hay valores SEO en los cuatro ficheros de idioma?
- ¿Qué diferencia hay entre el SEO dinámico de React y el HTML inicial del servidor?
- ¿Qué variables hay que fijar en producción?
- ¿Qué hay que tocar cuando se añade una nueva página, un nuevo restaurante o un nuevo idioma?
- ¿Qué parte está resuelta y qué parte sigue necesitando prerenderizado o URLs multidioma?

La documentación y los comentarios del proyecto deben mantenerse en español de España. Por eso, las explicaciones, nombres conceptuales y ejemplos de este documento siguen ese criterio, aunque algunos nombres técnicos (`canonical`, `Open Graph`, `JSON-LD`, `hreflang`, etc.) se mantengan en su forma habitual.

## 2. Alcance exacto de la rama

El alcance descrito aquí corresponde a la serie SEO que parte del commit `156fc3a` y llega hasta `HEAD` en el momento de redactar este documento. Los commits específicos son:

| Commit | Propósito |
| --- | --- |
| `abf1155` | Añadir la base SEO para despliegue. |
| `fd82dcb` | Completar los datos estructurados. |
| `067b7fd` | Afinar metadatos, límites y eventos. |
| `1fd9776` | Sustituir la imagen social SVG por una imagen PNG rasterizada. |
| `698581b` | Integrar placeholders SEO en el build de Vite y limitar también los títulos. |
| `3a586e5` | Asegurar que el generador de sitemap lea el mismo entorno que Vite. |

El objetivo de esta rama era resolver el bloque P0 del informe [`InformeSEOAlboloduy.md`](./InformeSEOAlboloduy.md): dejar de tener una SPA completamente muda para los metadatos, disponer de robots y sitemap reales, añadir señales sociales y datos estructurados, y distinguir rutas desconocidas.

Hay que entender bien la frase «resolver el bloque P0». La implementación mejora de forma importante una SPA renderizada en cliente, pero no convierte todavía la aplicación en una aplicación SSR o SSG. Por tanto, no debe confundirse «React actualiza correctamente el `<head>` cuando se ejecuta» con «cada URL entrega desde el servidor un HTML completo y específico antes de ejecutar JavaScript». Esa diferencia es una de las ideas más importantes de todo el documento.

## 3. El problema original: una SPA no es automáticamente una página SEO

### 3.1 Qué entregaba el servidor antes

La aplicación es una SPA de Vite + React. Nginx sirve un `index.html` común y, gracias a:

```nginx
location / {
    try_files $uri $uri/ /index.html;
}
```

las rutas de React funcionan aunque no exista un fichero físico para cada una. Si se solicita `/historia`, `/restaurantes` o `/rutas-turisticas/sendero-cordel`, Nginx termina devolviendo el mismo `index.html`.

Ese comportamiento es correcto para el enrutamiento de una SPA, pero tiene una consecuencia SEO: antes de que el navegador cargue el JavaScript y React resuelva la ruta, todas las URLs son esencialmente el mismo documento HTML. El servidor no sabe que `/historia` representa historia y que `/restaurantes` representa gastronomía.

Los problemas que motivaron la rama eran:

1. Todas las URLs compartían el mismo título y la misma descripción.
2. No había canonical por página.
3. No había Open Graph ni Twitter Cards.
4. No había datos estructurados `schema.org`.
5. `robots.txt` y `sitemap.xml` no eran archivos reales.
6. Una URL inexistente podía terminar mostrando el shell de la SPA con respuesta HTTP 200.
7. El idioma se guardaba en `localStorage`, pero no existían URLs diferenciadas ni `hreflang`.

Google puede ejecutar JavaScript, pero no todos los rastreadores lo hacen y, cuando lo hace, el contenido puede pasar por una fase posterior de renderizado. Las redes sociales suelen leer directamente las etiquetas del HTML inicial para construir la tarjeta de enlace. Los rastreadores de otros buscadores, herramientas de auditoría y algunos sistemas de IA tampoco tienen por qué ejecutar el JavaScript de la aplicación.

La implementación de esta rama ataca todos los problemas que se pueden resolver sin cambiar el modelo de despliegue estático. Los dos problemas estructurales que requieren una fase posterior —prerenderizado y URLs por idioma— quedan documentados como trabajo futuro.

### 3.2 La solución tiene dos capas

La solución se divide deliberadamente en dos capas:

#### Capa estática

Es lo que ya existe en `frontend/index.html` antes de ejecutar React. Incluye valores válidos para la página de inicio y placeholders que Vite sustituye durante el build. Esta capa es un fallback útil: si el navegador no ejecuta JavaScript, al menos la URL recibe metadatos coherentes de la portada.

#### Capa dinámica

Es el componente `frontend/src/seo.tsx`, montado por `App.tsx`. Cuando React arranca o cambia la ruta, este componente calcula los datos SEO correctos y actualiza el `<head>`: título, descripción, robots, canonical, Open Graph, Twitter y JSON-LD.

La capa dinámica es la que permite que una navegación SPA actualice correctamente la URL actual. La capa estática evita que el HTML inicial esté vacío y, además, sirve como base que Vite puede personalizar por dominio durante el build.

Esta duplicación no es accidental. Mantener una copia estática y otra dinámica tiene un coste de mantenimiento, pero es el coste de trabajar con la arquitectura actual mientras no se introduzca prerenderizado. Cuando exista SSG o SSR, la capa estática podrá generarse desde la misma fuente de datos que la capa dinámica, reduciendo esa duplicación.

## 4. Arquitectura general de la solución

El flujo completo es el siguiente:

```text
Datos de rutas + datos de Descubre + traducciones
                       │
                       ├── Seo.tsx, durante la navegación
                       │       └── actualiza el <head> en el navegador
                       │
                       └── generate-seo.ts, durante el build
                               └── genera robots.txt y sitemap.xml

index.html ── placeholders ──► seoHeadPlugin de Vite ──► dist/index.html

VITE_SITE_URL + VITE_SEO_IMAGE_URL
                       │
                       ├── Vite: canonical, OG y JSON-LD iniciales
                       ├── Seo.tsx: URLs absolutas durante la ejecución
                       └── generate-seo.ts: sitemap y robots
```

La intención es que no haya un dominio escrito a mano en una parte del SEO y otro dominio en otra. La URL canónica y la URL de la imagen social deben formar un conjunto coherente con el dominio público real.

## 5. El componente `Seo`: el centro del SEO en React

El fichero [`frontend/src/seo.tsx`](../frontend/src/seo.tsx) concentra la lógica SEO de la SPA. Es un componente sin salida visual: devuelve `null`, pero tiene un efecto importante sobre `document.head`.

Se monta al principio de `App.tsx`:

```tsx
return (
  <>
    <Seo />
    <ScrollToTop />
    ...
  </>
);
```

Colocarlo en `App`, fuera de las rutas concretas, es importante porque así todas las rutas pasan por la misma política. No hay que acordarse de montar SEO en cada página individual. La ruta cambia, `useLocation()` informa del cambio y el componente recalcula el documento.

### 5.1 La URL base y las URLs absolutas

El componente parte de una URL base:

```ts
const SITE_URL = (
  import.meta.env.VITE_SITE_URL || "https://alboloduy-turismo.setenova.es"
).replace(/\/+$/, "");
```

Se eliminan las barras finales para que la base sea estable. Después, `absoluteUrl()` utiliza `new URL()` para convertir rutas como `/restaurantes` o `/og-image.png` en URLs completas.

Esto importa porque `canonical`, `og:url`, `og:image`, `twitter:image`, `WebSite.url`, `Organization.logo` y los elementos del sitemap deben poder interpretarse fuera del contexto del navegador. Una ruta relativa puede funcionar en HTML, pero no es la forma más segura para metadatos que consumirán rastreadores o plataformas externas.

La variable de entorno tiene prioridad sobre el dominio predeterminado. El predeterminado permite que el proyecto funcione sin configuración local y refleja el dominio provisional actual. Cuando se active el dominio institucional, se debe modificar `VITE_SITE_URL` en el entorno de build; no se debe cambiar únicamente una constante de React.

### 5.2 Catálogo de rutas SEO

El componente no intenta adivinar el significado de una ruta leyendo texto libre. Mantiene mapas explícitos:

- `PAGINAS_DEDICADAS`: asocia rutas conocidas con tipos SEO como `home`, `historia`, `fiestas`, `restaurantes` o `movilidad`.
- `RUTAS_DETALLE_DESDE_NAVEGACION`: identifica ciertas rutas de `explora` que son fichas de detalle, aunque procedan de la navegación.
- `RUTAS_PENDIENTES`: genera automáticamente rutas provisionales a partir de `NAV_ROUTES`, excluyendo las que tienen página dedicada.
- `SECCION_I18N`: traduce el nombre de la sección de la URL al nombre de la rama de traducciones.
- `TIPO_DETALLE`: decide si una ficha corresponde a restaurante, alojamiento o atracción.
- `TIPO_SCHEMA`: convierte ese tipo interno en el tipo de Schema.org.

La fuente de verdad de las rutas de navegación sigue siendo `frontend/src/navRoutes.ts`. Esto evita que el menú y el SEO tengan listas de rutas completamente distintas. Sin embargo, que una ruta aparezca en `NAV_ROUTES` no significa automáticamente que tenga contenido editorial completo: algunas siguen siendo `PlaceholderPage` y por eso reciben metadatos provisionales.

### 5.3 Normalización de la barra final

`normalizarRuta()` conserva `/` como portada y elimina las barras finales en el resto de las rutas:

```ts
function normalizarRuta(pathname: string): string {
  if (pathname === "/") return pathname;
  return pathname.replace(/\/+$/, "");
}
```

El objetivo es que `/restaurantes` y `/restaurantes/` no compitan como dos URLs canónicas diferentes. La página puede seguir siendo accesible con barra final, pero el canonical apunta a la forma normalizada sin barra.

Esta normalización se aplica antes de resolver la página y antes de construir el canonical. No se debe eliminar la excepción de `/`: la portada debe conservar exactamente la raíz del dominio.

### 5.4 Resolución de una página

`resolverPagina()` convierte una ruta del navegador en un objeto interno `SeoPage` con:

- tipo de página;
- título;
- descripción;
- indicación de `noindex`;
- ruta canónica;
- y, si procede, elemento de datos, sección y slug.

La resolución sigue este orden:

#### A. Página dedicada

Si la ruta está en `PAGINAS_DEDICADAS`, se usa la traducción `seo.<pagina>.titulo` y `seo.<pagina>.descripcion`. Aquí entran las páginas con contenido propio: portada, cómo llegar, historia, fiestas, patrimonio, restaurantes, alojamiento, visitas, rutas, espacios naturales y movilidad.

#### B. Ruta provisional

Si la ruta pertenece a `RUTAS_PENDIENTES`, se obtiene el nombre de navegación con i18n y se insertan los valores en:

```text
seo.provisional.titulo
seo.provisional.descripcion
```

Esto es preferible a dejar títulos vacíos o repetir ciegamente el título de la portada. La página se reconoce como existente e indexable, pero deja claro que el contenido está en preparación. Si en el futuro esa página recibe contenido real, hay que sustituir este comportamiento por una entrada SEO específica o por una resolución de detalle más rica.

#### C. Ficha de `Descubre`

Para una ruta de dos segmentos, por ejemplo `/restaurantes/bar-aca-la-mari`, se divide el path en sección y slug. `findItem()` busca el elemento en los datos de `descubre.ts`.

La descripción se construye con el resumen traducido de i18n y el título usa el nombre estructural del elemento. La ficha queda asociada a una sección, un slug y un tipo Schema.org. Esa información se reutiliza después para Open Graph y JSON-LD.

#### D. Ruta desconocida

Si ninguna de las anteriores coincide, se devuelve la página `noEncontrado`, con:

```text
title: Página no encontrada | Turismo de Alboloduy
robots: noindex,follow
```

El `follow` permite que los rastreadores sigan enlaces que aparezcan en la página, pero `noindex` evita pedirles que incorporen esa URL inválida al índice.

Hay una limitación importante: esta decisión sucede después de cargar React. El servidor sigue entregando inicialmente el shell de la SPA con `index,follow`. La solución completa para devolver una respuesta HTTP 404 y un HTML de error desde el servidor requiere prerenderizado o una capa de servidor que conozca las rutas válidas.

### 5.5 Límites de títulos y descripciones

Las traducciones contienen los textos SEO, pero el componente aplica límites de seguridad:

- `limitarTitulo()` mantiene el título en un máximo de 60 caracteres.
- `limitarDescripcion()` mantiene la descripción en un máximo de 155 caracteres.

El recorte intenta cortar por un espacio y añade una elipsis. Esto evita que una traducción larga o un nombre de ficha excepcional haga crecer indefinidamente el fragmento que puede mostrar un buscador.

Estos límites no sustituyen una revisión editorial. Un texto truncado técnicamente puede seguir siendo malo desde el punto de vista de la intención de búsqueda. Cada nueva traducción debe escribirse procurando que la parte más importante aparezca al principio y que la frase siga teniendo sentido si no llega a recortarse.

Los títulos SEO viven en cuatro ficheros:

- `frontend/src/i18n/locales/es.js`
- `frontend/src/i18n/locales/en.js`
- `frontend/src/i18n/locales/de.js`
- `frontend/src/i18n/locales/fr.js`

Cada uno contiene las mismas ramas lógicas: páginas dedicadas, provisional, detalle y no encontrado. También contiene `seo.tiposTurismo`, usado por el JSON-LD de la portada.

### 5.6 Gestión de metadatos del `<head>`

El efecto de `Seo` observa dos dependencias principales:

- `pathname`, para reaccionar al cambio de ruta;
- `idioma`, para reaccionar al cambio de idioma.

Cuando se ejecuta:

1. Resuelve la página.
2. Calcula el canonical absoluto.
3. Traduce el idioma de i18n a locale Open Graph (`es_ES`, `en_US`, `de_DE`, `fr_FR`).
4. Calcula la URL absoluta de la imagen social.
5. Actualiza `document.title`.
6. Actualiza `html[lang]`.
7. Elimina los `og:locale:alternate` anteriores.
8. Crea o reutiliza las etiquetas meta gestionadas.
9. Crea o reutiliza el enlace canonical.
10. Crea o reutiliza el script JSON-LD.
11. Registra los elementos para retirarlos en la limpieza del efecto.

`crearMeta()` busca una meta existente por `name` o `property`. Esto permite reutilizar las etiquetas iniciales de `index.html` en lugar de multiplicarlas cada vez que React navega. En el caso de `og:locale:alternate`, también compara el contenido porque puede haber varias etiquetas con la misma propiedad.

La limpieza del efecto elimina los elementos que el componente gestionó antes del siguiente cálculo. Así no se acumulan títulos, canonicals o scripts JSON-LD obsoletos durante una navegación larga.

## 6. Metadatos implementados

### 6.1 Título

El título del documento se actualiza con el valor SEO de la página actual. Ejemplos en español:

```text
Turismo en Alboloduy (Almería) | Qué ver y hacer
Dónde comer en Alboloduy | Restaurantes y gastronomía
Rutas y senderos | Senderismo en la Alpujarra almeriense
Página no encontrada | Turismo de Alboloduy
```

El separador `|` permite combinar intención de búsqueda, localidad y contexto. No se ha añadido una marca repetida de forma artificial a todos los títulos: el propio nombre de Turismo de Alboloduy aparece donde aporta claridad.

### 6.2 Meta description

La descripción resume la utilidad concreta de la página y utiliza términos geográficos y temáticos relevantes: Alboloduy, Almería, Alpujarra, Sierra Nevada, Tabernas, restaurantes, senderismo, patrimonio o movilidad, según corresponda.

La descripción no es una orden para que Google posicione una palabra concreta, pero sí ayuda a explicar el resultado y puede mejorar el porcentaje de clics cuando el buscador la utiliza como fragmento.

### 6.3 Robots

Las páginas válidas usan:

```html
<meta name="robots" content="index,follow">
```

Las rutas desconocidas usan:

```html
<meta name="robots" content="noindex,follow">
```

No se utiliza `noindex` para las páginas provisionales: son rutas reales del menú y su contenido puede crecer. Si una ruta se convierte en una página vacía, duplicada o sin valor de búsqueda, habrá que tomar una decisión editorial explícita: mejorarla, retirarla del sitemap, redirigirla o marcarla como `noindex`.

### 6.4 Canonical

El canonical se escribe como URL absoluta y con la barra final normalizada. Su función es declarar cuál es la versión preferida cuando una misma página puede alcanzarse mediante variantes de URL.

El canonical no redirige al usuario ni impide que la URL alternativa exista. Es una señal para buscadores. Por eso sigue siendo conveniente que Nginx o el router apliquen redirecciones cuando haya variantes que no deban conservarse, pero el canonical proporciona una defensa adicional.

### 6.5 Open Graph

Se han incluido:

- `og:title`;
- `og:description`;
- `og:type`, con `website` para páginas generales y `article` para fichas;
- `og:url`;
- `og:image`;
- `og:image:width` = `1200`;
- `og:image:height` = `630`;
- `og:image:alt`;
- `og:site_name`;
- `og:locale`;
- `og:locale:alternate` para el resto de idiomas.

Estas etiquetas las utilizan, entre otros, clientes de mensajería y redes sociales para construir la vista previa de un enlace. En un destino turístico esto es especialmente importante: muchas visitas llegan por enlaces compartidos por WhatsApp, redes sociales o grupos de viajes.

`og:type` se decide según si la página corresponde a una ficha (`article`) o a una página general (`website`). No significa que una ficha turística sea literalmente una noticia; es una clasificación práctica para las plataformas que consumen Open Graph.

### 6.6 Twitter Cards

Se ha configurado:

```html
<meta name="twitter:card" content="summary_large_image">
```

junto con título, descripción e imagen. Aunque el nombre histórico sea Twitter, la etiqueta sigue siendo entendida por clientes compatibles con Twitter Cards y completa la cobertura de previsualización social.

## 7. Imagen social: SVG fuente y PNG público

La rama incluye dos recursos:

- `frontend/public/og-image.svg`, que sirve como fuente editable y representa la composición gráfica;
- `frontend/public/og-image.png`, que es la imagen configurada para compartir.

El PNG mide `1200 × 630`, que es una proporción habitual para tarjetas sociales. La composición contiene el nombre de Alboloduy, la referencia a la Alpujarra de Almería y una ilustración de paisaje en los colores de la marca.

Se utiliza PNG como imagen final porque las plataformas sociales no tienen una compatibilidad uniforme con SVG para tarjetas Open Graph. Mantener el SVG es útil para futuras modificaciones de diseño, pero no se debe cambiar automáticamente la extensión de la URL configurada sin revisar las plataformas de destino.

La imagen puede configurarse mediante `VITE_SEO_IMAGE_URL`. Si el valor es relativo, por ejemplo `/og-image.png`, el código lo convierte en una URL absoluta usando `VITE_SITE_URL`. También se admite una URL absoluta si la imagen termina sirviéndose desde un CDN o desde otro dominio.

En producción hay que verificar tres cosas:

1. Que la URL de la imagen responda con HTTP 200.
2. Que el `Content-Type` sea el apropiado para PNG.
3. Que el archivo sea accesible para los rastreadores y no quede bloqueado por autenticación, robots o CSP del proveedor.

### 7.1 Favicon e identidad visual

El favicon activo es el escudo de Alboloduy, servido desde `Escudo_de_Alboloduy.svg.webp`. Se utiliza tanto en `rel="icon"` como en `apple-touch-icon`, y también como `Organization.logo` en el JSON-LD.

La referencia anterior a `/favicon.svg` no respondía a una decisión de marca: era el favicon que venía con el scaffold inicial de Vite y se había conservado por inercia. No había una razón SEO para preferirlo. De hecho, mantener el favicon de Vite mientras se usaba el escudo como icono de Apple producía una identidad inconsistente según el navegador o la superficie.

El archivo `frontend/public/favicon.svg` antiguo puede quedar como recurso sin referencias mientras se decide si se elimina en una limpieza posterior. Lo importante es que ninguna etiqueta activa ni el logo estructurado vuelva a apuntar a él por accidente.

Este favicon no sustituye a `og-image.png`: el escudo es la identidad pequeña de la pestaña y del dispositivo, mientras que la imagen Open Graph es una composición horizontal de 1200 × 630 para tarjetas de enlaces.

## 8. Datos estructurados JSON-LD

### 8.1 Por qué existe JSON-LD

El HTML visible sirve para las personas. JSON-LD ofrece una descripción semántica explícita para buscadores: indica que una página representa un destino turístico, un restaurante, un alojamiento, una atracción o un evento.

El componente genera un único script:

```html
<script type="application/ld+json">...</script>
```

con un `@graph`. Un grafo permite colocar en el mismo documento la identidad general del sitio, la organización responsable, el recurso de la página y, cuando procede, las migas o los eventos.

### 8.2 `WebSite` y `Organization`

Todas las páginas incluyen:

- `WebSite`, con nombre, URL, idioma y referencia al publicador;
- `Organization`, con el nombre del Ayuntamiento de Alboloduy, URL institucional y logo.

Los identificadores `@id` se construyen desde la raíz canónica:

```text
https://dominio.example/#website
https://dominio.example/#organizacion
```

Esto permite que el mismo sitio y la misma organización se reconozcan como entidades compartidas entre páginas distintas, en lugar de parecer objetos independientes creados de nuevo en cada URL.

### 8.3 `TouristDestination` en la portada

La portada publica un `TouristDestination` con:

- nombre `Alboloduy`;
- descripción SEO;
- URL canónica;
- idioma activo;
- tipos de turismo;
- coordenadas del municipio;
- `includesAttraction` con los espacios naturales y elementos de `EXPLORA`.

Los tipos de turismo se han movido a las traducciones mediante `seo.tiposTurismo`, de forma que el valor sea coherente con el idioma activo.

Los elementos incluidos se enlazan con sus URLs de ficha. Esto crea una relación semántica entre el destino y sus recursos turísticos, y además obliga a que los slugs y las rutas que generan esos enlaces sigan siendo válidos.

Las coordenadas actuales son datos del municipio, no la posición de cada recurso. No se deben reutilizar como si fueran la ubicación exacta de todos los restaurantes, rutas o atracciones.

### 8.4 Fichas de restaurantes

Las fichas de la sección `restaurantes` se publican como `Restaurant`. Incluyen:

- nombre;
- descripción traducida;
- URL;
- imagen;
- idioma;
- dirección postal;
- localidad Alboloduy;
- provincia Almería;
- código postal;
- país ES;
- teléfono cuando existe;
- coordenadas cuando `mapa` contiene una pareja numérica.

Los teléfonos que contienen el texto de placeholder `No hay teléfono` no se publican como `telephone`. Es importante no presentar como dato estructurado un texto que no es un número.

### 8.5 Fichas de alojamientos

Las fichas de `alojamiento` se publican como `LodgingBusiness`. La estructura de dirección y el tratamiento del teléfono siguen el mismo principio que en restaurantes.

El tipo no pretende describir con precisión comercial cada modalidad de alojamiento posible. Si en el futuro se dispone de información más detallada —por ejemplo, número de habitaciones, precio, URL de reserva u horarios de recepción— habrá que comprobar primero qué propiedades admite Schema.org y cuáles están realmente verificadas por el negocio.

### 8.6 Otras atracciones

Las secciones de visitas, rutas, espacios naturales y explora se publican como `TouristAttraction`. El código reutiliza el mismo tipo para no inventar categorías más concretas que los datos actuales no puedan sostener.

La URL de la imagen usa el campo `foto` del elemento. La localización se añade solo si `mapa` tiene el formato numérico:

```text
37.032,-2.621
```

Cuando `mapa` contiene una dirección textual como `Alboloduy, Almería`, la ficha sigue teniendo ubicación visible para las personas, pero no se transforma automáticamente en coordenadas. No se debe geocodificar de forma silenciosa dentro del build sin una fuente de datos estable y revisada.

### 8.7 Eventos de fiestas

La página de fiestas publica un array de objetos `Event`. La tabla `EVENTOS_FIESTAS` relaciona el índice de la cronología con:

- meses de celebración;
- días concretos cuando se conocen;
- o fechas fijas de inicio y fin.

Para eventos recurrentes se utiliza `eventSchedule` con frecuencia anual y `byMonth` o `byMonthDay`. Para las fiestas patronales se construyen `startDate` y `endDate` con el año UTC actual.

Esta diferencia evita inventar una fecha concreta para eventos cuya fecha exacta puede cambiar. También deja una responsabilidad futura: revisar cada año la información de las fiestas y corregir la tabla si cambia el calendario real.

Los eventos reutilizan las traducciones de `fiestas.cronologia.<indice>`. Si se reordena la cronología en el fichero de idioma, hay que actualizar también los índices de `EVENTOS_FIESTAS`; el índice es el vínculo actual entre texto y estructura.

### 8.8 `BreadcrumbList`

Todas las páginas internas incluyen una miga de pan con:

1. portada;
2. página actual.

La portada no incluye breadcrumb porque sería redundante. En una ficha de restaurante, por ejemplo, la miga actual se basa en el título SEO de la ficha.

En el futuro se puede ampliar a una jerarquía de tres niveles —portada, sección, ficha— si esa jerarquía existe de forma visible y estable en la interfaz. No conviene publicar una miga más detallada en JSON-LD si el usuario no puede encontrar una estructura equivalente en la página.

### 8.9 Serialización segura

`serializarJsonLd()` escapa `<`, `>` y `&` antes de insertar el JSON en un script HTML. La razón es evitar que un texto proveniente de contenidos o traducciones pueda cerrar accidentalmente el bloque de script o alterar el documento.

El JSON-LD se construye con `JSON.stringify()` y luego se aplican esos escapes. Si en el futuro se incorporan campos procedentes de un CMS, esta protección debe conservarse y debe evitarse insertar HTML sin limpiar dentro de los datos estructurados.

## 9. Traducciones SEO e idiomas

La aplicación ya usa i18next y react-i18next. La rama añade a cada locale una rama `seo` completa.

La ventaja de usar i18n para SEO es que el título, la descripción, los textos de eventos y los tipos de turismo se mantienen alineados con el idioma visible. Cuando el usuario cambia de idioma, el componente actualiza `document.documentElement.lang`, `og:locale`, el título, la descripción y los textos JSON-LD que proceden de traducción.

La implementación actual no crea URLs multidioma. La misma ruta `/historia` puede mostrar español, inglés, alemán o francés según el estado de idioma guardado en el cliente. Eso significa que:

- no existe una URL estable para cada versión lingüística;
- no se han añadido enlaces `hreflang`;
- el sitemap solo representa una versión de cada ruta;
- el canonical no puede distinguir versiones lingüísticas con distintas URLs.

Esto es una limitación conocida del proyecto, no un olvido accidental. Resolverla exige cambiar el modelo de routing: por ejemplo, `/es/historia`, `/en/history`, `/de/geschichte` y `/fr/histoire`, o una estrategia equivalente. También habría que hacer que el selector de idioma sea navegación real entre URLs y generar alternates recíprocos.

Mientras eso no se haga, hay que ser prudente con las afirmaciones de SEO internacional. Tener traducciones dentro de i18n mejora la experiencia de usuario, pero no equivale a tener cuatro superficies indexables independientes.

## 10. HTML inicial y placeholders de Vite

### 10.1 Por qué `index.html` tiene valores de portada

`frontend/index.html` mantiene un título, descripción, imagen social y JSON-LD válidos para la portada. Esto cumple dos funciones:

1. Proporciona una página inicial razonable incluso antes de que React ejecute `Seo`.
2. Sirve de fallback para agentes que no ejecuten JavaScript.

No se ha intentado convertir `index.html` en una representación manual de todas las rutas, porque el servidor sigue sirviendo el mismo archivo para la SPA.

### 10.2 Placeholders de dominio

Para evitar fijar el dominio en varias líneas del HTML, se usan:

```text
__SEO_SITE_URL__
__SEO_IMAGE_URL__
```

El plugin `seoHeadPlugin()` de `frontend/vite.config.ts` los sustituye durante `transformIndexHtml`.

El resultado de `dist/index.html` no debe contener ninguno de esos textos. Si aparecen en producción, significa que se está sirviendo el fichero fuente o que el build no pasó por Vite correctamente.

### 10.3 La corrección de `loadEnv`

El cambio final de configuración solucionó un problema concreto: `loadEnv` se había importado desde `vitest/config`, pero esa exportación pertenece a `vite`. El import correcto es:

```ts
import { loadEnv } from "vite";
import { defineConfig } from "vitest/config";
```

La distinción es importante porque `defineConfig` se necesita para conservar la configuración de Vitest, mientras que `loadEnv` es una utilidad del propio Vite. Si se cambia la configuración en el futuro, no hay que volver a agrupar ambos imports suponiendo que proceden del mismo módulo.

## 11. `robots.txt` y `sitemap.xml`

### 11.1 Por qué están en `public/`

Los ficheros se sirven como recursos estáticos, antes de que Nginx aplique el fallback de la SPA. Al estar en `frontend/public/`, Vite los copia a `dist/` conservando sus nombres:

```text
dist/robots.txt
dist/sitemap.xml
```

La configuración de Nginx busca primero el recurso físico. Por eso la URL `/robots.txt` ya no termina en `index.html`.

### 11.2 Generación del sitemap

El script [`frontend/scripts/generate-seo.ts`](../frontend/scripts/generate-seo.ts) se ejecuta mediante:

```json
"build": "bun run seo:generate && tsc -b && vite build"
```

El orden es deliberado: primero se regeneran los archivos, después se verifica TypeScript y finalmente Vite crea el artefacto de producción.

El script toma las rutas estáticas de `NAV_ROUTES` y `MOVILIDAD_ROUTE`. Después añade las fichas de los arrays `RESTAURANTES`, `ALOJAMIENTOS`, `VISITAS`, `RUTAS`, `ESPACIOS` y `EXPLORA`.

El sitemap actual contiene 42 URLs:

| Grupo | Número de URLs |
| --- | ---: |
| Portada | 1 |
| Páginas principales y navegación | 16 |
| Movilidad | 1 |
| Fichas de restaurantes | 4 |
| Fichas de alojamiento | 3 |
| Fichas de visitas | 5 |
| Fichas de rutas | 8 |
| Fichas de espacios naturales | 3 |
| Fichas de explora | 1 |
| **Total** | **42** |

La cifra de cada detalle se basa en los datos actuales; la tabla de arriba puede cambiar cuando se añadan o eliminen elementos. La garantía importante no es que el número siga siendo 42, sino que el sitemap se regenere desde las mismas fuentes que usa la aplicación.

Antes de escribir, el script elimina duplicados con `Set`. También escapa caracteres XML (`&`, `<`, `>`, comillas y apóstrofes) para que un slug o dominio especial no rompa el documento.

### 11.3 `lastmod`

Cada URL recibe un `<lastmod>`. El valor por defecto es la fecha del build, pero se puede fijar explícitamente mediante `SEO_LASTMOD`.

En producción conviene usar `SEO_LASTMOD` con criterio. Marcar todas las URLs como modificadas cada vez que se reconstruye la imagen puede ser engañoso si solo se actualizó una dependencia. Para una primera versión es tolerable, pero a medio plazo sería mejor que el dato proviniera de la fecha real de cada contenido o que el generador recibiera una política de actualización por secciones.

### 11.4 Robots

El contenido actual es:

```text
User-agent: *
Allow: /
Disallow: /api/

Sitemap: https://alboloduy-turismo.setenova.es/sitemap.xml
```

Se permite rastrear el sitio público y se evita que los bots rastreen la API de movilidad. La API no es una superficie editorial y no debe convertirse en miles de URLs rastreables.

El sitemap se declara con una URL absoluta. Si cambia el dominio, debe cambiar tanto `VITE_SITE_URL` como el resultado generado de robots y sitemap mediante un nuevo build.

### 11.5 Sincronización de entornos

La corrección del commit `3a586e5` es importante. Vite usa `loadEnv()` y el generador también carga el entorno de producción con `loadEnv("production", process.cwd(), "VITE_")`. De esta manera, un `VITE_SITE_URL` en `.env.production` no modifica solo los canonicals del HTML dejando sitemap y robots con el dominio antiguo.

La prioridad es:

1. variable presente en `process.env`;
2. valor cargado desde los ficheros `.env` por Vite;
3. dominio predeterminado del proyecto.

Esto significa que una variable de entorno inyectada por Docker o CI gana sobre el fichero local, que es el comportamiento esperado en producción.

## 12. Integración con Docker y Compose

### 12.1 Variables declaradas

Se han incorporado estas variables a `.env.example`:

```dotenv
VITE_SITE_URL=https://alboloduy-turismo.setenova.es
VITE_SEO_IMAGE_URL=/og-image.png
```

`VITE_SITE_URL` es el dominio canónico. `VITE_SEO_IMAGE_URL` puede ser una ruta relativa o una URL absoluta.

### 12.2 Argumentos del build

`docker-compose.yaml` pasa ambas variables como argumentos del servicio frontend:

```yaml
VITE_SITE_URL: ${VITE_SITE_URL:-https://alboloduy-turismo.setenova.es}
VITE_SEO_IMAGE_URL: ${VITE_SEO_IMAGE_URL:-/og-image.png}
```

El `Dockerfile` las declara con `ARG` y las expone como `ENV` durante la fase de compilación. Es esencial entender que Vite sustituye las variables en el momento del build: cambiar una variable en el contenedor ya construido no cambia el HTML que está dentro de `dist`.

Por tanto, al cambiar dominio o imagen hay que reconstruir la imagen frontend:

```bash
docker compose build frontend
docker compose up -d frontend
```

Un simple reinicio no es suficiente si el valor cambió durante la compilación.

### 12.3 Dominio institucional futuro

El dominio actual es provisional: `alboloduy-turismo.setenova.es`. Cuando exista el dominio institucional, hay que revisar conjuntamente:

1. `VITE_SITE_URL` en el entorno de producción.
2. `VITE_SEO_IMAGE_URL` si la imagen se mueve.
3. `robots.txt` y `sitemap.xml` generados.
4. canonicals y `og:url` del HTML.
5. `Organization.url` y cualquier URL institucional relacionada.
6. certificados TLS, HAProxy y DNS.
7. redirecciones 301 desde el dominio anterior.
8. Google Search Console y Bing Webmaster Tools.
9. enlaces externos y documentación de despliegue.

No se debe cambiar únicamente `Organization.url`: eso describiría otra organización o dominio dentro de un documento cuyo canonical sigue apuntando al anterior.

## 13. Qué ocurre en producción con una URL concreta

Consideremos `/restaurantes/bar-aca-la-mari`.

### 13.1 Petición inicial

Nginx intenta encontrar un fichero físico. No existe una carpeta equivalente en `dist`, así que devuelve `index.html` por el fallback. El HTML inicial contiene los metadatos de la portada, porque todavía no se ha ejecutado React.

### 13.2 Carga de JavaScript

El navegador descarga el bundle principal y las partes necesarias. React Router resuelve la ruta `/:section/:slug` y renderiza `DetallePage`.

### 13.3 Ejecución de `Seo`

`Seo` observa el pathname, encuentra el restaurante en los datos, obtiene su resumen traducido, crea el título y la descripción, y sustituye el estado del `<head>` por:

- título de Bar Aca La Mari;
- description de restaurante;
- canonical de esa ficha;
- Open Graph tipo `article`;
- Twitter Card;
- JSON-LD `Restaurant`;
- breadcrumb.

Para un usuario con navegador, el resultado es el esperado. Para un rastreador que no ejecute JavaScript, el resultado sigue siendo la portada. Esta es exactamente la razón por la que el prerenderizado está en P1.

## 14. Pruebas incorporadas

Se ha creado [`frontend/src/seo.test.tsx`](../frontend/src/seo.test.tsx) con pruebas del comportamiento observable del `<head>`:

1. Metadatos y datos estructurados de una página de sección.
2. Ficha de restaurante como `Restaurant` y con dirección.
3. Normalización de la barra final en el canonical.
4. Límite máximo de 155 caracteres para descripción.
5. Eventos de fiestas y coordenadas de una ficha.
6. `noindex,follow` y título de una ruta inexistente.

También se actualizó `frontend/src/App.test.tsx` para comprobar el comportamiento a través de `App`, no solo montando `Seo` de forma aislada. Eso verifica que la integración real de rutas actualiza el título y el canonical.

La validación de la rama en el momento de redactar este documento es:

```text
26 ficheros de prueba pasados
114 pruebas pasadas
typecheck correcto
lint correcto
build de producción correcto
42 URLs generadas en sitemap.xml
```

Las pruebas de React usan jsdom, por lo que verifican el DOM y los metadatos después de ejecutar el componente. No sustituyen una prueba de un servidor HTTP real ni una inspección de un HTML prerenderizado.

## 15. Procedimiento de mantenimiento futuro

### 15.1 Añadir una página principal nueva

Si la página aparece en el menú:

1. Añadir la ruta a `NAV_ROUTES`.
2. Registrar la ruta en `App.tsx` con su componente.
3. Añadir una clave SEO específica en los cuatro locales si la página tiene contenido real.
4. Añadir la ruta a `PAGINAS_DEDICADAS` en `seo.tsx` y su nuevo tipo a `TipoPaginaSeo`.
5. Añadir una prueba de título, descripción, canonical y schema si aplica.
6. Ejecutar `bun run seo:generate` o `bun run build`.
7. Comprobar que aparece una sola vez en el sitemap.

Si aún es una placeholder, el paso 3 puede utilizar la plantilla provisional existente, pero hay que decidir si realmente debe ser indexable.

### 15.2 Añadir un restaurante, alojamiento o atracción

Para una ficha de `Descubre`:

1. Añadir el elemento al array adecuado en `frontend/src/data/descubre.ts`.
2. Añadir sus traducciones de resumen y contenido.
3. Usar un slug estable, legible y único dentro de su sección.
4. Añadir una foto disponible públicamente mediante `mediaUrl()`.
5. Añadir `mapa` numérico únicamente si las coordenadas están verificadas.
6. Añadir teléfono y dirección solo si están confirmados.
7. Regenerar sitemap y revisar que la nueva ficha aparece.
8. Probar que `Seo` produce el tipo Schema.org esperado.

No hace falta modificar manualmente el array de URLs del sitemap: el generador obtiene los detalles de los arrays de datos. Sí hace falta revisar la lógica de sección si se crea una categoría nueva que no exista en `SECCION_I18N`, `TIPO_DETALLE` o `TIPO_SCHEMA`.

### 15.3 Cambiar un título o una descripción

Los textos SEO deben editarse en los cuatro locales. No hay que cambiar el texto directamente en `seo.tsx`, porque el componente contiene la lógica y los locales contienen el contenido editorial.

Después de editar:

1. Comprobar que el título no supera el límite previsto.
2. Comprobar que la descripción no pierde el sujeto o la llamada principal al recortarse.
3. Ejecutar las pruebas.
4. Revisar el HTML inicial de portada y el HTML dinámico si se ha tocado la lógica.

Si una traducción utiliza una longitud o un estilo distinto, no se debe forzar una traducción literal del español. Lo importante es conservar la intención de búsqueda y el significado en cada mercado.

### 15.4 Añadir o modificar una fiesta

Hay que cambiar dos fuentes coordinadas:

- el texto visible y traducido en `fiestas.cronologia`;
- la información estructural en `EVENTOS_FIESTAS`.

Si el evento se celebra cada año en un mes, usar `eventSchedule`. Si existe una fecha fija y verificable para la edición del año actual, usar fechas concretas. No se debe conservar una fecha antigua solo para evitar modificar el código.

Después de cualquier cambio de fiestas hay que validar el JSON-LD con una herramienta de resultados enriquecidos y revisar la fecha que se está publicando.

### 15.5 Cambiar el dominio o la imagen social

Editar el `.env` de producción o los secretos/variables de CI, no solo los valores predeterminados del código. Ejecutar un build limpio, inspeccionar `dist/index.html`, `dist/robots.txt` y `dist/sitemap.xml`, y desplegar la imagen reconstruida.

Comprobaciones mínimas:

```bash
rg '__SEO_|alboloduy-turismo.setenova.es' frontend/dist/index.html
curl -I https://dominio-real.example/robots.txt
curl -I https://dominio-real.example/sitemap.xml
```

El primer comando debe devolver cero placeholders y no debe encontrar el dominio antiguo si se ha migrado completamente.

## 16. Lista de comprobación antes de producción

### Configuración

- [ ] `VITE_SITE_URL` apunta al dominio público definitivo.
- [ ] `VITE_SEO_IMAGE_URL` apunta a una imagen real y accesible.
- [ ] No se han dejado variables de entorno de desarrollo en el build.
- [ ] Se ha reconstruido la imagen Docker después de cambiar variables.

### Archivos públicos

- [ ] `/robots.txt` devuelve `Content-Type` de texto y no el HTML de la SPA.
- [ ] `/sitemap.xml` devuelve XML y no el HTML de la SPA.
- [ ] El sitemap contiene las rutas actuales.
- [ ] El sitemap no contiene rutas retiradas.
- [ ] El dominio de sitemap, canonical y Open Graph coincide.

### HTML y metadatos

- [ ] `dist/index.html` no contiene `__SEO_SITE_URL__` ni `__SEO_IMAGE_URL__`.
- [ ] La portada tiene título, descripción, canonical, OG, Twitter y JSON-LD válidos.
- [ ] Una navegación interna actualiza `document.title` y canonical.
- [ ] Una URL con barra final canonicaliza sin barra.
- [ ] Una URL desconocida obtiene `noindex,follow` después de ejecutar React.
- [ ] La imagen social mide 1200 × 630 y responde sin autenticación.

### Datos estructurados

- [ ] La portada aparece como `TouristDestination`.
- [ ] El Ayuntamiento aparece como `Organization`.
- [ ] Las fichas usan el tipo correcto.
- [ ] Los teléfonos ausentes no se publican como teléfonos.
- [ ] Las coordenadas son reales y están en el orden latitud,longitud.
- [ ] Los eventos tienen fechas o recurrencia coherentes.
- [ ] Se ha pasado una validación de Schema.org/resultados enriquecidos.

### Operación

- [ ] Se ha dado de alta el dominio en Search Console.
- [ ] Se ha enviado el sitemap.
- [ ] Se ha revisado la inspección de varias URLs representativas.
- [ ] Se ha comprobado el resultado de compartir una URL en redes o herramientas de preview.
- [ ] Se han planificado redirecciones 301 si se abandona el dominio provisional.

## 17. Limitaciones actuales que no deben confundirse con bugs

### 17.1 No hay prerenderizado

El HTML inicial de una ruta interna sigue siendo el shell común. `Seo` corrige los metadatos cuando React se ejecuta, pero no puede cambiar la respuesta que ya salió del servidor.

La solución futura más compatible con la infraestructura existente es prerenderizar las rutas durante el build y producir un `index.html` específico para cada URL. También se puede valorar SSR con React Router o una migración parcial a Astro. La elección debe tener en cuenta mapas, gráficos de movilidad, galerías y componentes que dependen del navegador.

### 17.2 No hay URLs por idioma

Las traducciones existen, pero no son cuatro conjuntos de URLs rastreables. El siguiente paso internacional debe definir primero el modelo de URL y después implementar `hreflang`, alternates, canonical por idioma y sitemap multidioma.

### 17.3 El 404 no es todavía un 404 HTTP

El usuario ve una página de no encontrado y React añade `noindex`, pero Nginx continúa devolviendo el shell con HTTP 200 para la ruta. Esto mitiga parte del problema para rastreadores con JavaScript, pero no elimina el soft 404 de servidor.

### 17.4 Los eventos requieren mantenimiento anual

Las fechas concretas se basan en el año actual UTC y los eventos recurrentes expresan meses o días. El código no sustituye a una agenda municipal actualizada. Antes de cada temporada de fiestas hay que revisar los datos.

### 17.5 El SEO no garantiza posiciones

La implementación mejora rastreo, comprensión y presentación. No garantiza una posición concreta. El posicionamiento también dependerá de contenido, enlaces, autoridad del dominio, fichas locales, reseñas, competencia, rendimiento y señales de utilidad para usuarios reales.

## 18. Futuras implementaciones recomendadas

### Prioridad 1: prerenderizado

Es la mejora técnica con mayor impacto pendiente. Debe generar HTML específico para cada ruta pública y conservar el comportamiento de hidratación de React. El trabajo debería incluir:

1. enumerar todas las rutas del sitemap;
2. renderizar cada una durante el build;
3. copiar el resultado a la estructura que Nginx sirve;
4. comprobar que la navegación posterior sigue funcionando;
5. verificar que cada HTML tiene el contenido y el `<head>` de su ruta;
6. decidir cómo se sirve una ruta inexistente con 404 real.

### Prioridad 2: URLs multidioma y `hreflang`

Antes de escribir código hay que decidir si los slugs se traducen o se mantienen en español. Después habrá que implementar:

- prefijo de idioma;
- enlaces reales entre versiones;
- canonical de cada idioma;
- `link rel="alternate" hreflang="..."`;
- `x-default`;
- sitemap con alternates o varios sitemaps;
- títulos, descripciones y schema por idioma.

No conviene añadir etiquetas `hreflang` apuntando a URLs que todavía no existen. Una etiqueta incorrecta es peor que una ausencia temporal porque puede enviar al buscador a páginas equivalentes inexistentes.

### Prioridad 3: fuente única de datos SEO

Cuando exista prerenderizado, convendrá extraer la resolución SEO y la generación de head a un módulo agnóstico de React. Ese módulo podría producir:

- metadatos para el componente del navegador;
- JSON-LD;
- datos para cada HTML prerenderizado;
- quizá datos del sitemap.

Así se reducirá la duplicación entre `index.html`, `Seo.tsx` y scripts de build. No hay que hacer esa abstracción antes de conocer la herramienta de prerenderizado: primero debe existir un seam real que justifique separar responsabilidades.

### Prioridad 4: gobierno editorial

El SEO técnico necesita un proceso de contenido:

- revisar títulos y descripciones cuando cambie la intención de búsqueda;
- mantener datos de restaurantes y alojamientos;
- actualizar fiestas cada año;
- retirar fichas sin contenido suficiente o completarlas;
- controlar enlaces rotos y fotos faltantes;
- revisar Search Console mensualmente.

Un sitemap perfecto que apunta a fichas vacías o desactualizadas no produce una buena experiencia ni una buena señal de calidad.

### Prioridad 5: medición

Después del despliegue se deben registrar, como mínimo:

- impresiones y clics por consulta;
- posición media;
- URLs indexadas;
- errores de cobertura;
- páginas rastreadas pero no indexadas;
- Core Web Vitals móviles;
- clics desde redes sociales y enlaces compartidos.

La medición permite distinguir un problema técnico —Google no puede rastrear— de un problema editorial —Google rastrea, pero no considera la página suficientemente útil—.

## 19. Resumen final

La rama convierte el SEO de una única descripción estática en un sistema coordinado de metadatos, datos estructurados y recursos de rastreo.

El componente `Seo` resuelve la página actual, normaliza la URL, obtiene los textos del idioma activo, actualiza el `<head>` y genera un grafo Schema.org. `index.html` proporciona una base válida antes de React. Vite sustituye el dominio y la imagen social durante el build. `generate-seo.ts` crea `robots.txt` y `sitemap.xml` desde las rutas y datos del proyecto. Docker y Compose pasan la configuración necesaria. Las pruebas verifican el resultado visible y el build confirma que los placeholders no llegan al artefacto final.

La principal precaución es no presentar esta solución como prerenderizado: las rutas internas siguen dependiendo de JavaScript para que sus metadatos específicos aparezcan en el HTML del navegador. La siguiente gran inversión debe ser generar HTML por ruta y, después, abordar URLs multidioma con `hreflang`.

Mientras tanto, el mantenimiento correcto consiste en conservar una sola fuente de verdad para rutas y datos, traducir cada cambio SEO en los cuatro locales, regenerar y revisar el sitemap, comprobar los valores de producción y validar periódicamente el comportamiento real desde fuera del navegador.
