---
tags:
  - ai/skills
  - ai/ponytail
---

## **Propósito**

Muestra la **referencia rápida de Ponytail**, incluyendo:

* Modos.
* Skills.
* Comandos.
* Configuración.
* Desactivación.
* Actualización.

Es esencialmente el manual rápido integrado del plugin.

---

## 🎯 ¿Cuándo usarla?

Cuando no recuerdes:

```text
qué skills existen
```

```text
cómo cambiar de nivel
```

```text
cómo desactivar Ponytail
```

```text
cómo configurar el modo predeterminado
```

Puedes ejecutar:

```text
/ponytail-help
```

---

## 🎚️ Referencia de niveles

```text
/ponytail lite
```

Implementa lo solicitado pero muestra una alternativa más simple.

```text
/ponytail
```

Activa el modo **Full**, aplicando toda la escalera Ponytail.

```text
/ponytail ultra
```

Activa YAGNI extremo y prioriza eliminar antes que añadir.

---

## ⚙️ Configuración del modo por defecto

El modo predeterminado es:

```text
full
```

Puede cambiarse mediante variable de entorno:

```bash
export PONYTAIL_DEFAULT_MODE=ultra
```

O mediante:

```text
~/.config/ponytail/config.json
```

con:

```json
{
  "defaultMode": "lite"
}
```

También puede utilizarse:

```json
{
  "defaultMode": "off"
}
```

para evitar que Ponytail se active automáticamente.

La prioridad es:

```text
environment variable
        ↓
config file
        ↓
full
```

---