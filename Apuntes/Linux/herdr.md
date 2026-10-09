---
tags:
  - linux
  - terminal
---

> **Objetivo:** dominar Herdr hasta convertirlo en el centro de trabajo para desarrollo de software, terminales y agentes de IA.
>
> Herdr no es simplemente otro `tmux`: es un **runtime persistente para agentes de coding**, que combina terminales reales, workspaces, tabs, panes, detección de agentes, persistencia, SSH y automatización.

---
# 1. La idea fundamental

Herdr se coloca entre tu terminal y los procesos que ejecutas.

```text
┌─────────────────────────────────────────────┐
│                 HERDR SERVER                │
│                                             │
│  Workspace: proyecto-web                    │
│  ├── Tab: desarrollo                        │
│  │   ├── Pane: Claude                       │
│  │   ├── Pane: Backend                      │
│  │   └── Pane: Frontend                     │
│  │                                          │
│  ├── Tab: testing                           │
│  │   ├── Pane: Tests                        │
│  │   └── Pane: Logs                         │
│  │                                          │
│  └── Tab: git                               │
│      └── Pane: Git                         │
└─────────────────────────────────────────────┘
                  ▲
                  │
          terminal client
```

La diferencia fundamental respecto a usar una terminal normal:

```text
Terminal normal
terminal → shell → proceso

Herdr
terminal → Herdr client → Herdr server → pane → proceso
```

El **server** mantiene los procesos vivos aunque cierres el terminal.

Por tanto:

```text
cierras terminal
      ↓
Herdr sigue ejecutándose
      ↓
Claude/Codex/servidores/tests siguen ejecutándose
      ↓
vuelves a ejecutar `herdr`
      ↓
recuperas la sesión
```

---

# 2. El modelo mental que tienes que dominar

Herdr tiene cinco conceptos fundamentales:

```text
Session
  └── Workspace
       ├── Tab
       │    ├── Pane
       │    ├── Pane
       │    └── Pane
       │
       └── Tab
            └── Pane
                 └── Agent
```

## Session

Es el **entorno de ejecución persistente** de Herdr.

Una sesión contiene:

* workspaces
* tabs
* panes
* procesos
* agentes
* estado persistente

La sesión por defecto se abre simplemente con:

```bash
herdr
```

También puedes tener sesiones independientes:

```bash
herdr session list

herdr session attach work

herdr session attach side-project
```

### Cuándo usar sesiones diferentes

Normalmente:

> **Un workspace por proyecto.**

Usa sesiones independientes cuando quieras separar completamente entornos de trabajo.

Ejemplo:

```text
Session: trabajo
├── proyecto-api
├── proyecto-web
└── infraestructura

Session: personal
├── proyecto-IA
└── laboratorio
```

---

# 3. Workspace

El workspace es probablemente el concepto más importante para tu día a día.

Piensa:

> **Workspace = proyecto/contexto de trabajo.**

Ejemplo:

```text
Workspace: factura-ai
```

Dentro:

```text
factura-ai
├── desarrollo
├── agentes
├── testing
└── git
```

Un workspace contiene tabs y panes.

La recomendación oficial es utilizar aproximadamente:

> **un workspace por repositorio, tarea o investigación.**

---

# 4. Tabs

Una tab es una disposición de trabajo dentro de un workspace.

Por ejemplo:

```text
Workspace: mi-app

Tabs:
├── desarrollo
├── testing
├── agentes
└── logs
```

Una organización muy buena:

```text
Tab "dev"
├── backend
├── frontend
└── database

Tab "agents"
├── Claude
├── Codex
└── reviewer

Tab "testing"
├── tests
└── logs

Tab "git"
└── git / lazygit
```

La clave:

> **No abras 20 terminales. Organiza el contexto.**

---

# 5. Panes

Un pane es una **terminal real**.

Dentro puedes ejecutar exactamente lo mismo que ejecutarías normalmente:

```bash
npm run dev
docker compose up
git status
python main.py
claude
codex
```

Puedes dividir panes:

```text
┌─────────────────────┬─────────────────────┐
│                     │                     │
│      Claude         │      Backend        │
│                     │                     │
├─────────────────────┼─────────────────────┤
│                     │                     │
│      Frontend       │       Tests         │
│                     │                     │
└─────────────────────┴─────────────────────┘
```

La filosofía correcta es:

> **Cada proceso persistente importante merece su propio pane.**

---

# 6. Agents

Aquí es donde Herdr empieza a ser especialmente interesante.

Un agente es un proceso que Herdr reconoce dentro de un pane.

Por ejemplo:

```text
Pane
└── claude
```

Herdr puede identificar que ese pane contiene Claude Code.

También puede detectar:

* Claude Code
* Codex
* OpenCode
* Cursor Agent
* GitHub Copilot CLI
* Gemini CLI
* Kimi
* Qwen
* Grok
* etc.

---

# 7. Estados de los agentes

Herdr intenta decirte qué está haciendo cada agente.

Los estados principales son:

| Estado    | Significado                           |
| --------- | ------------------------------------- |
| `working` | Está trabajando                       |
| `blocked` | Necesita intervención                 |
| `done`    | Ha terminado y aún no lo has revisado |
| `idle`    | Está esperando / ya lo has visto      |
| `unknown` | Herdr no sabe determinar el estado    |

Esto es una de las ideas más importantes de Herdr.

En vez de:

```text
¿qué estaba haciendo Claude?
¿terminó Codex?
¿hay algún agente esperando?
```

miras el sidebar:

```text
● proyecto-A       working
● proyecto-B       working
! proyecto-C       blocked
✓ proyecto-D       done
```

---

# 8. State rollup

Los estados se propagan hacia arriba.

Por ejemplo:

```text
Workspace
└── Tab
    └── Pane
        └── Agent = blocked
```

Entonces Herdr puede mostrar:

```text
Workspace = blocked
```

Esto permite utilizar el sidebar como un **panel de control de agentes**.

La idea es:

> No necesitas inspeccionar constantemente cada terminal.

---

# 9. El ratón NO es un ciudadano de segunda clase

Un error sería pensar:

> "Tengo que aprenderme 80 atajos."

No.

Herdr está diseñado para funcionar perfectamente con ratón.

Puedes:

* hacer click en panes
* cambiar tabs
* cambiar workspaces
* hacer click en agentes
* arrastrar divisiones
* seleccionar texto
* hacer click derecho
* abrir menús contextuales

Incluso copiar texto puede hacerse seleccionándolo con el ratón.

Por tanto:

### Principio

```text
Mouse → descubrimiento y operaciones ocasionales

Keyboard → operaciones repetitivas y navegación rápida
```

---

# 10. El concepto clave del teclado: Prefix

Por defecto:

```text
Ctrl+B
```

es el **prefix**.

Herdr no quiere robarte combinaciones que pertenecen al shell, Vim, Emacs, etc.

Por eso funciona así:

```text
Ctrl+B
release
C
```

significa:

```text
prefix + c
```

y crea una nueva tab.

---

# 11. Los 5 atajos que debes memorizar primero

Estos son los fundamentales:

| Acción              | Atajo                |
| ------------------- | -------------------- |
| Nueva tab           | `Ctrl+B` → `C`       |
| Split derecha       | `Ctrl+B` → `V`       |
| Split abajo         | `Ctrl+B` → `-`       |
| Moverse entre panes | `Ctrl+B` → `H/J/K/L` |
| Cambiar workspace   | `Ctrl+B` → `W`       |
| Detach              | `Ctrl+B` → `Q`       |

Si solo memorizas estos, ya puedes trabajar perfectamente.

---

# 12. Navegación de panes

```text
Ctrl+B H   ← izquierda
Ctrl+B J   ↓ abajo
Ctrl+B K   ↑ arriba
Ctrl+B L   → derecha
```

Es la navegación tipo Vim.

Visualmente:

```text
       K
       ↑
H ← pane → L
       ↓
       J
```

Esto merece muchísimo la pena memorizar.

---

# 13. Splits

### Split hacia la derecha

```text
Ctrl+B V
```

Resultado:

```text
┌──────────┬──────────┐
│          │          │
│          │          │
└──────────┴──────────┘
```

### Split hacia abajo

```text
Ctrl+B -
```

Resultado:

```text
┌─────────────────────┐
│                     │
├─────────────────────┤
│                     │
└─────────────────────┘
```

---

# 14. Zoom

```text
Ctrl+B Z
```

Amplía temporalmente el pane seleccionado.

Muy útil cuando tienes:

```text
┌─────────────┬─────────────┐
│             │             │
│             │             │
├─────────────┼─────────────┤
│             │             │
└─────────────┴─────────────┘
```

y quieres trabajar temporalmente solo con:

```text
┌───────────────────────────┐
│                           │
│         Claude            │
│                           │
└───────────────────────────┘
```

---

# 15. Mover / intercambiar panes

```text
Ctrl+B Shift+H
Ctrl+B Shift+J
Ctrl+B Shift+K
Ctrl+B Shift+L
```

Sirven para intercambiar panes.

---

# 16. Resize mode

```text
Ctrl+B R
```

Entra en modo de redimensionado.

Útil cuando quieres controlar el tamaño de:

```text
Claude      │
            │
            │
```

frente a:

```text
Claude      │ Backend
            │
            │
```

---

# 17. Tabs

### Nueva tab

```text
Ctrl+B C
```

### Siguiente tab

```text
Ctrl+B N
```

### Tab anterior

```text
Ctrl+B P
```

### Ir directamente a una tab

```text
Ctrl+B 1
Ctrl+B 2
Ctrl+B 3
...
Ctrl+B 9
```

### Renombrar

```text
Ctrl+B Shift+T
```

### Cerrar

```text
Ctrl+B Shift+X
```

---

# 18. Workspaces

### Crear

```text
Ctrl+B Shift+N
```

### Navegar

```text
Ctrl+B W
```

### Renombrar

```text
Ctrl+B Shift+W
```

### Cerrar

```text
Ctrl+B Shift+D
```

### Picker

```text
Ctrl+B G
```

### Sidebar

```text
Ctrl+B B
```

---

# 19. Detach: la magia de Herdr

Este es uno de los conceptos que tienes que interiorizar.

```bash
Ctrl+B Q
```

NO significa:

> "Cerrar todo."

Significa:

> "Desconectar mi terminal de Herdr."

Por ejemplo:

```text
Claude
   ↓
ejecutando una tarea
   ↓
Ctrl+B Q
   ↓
cierras terminal
   ↓
Claude SIGUE ejecutándose
```

Después:

```bash
herdr
```

y vuelves a la sesión.

---

# 20. Detach vs parar

### Detach

```bash
Ctrl+B Q
```

Mantiene todo funcionando.

### Parar el servidor

```bash
herdr server stop
```

Esto sí detiene los procesos asociados a la sesión.

Por tanto:

> **Detach = me voy.**
>
> **server stop = apago.**

---

# 21. La persistencia

Herdr tiene diferentes niveles de persistencia.

## Detach

Es el mejor caso.

Los procesos siguen vivos:

```text
shells       ✓
servers      ✓
tests        ✓
agents       ✓
layout       ✓
```

---

## Reinicio del servidor

Si el servidor se reinicia:

```text
layout       ✓
cwd          ✓
workspace    ✓
tabs         ✓
panes        ✓

procesos     ✗
```

Herdr puede reconstruir la estructura, pero no puede mantener vivo un proceso que ha muerto.

---

# 22. Agent session restore

Aquí entra una segunda capa.

Algunos agentes tienen sus propias sesiones/conversaciones.

Herdr puede guardar la referencia de la sesión del agente y volver a arrancarlo después.

Ejemplo conceptual:

```text
Claude session
      │
      ▼
agent_session_id
      │
      ▼
Herdr
      │
      ▼
restart
      │
      ▼
claude --resume <id>
```

Esto es muchísimo más potente que simplemente recuperar el texto de la terminal.

---

# 23. Pane history

Herdr puede guardar historial visual de los panes.

Se activa mediante:

```toml
[experimental]
pane_history = true
```

Pero cuidado:

> El historial puede contener contraseñas, tokens, comandos y otros secretos.

Por tanto, no lo actives alegremente en un entorno donde la exposición del historial sea un problema.

---

# 24. Copy mode

Entra con:

```text
Ctrl+B [
```

Dentro puedes moverte con:

```text
h j k l
```

También:

```text
w b e
W B E
{ }
PageUp
PageDown
Ctrl+B
Ctrl+F
Ctrl+U
Ctrl+D
```

---

# 25. Buscar en copy mode

Buscar hacia delante:

```text
/
```

Buscar hacia atrás:

```text
?
```

Siguiente resultado:

```text
n
```

Resultado anterior:

```text
N
```

La búsqueda es normalmente case-insensitive.

Si la búsqueda contiene una letra mayúscula, pasa a distinguir mayúsculas/minúsculas.

---

# 26. Seleccionar y copiar

En copy mode:

```text
v
```

o:

```text
Space
```

inicia selección.

Copiar:

```text
y
```

o:

```text
Enter
```

Salir:

```text
q
Esc
```

Importante:

> Seleccionar con el ratón también permite copiar sin entrar en copy mode.

---

# 27. Ayuda integrada

Este es probablemente el atajo más importante para aprender Herdr:

```text
Ctrl+B ?
```

Abre el mapa de teclado activo.

Además puedes:

```text
/
```

para buscar acciones.

Esto significa que **no necesitas memorizar toda la documentación**.

---

# 28. La estrategia de teclado definitiva

No recomiendo intentar aprender 50 atajos el primer día.

Aprende en este orden:

### Nivel 1

```text
Ctrl+B C
Ctrl+B V
Ctrl+B -
Ctrl+B H/J/K/L
Ctrl+B W
Ctrl+B Q
```

### Nivel 2

```text
Ctrl+B Z
Ctrl+B N
Ctrl+B P
Ctrl+B 1..9
Ctrl+B R
```

### Nivel 3

```text
Ctrl+B Shift+H/J/K/L
Ctrl+B Shift+T
Ctrl+B Shift+X
Ctrl+B Shift+N
Ctrl+B Shift+W
```

### Nivel 4

```text
Ctrl+B [
```

y domina copy mode.

---

# 29. Prefix-free: el modo avanzado

Una vez seas rápido con Herdr, puedes eliminar la necesidad de pulsar:

```text
Ctrl+B
```

antes de determinadas acciones.

Por ejemplo:

```toml
[keys]

focus_pane_left = ["prefix+h", "ctrl+alt+h"]
focus_pane_down = ["prefix+j", "ctrl+alt+j"]
focus_pane_up = ["prefix+k", "ctrl+alt+k"]
focus_pane_right = ["prefix+l", "ctrl+alt+l"]
```

Ahora puedes hacer:

```text
Ctrl+Alt+H
Ctrl+Alt+J
Ctrl+Alt+K
Ctrl+Alt+L
```

directamente.

---

# 30. ¿Por qué Ctrl+Alt?

Porque muchas combinaciones:

```text
Alt+...
Ctrl+...
Ctrl+Shift+...
```

ya están ocupadas por:

* terminal
* shell
* editor
* sistema operativo
* escritorio

`Ctrl+Alt` suele ser una familia bastante libre.

Pero debes comprobar tu entorno.

---

# 31. Atajos que debes evitar en Linux

La documentación advierte especialmente de combinaciones como:

```text
Ctrl+Alt+Arrow
```

porque pueden estar ocupadas por el escritorio.

También:

```text
Ctrl+Alt+T
```

suele abrir una terminal.

Y:

```text
Ctrl+Alt+F1..F12
```

puede cambiar a consolas virtuales.

---

# 32. Configuración

En Linux/macOS:

```text
~/.config/herdr/config.toml
```

Puedes generar una configuración completa:

```bash
herdr --default-config
```

Y guardarla:

```bash
herdr --default-config > ~/.config/herdr/config.toml
```

---

# 33. Cambiar el prefix

Por ejemplo:

```toml
[keys]

prefix = "ctrl+a"
```

Ahora:

```text
Ctrl+A
```

será el prefix.

---

# 34. Cambiar atajos

Ejemplo:

```toml
[keys]

prefix = "ctrl+b"

goto = "prefix+g"
new_tab = "prefix+c"
next_tab = "prefix+n"
previous_tab = "prefix+p"

focus_pane_left = "prefix+h"
navigate_pane_down = "ctrl+j"
```

Los bindings son totalmente configurables.

---

# 35. Mi configuración conceptual recomendada

Si vienes de desarrollo y quieres maximizar productividad:

```text
Prefix
Ctrl+B

Pane navigation
Ctrl+Alt+H/J/K/L

New tab
Ctrl+Alt+C

Split right
Ctrl+Alt+D

Split down
Ctrl+Alt+Shift+D

Zoom
Ctrl+Alt+Z

Next tab
Ctrl+Alt+]

Previous tab
Ctrl+Alt+[
```

Y mantendría los bindings tradicionales de `prefix+...` como backup.

Así tienes:

```text
rápido → Ctrl+Alt+...
compatible → Ctrl+B ...
```

---

# 36. El workflow que recomiendo para desarrollo

Supongamos que tienes:

```text
factura-ai/
```

Crea:

```text
Workspace: factura-ai
```

Y organiza:

```text
Tab: development

┌──────────────────────┬──────────────────────┐
│                      │                      │
│       Claude         │       Backend        │
│                      │                      │
├──────────────────────┼──────────────────────┤
│                      │                      │
│       Frontend       │       Database       │
│                      │                      │
└──────────────────────┴──────────────────────┘
```

Otra tab:

```text
Tab: testing

┌──────────────────────┬──────────────────────┐
│                      │                      │
│       Tests          │       Logs           │
│                      │                      │
└──────────────────────┴──────────────────────┘
```

Otra:

```text
Tab: git

┌──────────────────────────────────────────────┐
│                  lazygit                    │
└──────────────────────────────────────────────┘
```

---

# 37. El verdadero superpoder: múltiples agentes

Aquí es donde Herdr empieza a cambiar radicalmente el workflow.

En lugar de:

```text
tú
 ↓
Claude
 ↓
esperas
 ↓
Claude termina
 ↓
siguiente tarea
```

puedes hacer:

```text
                    ┌── Claude → backend
                    │
                    ├── Codex → tests
                    │
TÚ → HERDR ─────────┼── OpenCode → frontend
                    │
                    ├── Agent → revisión
                    │
                    └── Agent → documentación
```

Cada agente tiene su pane.

Herdr te muestra su estado.

---

# 38. Ejemplo real de trabajo paralelo

Supongamos que quieres implementar una feature.

## Workspace

```text
feature-auth
```

## Agents

```text
Claude
→ implementar backend

Codex
→ crear tests

OpenCode
→ revisar frontend

Claude reviewer
→ revisar cambios
```

Mientras trabajan:

```text
Claude       working
Codex        working
OpenCode     done
Reviewer     blocked
```

Tu atención se dirige directamente al agente que lo necesita.

Esto reduce enormemente el coste mental de supervisar agentes.

---

# 39. Integraciones de agentes

Herdr puede funcionar simplemente detectando lo que aparece en pantalla.

Pero las integraciones son mejores.

Ejemplo:

```bash
herdr integration install claude
```

Comprobar:

```bash
herdr integration status
```

Una integración puede proporcionar:

* estado real del agente
* lifecycle events
* identidad de sesión
* recuperación de sesiones
* información más fiable que analizar únicamente la pantalla

---

# 40. Regla práctica

Si utilizas constantemente un agente dentro de Herdr:

> **Instala su integración si Herdr la soporta.**

Especialmente si quieres aprovechar:

```text
state detection
+
session restore
+
agent orchestration
```

---

# 41. Automatización

Herdr no solo sirve para que TÚ controles terminales.

También permite que:

> **un agente controle Herdr.**

Esto abre una arquitectura muy potente:

```text
Agent A
   │
   ├── crea workspace
   ├── crea pane
   ├── lanza Agent B
   ├── espera
   ├── lee output
   └── toma decisiones
```

Herdr proporciona tres primitivas principales:

```text
Layout
Pane
Agent
```

---

# 42. Layout

Controla:

```text
workspace
tab
pane
```

Sirve para construir la infraestructura donde trabajan los agentes.

---

# 43. Pane API

El pane representa el terminal crudo.

Puede:

```text
split
swap
move
zoom
resize
focus
read
send_text
send_keys
send_input
wait_for_output
close
```

Conceptualmente:

```text
create pane
     ↓
run command
     ↓
wait
     ↓
read output
     ↓
send input
```

---

# 44. Agent API

La abstracción de agente permite trabajar directamente con agentes reconocidos.

Puede:

```text
list
get
read
explain
send_keys
prompt
wait
rename
focus
start
```

Esto es mucho más potente que enviar simplemente caracteres a un terminal.

---

# 45. Diferencia Pane vs Agent

Importantísimo:

```text
Pane
```

es:

> "El terminal."

Mientras:

```text
Agent
```

es:

> "El agente reconocido que está dentro de ese terminal."

Por eso:

```text
pane exists
```

aunque no haya agente.

Pero:

```text
agent
```

necesita existir dentro de un pane.

---

# 46. Ejemplo de arquitectura multiagente

```text
Workspace: software-project

Tab: orchestration

┌─────────────────────────────────────┐
│ Orchestrator Agent                  │
│                                     │
│ Decide qué hacer                    │
└──────────────────┬──────────────────┘
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
       Backend   Frontend   Tests
       Agent     Agent      Agent
```

El agente orquestador podría:

1. crear panes
2. lanzar agentes
3. asignar tareas
4. esperar resultados
5. leer outputs
6. detectar bloqueos
7. pedir revisiones
8. coordinar el trabajo

---

# 47. SSH

Puedes ejecutar Herdr directamente en el servidor:

```bash
ssh you@server
herdr
```

La arquitectura sería:

```text
Tu ordenador
     │
     │ SSH
     ▼
Servidor
     │
     └── Herdr
          ├── Agent
          ├── Agent
          └── Agent
```

Al desconectarte:

```text
SSH cerrado
     ↓
Herdr sigue vivo
     ↓
agentes siguen trabajando
```

---

# 48. Herdr desde el móvil

No necesitas una aplicación específica de Herdr.

Puedes:

```text
móvil
  ↓
SSH
  ↓
servidor
  ↓
Herdr
```

Esto es especialmente útil para:

* comprobar agentes
* responder preguntas
* revisar estados
* lanzar tareas
* comprobar servidores

---

# 49. Herdr --remote

También puedes hacer:

```bash
herdr --remote workbox
```

o:

```bash
herdr --remote ssh://you@server:2222
```

Esto convierte tu Herdr local en un **thin client** del Herdr remoto.

Conceptualmente:

```text
Tu ordenador
┌──────────────────────┐
│ Herdr client         │
└──────────┬───────────┘
           │ SSH
           ▼
┌──────────────────────┐
│ servidor             │
│                      │
│ Herdr server         │
│ ├── Claude            │
│ ├── Codex             │
│ └── Backend           │
└──────────────────────┘
```

---

# 50. Cuándo usar cada modalidad

### Local

```bash
herdr
```

Para desarrollo normal.

### SSH tradicional

```bash
ssh user@server
herdr
```

Para trabajar directamente dentro del servidor.

### Thin client

```bash
herdr --remote server
```

Cuando quieres que el entorno remoto se sienta como si estuviera local.

---

# 51. Named sessions

Puedes tener:

```bash
herdr session list
```

Crear/usar sesiones independientes:

```bash
herdr session attach work
herdr session attach personal
```

Y detener una:

```bash
herdr session stop work
```

Eliminarla:

```bash
herdr session delete side-project
```

---

# 52. Workspaces vs Sessions

No confundas:

```text
Workspace
```

con:

```text
Session
```

Piensa:

```text
SESSION
└── Workspace
    ├── Tab
    │   ├── Pane
    │   └── Pane
    └── Tab
        └── Pane
```

### Workspace

Separación lógica de proyectos.

### Session

Separación completa del runtime.

Regla:

> **Workspace primero. Session solo cuando necesites aislamiento real.**

---

# 53. Git Worktrees

Herdr también integra worktrees.

Puedes trabajar con diferentes ramas simultáneamente.

Conceptualmente:

```text
Workspace A
→ main

Workspace B
→ feature/login

Workspace C
→ feature/payments
```

Esto es especialmente interesante con agentes.

Puedes dar:

```text
Agent A → feature/login
Agent B → feature/payments
Agent C → tests/refactor
```

sin mezclar sus working trees.

---

# 54. Patrón brutalmente productivo: Agent + Worktree

Para tareas paralelas:

```text
repo
│
├── worktree-main
│
├── worktree-feature-A
│      └── Agent A
│
├── worktree-feature-B
│      └── Agent B
│
└── worktree-tests
       └── Agent C
```

Así reduces conflictos entre agentes.

---

# 55. Configuración visual

Herdr permite configurar:

```text
theme
sidebar
notifications
scrollback
terminal
mouse
```

La configuración está en:

```text
~/.config/herdr/config.toml
```

---

# 56. Sidebar

El sidebar es mucho más que una lista de terminales.

Puede mostrar información como:

```text
workspace
branch
git status
agent
state
terminal title
```

Por ejemplo:

```text
● factura-ai
  feature/extraction
  Claude · working

● backend
  main
  Codex · done

! frontend
  feature/ui
  Claude · blocked
```

---

# 57. La filosofía correcta del sidebar

No lo uses simplemente como:

> "lista de terminales".

Úsalo como:

> **dashboard de tu trabajo.**

La pregunta que debería responderte es:

> "¿Dónde tengo que prestar atención ahora mismo?"

---

# 58. Notificaciones

Una buena configuración de Herdr debería ayudarte a saber:

```text
agente bloqueado
agente terminado
evento importante
```

La idea es minimizar:

```text
polling manual
```

y maximizar:

```text
attention routing
```

---

# 59. El workflow definitivo

Una sesión de desarrollo madura podría parecerse a esto:

```text
Herdr
│
├── Workspace: proyecto-A
│   │
│   ├── Tab: development
│   │   ├── Claude
│   │   ├── Backend
│   │   ├── Frontend
│   │   └── Database
│   │
│   ├── Tab: testing
│   │   ├── Tests
│   │   └── Logs
│   │
│   └── Tab: git
│       └── Lazygit
│
├── Workspace: proyecto-B
│   │
│   ├── Tab: agents
│   │   ├── Codex
│   │   └── Claude
│   │
│   └── Tab: monitoring
│
└── Workspace: infrastructure
    ├── Docker
    └── Logs
```

---

# 60. Rutina diaria recomendada

## Al empezar

```bash
herdr
```

Comprueba el sidebar.

Pregunta:

```text
¿Qué agentes están working?
¿Qué agentes están blocked?
¿Qué agentes están done?
```

No abras terminales nuevas innecesariamente.

---

## Durante el trabajo

Usa:

```text
Ctrl+Alt+H/J/K/L
```

para moverte rápidamente.

Usa:

```text
Ctrl+Alt+C
```

para crear tabs.

Usa splits para procesos relacionados.

---

## Cuando un agente necesita atención

Ve directamente al pane marcado como:

```text
blocked
```

No inspecciones todos los agentes.

---

## Cuando termines

No mates los procesos.

Simplemente:

```text
Ctrl+B Q
```

y vete.

---

## Cuando vuelvas

```bash
herdr
```

y continúa.

---

# 61. Principio de productividad nº1

## No uses una terminal por proceso.

Usa:

```text
un workspace por proyecto
un tab por contexto
un pane por proceso
```

---

# 62. Principio de productividad nº2

## No supervises agentes manualmente.

En lugar de:

```text
mirar Claude
mirar Codex
mirar Claude
mirar Codex
```

usa:

```text
Herdr sidebar
       ↓
estado
       ↓
solo intervienes cuando hace falta
```

---

# 63. Principio de productividad nº3

## Los agentes trabajan en paralelo.

No pienses:

```text
"Voy a pedirle algo a Claude."
```

Piensa:

```text
"¿Qué tareas puedo ejecutar simultáneamente?"
```

Ejemplo:

```text
Claude → implementación
Codex → tests
OpenCode → revisión
```

---

# 64. Principio de productividad nº4

## Separa contexto.

No mezcles:

```text
backend
frontend
tests
logs
agent
git
```

en una única terminal.

Usa tabs/panes.

Esto reduce muchísimo el cambio de contexto.

---

# 65. Principio de productividad nº5

## Utiliza worktrees para paralelismo real.

Si dos agentes van a modificar el código simultáneamente:

```text
Agent A → worktree A
Agent B → worktree B
```

mejor que:

```text
Agent A ─┐
         ├── mismo working tree
Agent B ─┘
```

---

# 66. Principio de productividad nº6

## Automatiza lo repetitivo.

Cuando algo pueda expresarse como:

```text
crear pane
ejecutar comando
esperar
leer resultado
```

considera automatizarlo mediante Herdr.

---

# 67. Principio de productividad nº7

## Deja de pensar en "terminales".

Piensa en:

```text
entornos de trabajo persistentes
```

Tu ordenador pasa de ser:

```text
terminales independientes
```

a ser:

```text
sistema operativo para agentes de desarrollo
```

---

# 68. CLI mental que debes conocer

No necesitas memorizar todos los comandos, pero sí saber que Herdr tiene CLI para:

```text
server
session
workspace
worktree
tab
pane
agent
integration
plugin
```

Cuando dudes:

```bash
herdr --help
```

y para obtener la configuración completa:

```bash
herdr --default-config
```

---

# 69. Diagnóstico

Si algo falla:

```bash
herdr status
```

Servidor:

```bash
herdr status server
```

Cliente:

```bash
herdr status client
```

---

# 70. Si un agente no se detecta

Primero:

```bash
herdr agent list
```

Después:

```bash
herdr agent explain <target> --json
```

Esto permite entender por qué Herdr ha clasificado un pane de determinada forma.

Si existe integración:

```bash
herdr integration status
```

e instala la correspondiente.

---

# 71. Si un shortcut no funciona

No asumas que Herdr está roto.

Hay tres capas:

```text
Sistema operativo
      ↓
Terminal
      ↓
Herdr
      ↓
Programa dentro del pane
```

El shortcut puede ser consumido antes de llegar a Herdr.

Ejemplo:

```text
Ctrl+Alt+T
```

puede abrir una terminal del sistema en vez de llegar a Herdr.

Solución:

1. comprobar qué programa consume el shortcut
2. liberar la combinación
3. cambiar el binding de Herdr

---

# 72. Si necesitas más información

Logs:

```text
~/.config/herdr/herdr.log
~/.config/herdr/herdr-client.log
~/.config/herdr/herdr-server.log
```

---

# 73. Plugins

Herdr puede extenderse mediante plugins.

Un plugin puede proporcionar:

* acciones
* hooks
* panes
* workflows
* integración con eventos

La idea:

```text
Herdr
  │
  ├── core
  │
  └── plugins
       ├── plugin A
       ├── plugin B
       └── plugin C
```

Esto convierte Herdr en una plataforma extensible.

---

# 74. API / Socket

Herdr dispone de una API local mediante socket.

Permite controlar:

```text
sessions
workspaces
tabs
panes
agents
events
integrations
plugins
layouts
```

Ejemplos conceptuales:

```text
workspace.create
workspace.list
workspace.focus

tab.create
tab.list
tab.focus

pane.split
pane.read
pane.send_text
pane.send_keys
pane.wait_for_output

agent.list
agent.read
agent.prompt
agent.wait
agent.focus
```

---

# 75. Eventos

La API permite trabajar de forma reactiva.

En vez de:

```text
cada 2 segundos:
    comprobar si terminó Claude
```

puedes pensar:

```text
evento
 ↓
agent done
 ↓
ejecutar siguiente acción
```

Esto es fundamental para construir automatizaciones inteligentes.

---

# 76. Arquitectura avanzada de automatización

Un sistema podría ser:

```text
                    ┌───────────────┐
                    │ Orchestrator  │
                    └───────┬───────┘
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
          Agent A        Agent B        Agent C
          Backend        Frontend       Tests
             │              │              │
             └──────────────┼──────────────┘
                            ▼
                       Reviewer
                            │
                            ▼
                         Merge
```

Herdr proporciona la infraestructura para mantener esos agentes vivos y comunicarse con ellos.

---

# 77. Tu stack ideal

Para desarrollo moderno con IA:

```text
                 HERDR
                   │
       ┌───────────┼───────────┐
       │           │           │
     Claude      Codex      OpenCode
       │           │           │
       └───────────┼───────────┘
                   │
                Git
                   │
              Worktrees
                   │
          Docker / backend
                   │
               Database
```

---

# 78. Herdr + Git + Worktrees + IA

Esta combinación es probablemente una de las más potentes:

```text
                    Git repo
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
      worktree-A   worktree-B   worktree-C
          │            │            │
       Claude        Codex        Claude
          │            │            │
       feature-A    feature-B    tests
```

Cada agente trabaja aislado.

Herdr mantiene todo persistente y visible.

---

# 79. Herdr vs terminal normal

| Terminal normal              | Herdr                         |
| ---------------------------- | ----------------------------- |
| Procesos ligados al terminal | Procesos persistentes         |
| Una terminal                 | Muchos panes                  |
| Muchas ventanas              | Workspaces                    |
| Tabs limitadas               | Tabs organizadas por contexto |
| Supervisión manual           | Estado de agentes             |
| Difícil paralelismo          | Multiagente                   |
| SSH básico                   | SSH persistente               |
| Sin orquestación             | CLI/API                       |
| Sin modelo de agentes        | Agentes como entidad          |

---

# 80. Herdr vs tmux

Si vienes de tmux:

```text
tmux
↓
multiplexor de terminal

Herdr
↓
multiplexor
+
persistencia
+
agentes
+
detección de estado
+
integraciones
+
API
+
automatización
+
plugins
```

La ergonomía de teclado resulta familiar porque Herdr utiliza el modelo de prefix.

---

# 81. La filosofía que debes adoptar

No pienses:

> "Herdr es un programa que organiza mis terminales."

Piensa:

> **"Herdr es el entorno donde viven mis agentes y procesos de desarrollo."**

Esa diferencia mental es importante.

---

# 82. Cheat sheet definitiva

## Herdr

```bash
herdr
```

Reattach/start.

```bash
herdr server stop
```

Parar sesión/servidor.

```bash
herdr --help
```

Ayuda.

```bash
herdr --default-config
```

Configuración completa.

```bash
herdr status
```

Estado.

---

## Prefix

```text
Ctrl+B
```

---

## Navegación

```text
Ctrl+B H   izquierda
Ctrl+B J   abajo
Ctrl+B K   arriba
Ctrl+B L   derecha
```

---

## Panes

```text
Ctrl+B V       split derecha
Ctrl+B -       split abajo
Ctrl+B Z       zoom
Ctrl+B X       cerrar pane
Ctrl+B R       resize
```

---

## Tabs

```text
Ctrl+B C       nueva
Ctrl+B N       siguiente
Ctrl+B P       anterior
Ctrl+B 1..9    seleccionar
Ctrl+B Shift+T renombrar
Ctrl+B Shift+X cerrar
```

---

## Workspaces

```text
Ctrl+B W        navegar
Ctrl+B Shift+N  nuevo
Ctrl+B Shift+W  renombrar
Ctrl+B Shift+D  cerrar
Ctrl+B G        picker
Ctrl+B B        sidebar
```

---

## Sesión

```text
Ctrl+B Q
```

Detach.

---

## Ayuda

```text
Ctrl+B ?
```

---

## Copy mode

```text
Ctrl+B [
```

---

# 83. Cheat sheet mental

Si solo recuerdas una cosa:

```text
C = Create tab
V = Vertical split
- = Horizontal split
H/J/K/L = Move
Z = Zoom
N/P = Next/Previous tab
W = Workspace
Q = Quit/Detach
? = Help
[ = Copy mode
```

---

# 84. Plan para dominar Herdr en 7 días

## Día 1 — Navegación

Aprende:

```text
C
V
-
H/J/K/L
Q
```

Objetivo:

> Poder trabajar sin tocar el ratón para las operaciones básicas.

---

## Día 2 — Organización

Aprende:

```text
tabs
workspaces
rename
sidebar
```

Objetivo:

> Que cada proyecto tenga una estructura clara.

---

## Día 3 — Copy mode

Aprende:

```text
Ctrl+B [
/
?
n
N
v
y
```

Objetivo:

> Poder inspeccionar y copiar terminales sin usar el ratón.

---

## Día 4 — Agentes

Monta:

```text
Claude
Codex
otro agente
```

en panes independientes.

Observa:

```text
working
blocked
done
idle
```

---

## Día 5 — Persistencia

Haz la prueba:

```text
lanza agente
↓
Ctrl+B Q
↓
cierra terminal
↓
espera
↓
herdr
```

Comprueba que sigue vivo.

---

## Día 6 — Worktrees

Crea varios worktrees y asigna:

```text
Agent A → branch A
Agent B → branch B
```

---

## Día 7 — Automatización

Empieza a investigar:

```text
herdr agent
herdr pane
socket API
integrations
plugins
```

Objetivo:

> Que tus agentes puedan empezar a controlar parte del entorno por sí mismos.

---

# 85. Nivel maestro

Cuando ya domines todo lo anterior, tu objetivo final debería ser pasar de:

```text
Yo
 ↓
terminal
 ↓
agente
 ↓
esperar
```

a:

```text
                    ┌── Agent A
                    │
                    ├── Agent B
YO → HERDR →───────┼── Agent C
                    │
                    ├── Tests
                    │
                    └── Reviewer
                         │
                         ▼
                       Git
```

Tú dejas de ser el operador que mueve terminales constantemente y pasas a ser:

> **el supervisor del sistema de agentes.**

Ese es el verdadero potencial de Herdr.

---

# 86. Las 10 cosas que debes recordar siempre

1. **`herdr` es persistente.**
2. **`Ctrl+B` es el prefix por defecto.**
3. **H/J/K/L = navegar panes.**
4. **C = nueva tab.**
5. **V y `-` = splits.**
6. **W = workspaces.**
7. **Q = detach, no apagar.**
8. **El sidebar es tu dashboard de agentes.**
9. **Usa worktrees para paralelizar agentes que modifican código.**
10. **Cuando controles panes + agentes + API, Herdr deja de ser un multiplexor y se convierte en una plataforma de automatización.**

---

# 87. Configuración de referencia

Archivo:

```text
~/.config/herdr/config.toml
```

Ejemplo inicial:

```toml
[keys]

prefix = "ctrl+b"

# Navegación rápida
focus_pane_left = ["prefix+h", "ctrl+alt+h"]
focus_pane_down = ["prefix+j", "ctrl+alt+j"]
focus_pane_up = ["prefix+k", "ctrl+alt+k"]
focus_pane_right = ["prefix+l", "ctrl+alt+l"]

# Tabs
new_tab = ["prefix+c", "ctrl+alt+c"]
next_tab = ["prefix+n", "ctrl+alt+]"]
previous_tab = ["prefix+p", "ctrl+alt+["]

# Panes
split_vertical = ["prefix+v", "ctrl+alt+d"]
split_horizontal = ["prefix+minus", "ctrl+alt+shift+d"]
zoom = ["prefix+z", "ctrl+alt+z"]
```

**Importante:** antes de adoptar bindings directos, comprueba que no los consume tu terminal o tu escritorio.

---

# 88. Regla de oro

> **No intentes aprender Herdr entero.**
>
> Aprende primero a moverte.
>
> Después organiza tus proyectos.
>
> Después introduce agentes.
>
> Después paraleliza con worktrees.
>
> Finalmente automatiza Herdr mediante CLI/API.

El salto de productividad no viene de saber 100 shortcuts.

Viene de conseguir que:

```text
tú
 ↓
decides qué hay que hacer

Herdr
 ↓
mantiene el entorno

agentes
 ↓
ejecutan el trabajo

sidebar
 ↓
te dice dónde intervenir

Git/worktrees
 ↓
mantienen el trabajo aislado
```

Ese es el modelo mental que realmente merece la pena dominar.
