#Programming 
# Slides Datacamp
[chapter1](attachment:482ad98a-6c4a-48d0-96f7-a03fb1a56f14:chapter1_compressed.pdf)
[chapter2](attachment:9eab32d3-df22-44f4-8e0c-ae868539eccd:chapter2_compressed.pdf)
# Apuntes

### Crear repositorio local

```bash
#Inicializar repo local
git init

#Ver estado
git status

#Añadir archivos
git add .

#hacer commit
git commit -m "Descripción del commit"
```

### Crear repositorio en Github y conectarlo

```bash
git remote add origin <https://github.com/USUARIO/REPO.git>
git branch -M main
git push -u origin main
```

## Flujo Github

```bash
trabajas en tu PC
↓
git commit → guarda checkpoint local
↓
NO se sube a GitHub todavía
↓
git push → recién aquí se sube

#ver en qué rama estás
git branch

#crear y cambiar a nueva branch
git switch -b nombre-rama

#trabajar en la branch
editar código
git add .
git commit -m "descripción nueva rama"

#Subir rama a github
git push -u origin experimento-parser

==#fusionar rama con main==
git checkout main
git pull
git merge nombre-rama

#subir main actualizado
git push

#Borrar rama de experimento
git branch -d nombre-rama --> local
git push origin --delete nombre-rama --> GITHUB
```