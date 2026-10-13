---
alias:
  - Skill frontend-ui-engineering
  - Addy Osmani Frontend UI Engineering
tags:
  - ai/skills
  - addy-osmani
  - ai/build
  - ui-ux
  - dev/design
source: https://github.com/addyosmani/agent-skills/blob/main/skills/frontend-ui-engineering/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Construye interfaces **accesibles, performantes y pulidas**. El objetivo es UI que parezca hecha por un ingeniero con criterio de diseño en una empresa top — **no como generada por una IA**. Eso significa adhesión real al design system, accesibilidad adecuada, patrones de interacción pensados, y **cero "estética de IA"**.

---

## 🎯 ¿Cuándo usarla?

Construyendo componentes o páginas nuevas · modificando interfaces existentes · implementando layouts responsive · añadiendo interactividad o gestión de estado · arreglando problemas visuales o de UX.

---

## 🧱 Arquitectura de componentes

**Coloca todo lo relacionado en un mismo sitio:**
```
src/components/TaskList/
  TaskList.tsx          # implementación
  TaskList.test.tsx     # tests
  TaskList.stories.tsx  # stories (si usas Storybook)
  use-task-list.ts      # hook de estado complejo
  types.ts              # tipos específicos del componente
```

**Prefiere composición sobre configuración:**
```tsx
<Card>                    // ✅ componible
  <CardHeader><CardTitle>Tasks</CardTitle></CardHeader>
  <CardBody><TaskList tasks={tasks} /></CardBody>
</Card>

<Card title="Tasks" headerVariant="large" content={<TaskList/>} />  // ❌ sobre-configurado
```

**Separa fetching de presentación:** un *container* que maneja datos/loading/error/vacío, y un componente *presentational* que solo renderiza.

---

## 🗄️ Gestión de estado — elige lo más simple que funcione

```
Estado local (useState)          → estado de UI específico del componente
Estado elevado                   → compartido entre 2-3 hermanos
Contexto                         → tema, auth, locale (lectura frecuente, escritura rara)
Estado en URL (searchParams)     → filtros, paginación, UI compartible
Estado de servidor (React Query) → datos remotos con caché
Store global (Zustand, Redux)    → estado cliente complejo compartido
```
**Evita el prop drilling más de 3 niveles.** Si pasas props por componentes que no los usan, introduce contexto o reestructura el árbol.

---

## 🎨 Adhesión al design system

### UI de calidad guiada por referencias
Cuando el producto necesita una dirección visual distinta, **recoge evidencia antes de elegir layout**: busca un catálogo de referencias fiable o usa las que da el equipo → estudia 2–3 pantallas relevantes, anotando jerarquía, densidad, navegación, controles, comportamiento responsive y estados de interacción → convierte esas decisiones en un **contrato de diseño** breve (el trabajo de la pantalla, la acción principal, estados requeridos, reglas responsive, patrones a rechazar) → **reconstruye la estructura útil** en los componentes, tokens y lenguaje visual del producto. **Nunca copies** el branding, texto propietario, imágenes o layout exacto de otro producto.

> [!tip] Las referencias son evidencia, no plantillas
> Si no hay referencias disponibles, **documenta los supuestos** y verifica el resultado contra el design system existente del producto.

### Evita la estética de IA

| Default de IA | Por qué es un problema | Calidad de producción |
| :--- | :--- | :--- |
| Todo morado/índigo | Paleta visualmente "segura" → todas las apps parecen iguales | Usa la paleta real del proyecto |
| Gradientes excesivos | Ruido visual que choca con casi todo design system | Planos o gradientes sutiles |
| Redondear todo (`rounded-2xl`) | Máximo redondeo = "amistoso", pero ignora la jerarquía de radios | Radio consistente del design system |
| Secciones hero genéricas | Layout por plantilla, sin conexión al contenido real | Layouts *content-first* |
| Texto tipo lorem ipsum | El placeholder esconde los problemas de layout que el contenido real revela | Contenido placeholder realista |
| Padding desbordado en todo | Padding generoso igual en todo destruye la jerarquía visual | Escala de espaciado consistente |
| Rejillas de tarjetas idénticas | Atajo de layout que ignora prioridad de información | Layouts dirigidos por propósito |
| Sombras en capas | Compiten con el contenido y ralentizan el render en dispositivos modestos | Sombras sutiles o ninguna |

### Espaciado, tipografía y color
Usa una **escala de espaciado consistente** — no inventes valores (`padding: 13px` no está en ninguna escala). Respeta la **jerarquía tipográfica** (`h1` título de página, uno por página → `h2` sección → `h3` subsección → body → small) y **no saltes niveles**. Usa **tokens semánticos** (`text-primary`, `bg-surface`), no hex crudos, y asegura **contraste suficiente** (4.5:1 texto normal, 3:1 texto grande). **No dependas solo del color** para transmitir información.

---

## ♿ Accesibilidad (WCAG 2.1 AA) — no negociable

- **Navegación por teclado.** Todo elemento interactivo debe ser accesible por teclado. `<div onClick>` **no** es enfocable; prefiere `<button>`. Si necesitas un div, añade `role`, `tabIndex` y manejo de `Enter`/`Space`.
- **Labels ARIA.** Elementos interactivos sin texto visible necesitan `aria-label`; los inputs necesitan `<label htmlFor>` o `aria-label`.
- **Gestión del foco.** Mueve el foco cuando cambia el contenido (p. ej. al abrir un diálogo) y **atrapa el foco** dentro del diálogo mientras esté abierto.
- **Estados vacíos y de error con significado.** Nunca pantallas en blanco. Incluye icono, título, mensaje y acción.

---

## 📱 Responsive

**Diseña mobile-first y expande.** Testea en **320px, 768px, 1024px y 1440px**.

## ⚡ Carga y transiciones
Usa **skeletons** (no spinners) para contenido, y **actualizaciones optimistas** con rollback para dar sensación de velocidad.

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "La accesibilidad es nice-to-have" | Es un requisito legal en muchas jurisdicciones y un estándar de calidad de ingeniería |
| "Haremos el responsive luego" | Adaptar responsive es **3× más difícil** que construirlo desde el principio |
| "El diseño no está final, así que salto estilos" | Usa los defaults del design system. UI sin estilos da una primera impresión rota a quien revisa |
| "Esto es solo un prototipo" | Los prototipos se vuelven producción. Construye bien los cimientos |
| "La estética de IA sirve por ahora" | Señala baja calidad. Usa el design system real del proyecto desde el inicio |

---

## 🚩 Red Flags

- Componentes de **más de 200 líneas** (divídelos).
- Estilos inline o valores de píxeles arbitrarios.
- Estados de carga, error o vacío **faltantes**.
- Sin testing de navegación por teclado.
- Color como **único** indicador de estado (rojo/verde sin texto o icono).
- Aspecto genérico de "IA" (gradientes morados, tarjetas enormes, layouts de stock).

---

## 🔗 Relaciones

**En el flujo de Addy:** se activa automáticamente al construir UI. Se apoya en [[test-driven-development]] (sus tests de componentes) y [[browser-testing-with-devtools]] (verificación runtime, screenshots responsive). Sus números de accesibilidad y performance los fija [[constraint-driven-development]].

**Equivalencia Matt Pocock:** **solapamiento fuerte con tus notas de `Impeccable`.** Impeccable trabaja el **pulido visual** (geometría, color, tipografía, motion, jerarquía) sobre un sistema de diseño existente; esta skill trabaja la **disciplina de ingeniería** (arquitectura de componentes, estado, a11y obligatoria, responsive, evitar la estética de IA). Complementarias: Impeccable *embellece*, esta *construye bien*.

```bash
npx skills add addyosmani/agent-skills --skill frontend-ui-engineering
```
