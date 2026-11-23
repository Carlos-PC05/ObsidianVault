---
tags:
  - ai/skills
  - ai/android-review
  - android
source: https://github.com/liuyi0808/android-review/blob/main/skills/compose-ui/SKILL.md
---
# Compose UI

## **Propósito**

Revisar interfaces Jetpack Compose y sus patrones de estado, efectos y accesibilidad.

## 🎯 ¿Cuándo usarla?

Al construir o auditar pantallas Compose, navegación y componentes Material 3.

## 🔎 Qué revisa

- **Estado:** elevarlo al propietario adecuado y recoger flujos teniendo en cuenta el ciclo de vida con `collectAsStateWithLifecycle`.
- **Efectos:** comprobar claves de `LaunchedEffect` y limpieza en `DisposableEffect`.
- **Navegación:** revisar rutas tipadas y paso de datos entre pantallas.
- **Tema:** usar Material 3, evitar colores codificados y comprobar tema oscuro.
- **Accesibilidad:** descripciones, objetivos táctiles y contraste; la referencia propone 48 dp y 4,5:1.
- **Adaptación y animación:** considerar tamaños de pantalla y preferencias de movimiento.

## 📄 Resultado

Hallazgos `[COMPOSE-*]` con ubicación, severidad y solución.

## 🔗 Relacionado

* [[Android Review]]
