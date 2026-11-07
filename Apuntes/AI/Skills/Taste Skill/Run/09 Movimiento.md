---
aliases:
  - Taste Skill Paso 9
  - Movimiento
  - Motion layer
  - Signature move
tags:
  - dev/ai
  - design-system
  - taste-skill
  - taste-skill/run
  - frontend
  - animation
source: https://github.com/Leonxlnx/taste-skill/blob/main/skills/taste-skill/SKILL.md
---
# 09 — Movimiento

> [!abstract] Resumen Ejecutivo
> La capa de movimiento es obligatoria y es más que un fade. El inspector cuenta los elementos que realmente se mueven al cargar y al hacer scroll: falla una página con menos de **8** y avisa con menos de **16**. La capa tiene cuatro partes: el movimiento firma, la entrada de cada sección, el feedback de cada control y una imagen en movimiento.

---

## 🎯 Objetivo Principal

* **Movimiento con propósito:** cada movimiento confirma una acción, muestra una relación espacial o reduce la espera percibida.
* **Entrada por partes:** no un fade para todo el bloque.
* **Accesibilidad:** todo movimiento tiene fallback con `prefers-reduced-motion`.

---

## 📋 Las cuatro partes

### 1. El movimiento firma

* El que nombra la ficha de dirección.
* Uno, bien hecho. Una escena repartida puede serlo.

### 2. Entrada de las partes de cada sección

* Primero el encabezado, luego el texto, luego la imagen.
* Stagger de **60–90 ms**.
* Subida de **16–32 px** con opacidad.
* Se dispara una vez cuando la sección entra.
* No un fade para todo el bloque.
* **Nunca** contenido que se queda oculto si falla el script.

### 3. Feedback en cada control

* Press, hover y focus en botones, enlaces, tarjetas y la cosa viva.

### 4. Una imagen en movimiento

* Un clip encontrado como loop silenciado.
* Un scale o drift lento sobre una fotografía.
* Un movimiento ligado al scroll en la imagen del hero.

---

## ⚙️ Reglas técnicas

* Solo **transform** y **opacity**.
* Todo movimiento tiene fallback `prefers-reduced-motion` que mantiene el contenido y elimina el viaje.
* Un movimiento que cambia el color o el layout de pantallas enteras (color por capítulo, una historia pinned) es parte de la composición: se construye en el paso 6 y se ve en cada ronda de mirada.
* `references/motion.md` tiene los tokens y recetas probadas.
* Después de añadir movimiento, corre el inspector otra vez y revisa cada captura en busca de contenido que no apareció.

---

## 💡 Regla del dial

> [!important] Si `MOTION_INTENSITY` es mayor que 4, la página anima de verdad.
> Si no, quita el dial.

---

## 🔗 Relacionado

* [[04 Encontrar la idea]]
* [[06 Construir]]
* [[08 Mirar]]
* [[10 Informe]]
