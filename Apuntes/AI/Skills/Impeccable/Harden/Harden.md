---
aliases:
  - Impeccable Harden
  - Endurecimiento de UI
  - Robustez de Componentes
tags:
  - dev/ai
  - design-system
  - impeccable
source: https://impeccable.style/docs/harden/
---
# `/impeccable harden` — Impeccable Design Skill

> [!abstract] Resumen Ejecutivo
> `/impeccable harden` es el comando especializado en **blindar la interfaz frente a casos límite (*edge cases*) y condiciones de datos imprevistas**. Garantiza que los componentes no se rompan visualmente ante textos extremadamente largos, estados sin datos, retardos de red, nombres sin espacio, imágenes faltantes o desbordamientos de contenedor.

---

## 🎯 Objetivo Principal

* **Resistencia a Datos Reales:** Previene que la maquetación se colapse o sufra desbordamientos (*overflows*) cuando los datos reales difieren de los prototipos perfectos.
* **Manejo de Casos Límite:** Implementa soluciones resilientes para truncamiento de texto, valores nulos, listas vacías y cargas de red lentas.
* **Calidad de Software Profesional:** Transforma prototipos frágiles en componentes de nivel de producción listos para cualquier escenario.

---

## 🔍 ¿Cuándo utilizarlo?

* **Pruebas de Estrés de UI:** Antes de lanzar una función a producción para validar cómo responde la interfaz con datos no controlados.
* **Componentes Frágiles:** Cuando notas que un nombre largo de usuario desencaja un botón o rompe el alineamiento de una tarjeta.
* **Integración con APIs:** Al conectar la UI con endpoints cuyos datos pueden llegar incompletos, vacíos o demorarse.

---

## 📋 Flujo de Trabajo

1. **Inyección de Estrés:** Revisa el componente simulando entradas extremas (cadenas de texto sin espacios, títulos de 200 caracteres, URLs de imagen rotas, arreglos vacíos).
2. **Identificación de Puntos de Ruptura:** Detecta elementos con anchos fijos rígidos, falta de `line-clamp`, ausencia de `overflow-hidden` o falta de estados de carga/error.
3. **Refactorización de Robustez:** Aplica truncamientos elegantes, valores por defecto (*fallbacks*), estados *skeleton* y contenedores flexibles con límites defensivos.

---

## 🛡️ Casos Límite que Resuelve

> [!check] Blindajes Típicos que Aplica
> * **Textos Extremos:** Aplica `truncate`, `line-clamp` o `break-words` para evitar desbordamientos horizontales.
> * **Recursos Faltantes:** Añade imágenes de reserva (*fallback avatars/placeholders*) para manejar errores de carga (404).
> * **Estados Nulos y Vacíos:** Maneja elegantes *empty states* cuando las listas o colecciones no devuelven ítems.
> * **Asincronía:** Incorpora indicadores de carga (*spinners*, esqueletos) y estados de error interactivos.

---

## 🚀 Uso Rápido

```bash
# Blindar un componente contra datos extremos o casos límite
/impeccable harden [componente o archivo]
```
