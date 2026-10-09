# Sincronizar este vault con Git

Se comparten notas, imágenes, PDF y la configuración de Obsidian, incluidos plugins y temas. El estado de ventanas, la papelera, los archivos de Syncthing y las credenciales locales quedan fuera de Git mediante `.gitignore`.

## Primera puesta en marcha

1. Haz una copia de seguridad del vault y conserva cualquier cambio pendiente de los otros ordenadores.
2. Si usas Syncthing para esta carpeta, desactiva esa sincronización en todos los equipos antes de usar Git. Usa un único sistema para sincronizar este vault.
3. Crea un repositorio **privado** vacío en tu proveedor Git, sin README ni otros archivos iniciales.
4. Desde una terminal en este vault:

```bash
git init -b main
git config core.fileMode false
git config pull.ff only
git status --short
```

Revisa los archivos que se incluirán, especialmente las notas de trabajo y los ajustes de plugins. `.gitignore` no detecta contraseñas escritas dentro de notas. Configura `user.name` y `user.email` si Git te lo pide.

```bash
git add .
git diff --cached --stat
git diff --cached
git commit -m "Añadir vault de Obsidian"
```

Añade la URL real del repositorio y sube el primer commit:

```bash
git remote add origin URL_DEL_REPOSITORIO
git push -u origin main
```

## En los otros ordenadores

Instala Git y configura la autenticación de tu proveedor. Clona el repositorio en una carpeta nueva, fuera de carpetas sincronizadas por otras aplicaciones:

```bash
git clone URL_DEL_REPOSITORIO "Obsidian Vault"
cd "Obsidian Vault"
git config core.fileMode false
git config pull.ff only
```

Abre esa carpeta como vault en Obsidian. Si tenías otra copia con cambios exclusivos de ese equipo, consérvala e incorpora esos cambios antes de retirarla. Revisa y habilita los plugins comunitarios si Obsidian lo solicita.

## Rutina diaria

Antes de editar, con el árbol de trabajo limpio:

```bash
git pull --ff-only
```

Al terminar, espera a que Obsidian guarde los cambios y ejecuta:

```bash
git add .
git diff --cached --stat
git commit -m "Actualizar notas"
git push
```

Si no hay cambios, omite el commit y el push. Antes de cambiar de ordenador, comprueba que el push terminó correctamente. Evita editar la misma nota a la vez en dos equipos.

Si el pull o el push falla porque ambos equipos tienen commits nuevos, conserva tus cambios en un commit y ejecuta `git pull --no-rebase`. Si aparecen conflictos, revisa ambas versiones y resuélvelos antes de hacer commit y push. No uses `git reset --hard` ni `git push --force` para sincronizar.

## Mantenerlo limpio

- Las imágenes existentes conservan su ubicación para no romper enlaces. Para ordenar los adjuntos, muévelos desde Obsidian con la actualización automática de enlaces activada.
- El tamaño actual del vault no requiere introducir Git LFS. Evita añadir vídeos o archivos grandes que cambien continuamente: sus versiones hacen crecer el historial.
- Los ajustes de plugins también pueden contener tokens o rutas específicas del equipo. Revisa sus archivos `data.json` antes de publicarlos y excluye los que contengan información local.
- Git sincroniza cuando ejecutas pull y push; no lo hace automáticamente. Mantén una copia de seguridad independiente.
