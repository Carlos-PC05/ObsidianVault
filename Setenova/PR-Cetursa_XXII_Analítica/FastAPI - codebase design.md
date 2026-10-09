---
tags:
  - backend
  - fastapi
  - python
---
Si quieres una estructura **seria, escalable y alineada con las recomendaciones oficiales de FastAPI**, yo no me limitaría a copiar literalmente el ejemplo de la documentación. FastAPI muestra `routers/`, `dependencies.py`, `main.py`, etc., pero para una API que va a crecer merece la pena separar claramente **HTTP → lógica de negocio → acceso a datos**. La propia documentación recomienda dividir las aplicaciones grandes mediante `APIRouter` y módulos separados. ([FastAPI][1])

Para un proyecto con `api/src/`, yo usaría algo así:

```text
api/
├── src/
│   ├── __init__.py
│   │
│   ├── main.py
│   │
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py
│   │   ├── security.py
│   │   └── exceptions.py
│   │
│   ├── db/
│   │   ├── __init__.py
│   │   ├── session.py
│   │   ├── base.py
│   │   └── migrations/
│   │
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── item.py
│   │   └── ...
│   │
│   ├── schemas/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── item.py
│   │   └── ...
│   │
│   ├── routers/
│   │   ├── __init__.py
│   │   ├── users.py
│   │   ├── items.py
│   │   └── auth.py
│   │
│   ├── services/
│   │   ├── __init__.py
│   │   ├── user_service.py
│   │   ├── item_service.py
│   │   └── auth_service.py
│   │
│   ├── repositories/
│   │   ├── __init__.py
│   │   ├── user_repository.py
│   │   ├── item_repository.py
│   │   └── ...
│   │
│   ├── dependencies/
│   │   ├── __init__.py
│   │   ├── auth.py
│   │   └── database.py
│   │
│   └── utils/
│       ├── __init__.py
│       └── ...
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── conftest.py
│
├── alembic.ini
├── pyproject.toml
└── README.md
```

### La idea importante

Yo establecería esta dirección de dependencias:

```text
             HTTP
              │
              ▼
        ┌─────────────┐
        │   routers   │
        └──────┬──────┘
               │
               ▼
        ┌─────────────┐
        │  services   │
        └──────┬──────┘
               │
               ▼
       ┌────────────────┐
       │  repositories  │
       └───────┬────────┘
               │
               ▼
        ┌─────────────┐
        │   database  │
        └─────────────┘
```

Y los modelos/schemas quedan como estructuras compartidas:

```text
models  ───────────────► database
   ▲
   │
repositories

schemas ───────────────► routers
   ▲
   │
services
```

### ¿Qué debería contener cada carpeta?

**`routers/`**

Únicamente debería preocuparse de HTTP:

```python
@router.get("/{user_id}")
async def get_user(user_id: int):
    return user_service.get_user(user_id)
```

Aquí:

* `APIRouter`
* parámetros HTTP
* status codes
* `Depends`
* validación de entrada mediante schemas
* respuestas

FastAPI recomienda precisamente utilizar `APIRouter` para separar grupos de endpoints y posteriormente incluirlos desde `main.py`. ([FastAPI][1])

---

**`services/`**

Aquí pondría **toda la lógica de negocio**.

```python
class UserService:

    def get_user(self, user_id: int):
        user = self.repository.get_by_id(user_id)

        if user is None:
            raise UserNotFoundError(user_id)

        return user
```

Esto hace que puedas reutilizar la lógica desde:

* HTTP
* CLI
* jobs
* workers
* tests

sin depender de FastAPI.

---

**`repositories/`**

Aquí pondría el acceso a la base de datos.

```python
class UserRepository:

    def get_by_id(self, user_id: int):
        return self.session.get(User, user_id)
```

Así evitas que aparezca SQL/ORM por todas partes.

Esto encaja especialmente bien con la filosofía de separar la aplicación de la tecnología de persistencia. FastAPI no impone un ORM concreto; su documentación muestra SQLModel como ejemplo, pero también indica que puedes utilizar SQLAlchemy u otras soluciones. ([FastAPI][2])

---

**`models/`**

Modelos de persistencia:

```python
class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True)
    email = Column(String, unique=True)
```

Representan principalmente **cómo se almacenan los datos**.

---

**`schemas/`**

Modelos Pydantic para la API:

```python
class UserCreate(BaseModel):
    email: EmailStr
    password: str


class UserResponse(BaseModel):
    id: int
    email: EmailStr
```

Es decir:

```text
Model  → cómo se guarda
Schema → cómo entra/sale por la API
```

No mezclaría ambas cosas salvo casos muy sencillos.

---

**`dependencies/`**

Dependencias propias de FastAPI:

```python
async def get_current_user(...):
    ...
```

```python
def get_db(...):
    ...
```

La documentación oficial utiliza precisamente un módulo de `dependencies` para dependencias compartidas. ([FastAPI][1])

---

**`core/`**

Configuración transversal de la aplicación:

```text
core/
├── config.py
├── security.py
└── exceptions.py
```

Por ejemplo:

```python
class Settings(BaseSettings):
    database_url: str
    jwt_secret: str
```

---

### `main.py` debería ser MUY pequeño

Algo de este estilo:

```python
from fastapi import FastAPI

from .routers import auth, users, items

app = FastAPI(
    title="My API",
    version="1.0.0",
)

app.include_router(auth.router)
app.include_router(users.router)
app.include_router(items.router)
```

Y poco más.

La documentación de FastAPI sigue exactamente esta filosofía: `main.py` actúa como punto que conecta la aplicación y los distintos `APIRouter`, mientras que la lógica se encuentra en módulos separados. ([FastAPI][3])

---

### Pero hay un matiz importante para TU proyecto

Por lo que me has contado de tu refactor actual, **yo mantendría esta separación**:

```text
src/
├── main.py
├── routers/
├── services/
├── repositories/
├── models/
├── schemas/
├── db/
├── dependencies/
└── core/
```

y evitaría hacer algo como:

```text
routers/
services/
models/
schemas/
...
```

**por cada feature** desde el principio:

```text
users/
    router.py
    service.py
    model.py
    schema.py

orders/
    router.py
    service.py
    model.py
    schema.py
```

Eso también es válido y puede ser excelente en proyectos enormes, pero para una API que estás refactorizando ahora mismo, la separación por **responsabilidad técnica** es más sencilla de mantener.

En resumen, tu regla podría ser:

> **Router recibe la petición → Service decide qué hacer → Repository habla con BD.**

Y:

> **Schema = contrato de la API. Model = representación de BD.**

Esa arquitectura te deja además una frontera muy clara para que un agente de coding pueda trabajar sin mezclar responsabilidades.

[1]: https://fastapi.tiangolo.com/es/tutorial/bigger-applications/?utm_source=chatgpt.com "Aplicaciones más grandes - Múltiples archivos - FastAPI"
[2]: https://fastapi.tiangolo.com/es/tutorial/sql-databases/?utm_source=chatgpt.com "Bases de Datos SQL (Relacionales) - FastAPI"
[3]: https://fastapi.tiangolo.com/tutorial/bigger-applications/?utm_source=chatgpt.com "Bigger Applications - Multiple Files - FastAPI"
