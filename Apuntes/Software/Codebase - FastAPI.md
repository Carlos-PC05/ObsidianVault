
```text
src/
├── middleware/
├── db/
├── models/
├── schemas/
└── services/
```

es bastante habitual en APIs, especialmente con **FastAPI + SQLAlchemy/Pydantic**. La clave está en que cada carpeta tenga una responsabilidad clara y que no acabes metiendo lógica de negocio en sitios que no corresponden.

Te lo explico de **abajo hacia arriba**, porque así se entiende mejor.

---

## 1. `db/` → acceso y configuración de la base de datos

Aquí debería estar todo lo relacionado con **cómo la aplicación se conecta y trabaja con la BD**, pero no la lógica de negocio.

Por ejemplo:

```text
db/
├── database.py
├── session.py
└── migrations/
```

Podrías tener:

```python
# db/session.py

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

También:

* configuración del engine
* creación de sesiones
* configuración del pool
* transacciones
* Base declarativa de SQLAlchemy
* migraciones si las gestionas dentro del proyecto

### ❌ Qué NO debería ir aquí

No debería haber cosas como:

```python
def get_user_by_email(email):
    ...
```

si `get_user_by_email` forma parte de la lógica de acceso a usuarios de tu aplicación. Eso suele pertenecer a otra capa (repository/DAO si existe, o service en una arquitectura sencilla).

**Idea mental:**

> `db` responde a: **"¿Cómo hablo con la base de datos?"**

---

# 2. `models/` → cómo se representa la BD

Los **models** representan las entidades persistidas en la base de datos.

Por ejemplo:

```text
models/
├── user.py
├── product.py
└── order.py
```

Un modelo SQLAlchemy podría ser:

```python
class User(Base):
    __tablename__ = "users"

    id = Column(UUID, primary_key=True)
    email = Column(String, unique=True, nullable=False)
    hashed_password = Column(String, nullable=False)
```

Aquí defines cosas como:

* columnas
* tipos
* claves primarias
* foreign keys
* relaciones
* índices
* constraints

Por ejemplo:

```python
class Order(Base):
    __tablename__ = "orders"

    id = Column(UUID, primary_key=True)
    user_id = Column(
        UUID,
        ForeignKey("users.id"),
        nullable=False
    )

    user = relationship("User")
```

### ❌ Qué NO debería hacer un model

No debería decidir cosas como:

```python
if user.is_admin:
    ...
```

ni enviar emails, validar permisos de negocio, etc.

El modelo describe **los datos y sus relaciones**, no el comportamiento de toda la aplicación.

**Idea mental:**

> `models` responde a: **"¿Cómo están representados mis datos en la BD?"**

---

# 3. `schemas/` → qué entra y qué sale de la API

Esta carpeta suele ser especialmente importante en FastAPI.

Los schemas normalmente son modelos **Pydantic** que representan los contratos de la API.

Por ejemplo:

```text
schemas/
├── user.py
├── auth.py
└── order.py
```

Puedes tener:

```python
class UserCreate(BaseModel):
    email: EmailStr
    password: str
    name: str
```

Y:

```python
class UserResponse(BaseModel):
    id: UUID
    email: EmailStr
    name: str
```

Así puedes tener:

```text
HTTP Request
     ↓
UserCreate
     ↓
Service
     ↓
User model
     ↓
Database
```

Y para la respuesta:

```text
Database
     ↓
User model
     ↓
Service
     ↓
UserResponse
     ↓
HTTP Response
```

### Una distinción MUY importante

No confundas:

```text
models.User
```

con:

```text
schemas.UserResponse
```

El primero representa **la entidad en la BD**.

El segundo representa **lo que quieres exponer mediante la API**.

Por ejemplo, tu `User` podría contener:

```python
hashed_password
```

pero tu:

```python
UserResponse
```

jamás debería incluirlo.

---

**Idea mental:**

> `schemas` responde a: **"¿Qué datos puede recibir/devolver mi API?"**

---

# 4. `services/` → lógica de negocio

Esta es probablemente la carpeta **más importante** para mantener una API limpia.

Aquí debería vivir la lógica que responde a:

> **"¿Qué tiene que hacer mi aplicación?"**

Por ejemplo:

```text
services/
├── user_service.py
├── auth_service.py
├── order_service.py
└── payment_service.py
```

Supongamos que registras un usuario.

El service podría hacer:

```python
def create_user(db, data):
    existing_user = ...
    
    if existing_user:
        raise UserAlreadyExists()

    hashed_password = hash_password(data.password)

    user = User(
        email=data.email,
        hashed_password=hashed_password,
        name=data.name
    )

    db.add(user)
    db.commit()
    db.refresh(user)

    return user
```

Aquí sí tiene sentido que estén las reglas:

* comprobar si existe
* hashear contraseña
* comprobar condiciones
* crear entidades
* ejecutar operaciones
* coordinar varias operaciones
* aplicar reglas de negocio
* gestionar transacciones cuando corresponda

---

## 5. `middleware/` → cosas que atraviesan las peticiones

El middleware funciona a un nivel diferente.

Una petición pasa aproximadamente por:

```text
Cliente
   ↓
Middleware
   ↓
Router / Endpoint
   ↓
Service
   ↓
Model / DB
   ↓
Respuesta
   ↓
Middleware
   ↓
Cliente
```

El middleware es apropiado para funcionalidades **transversales**, que afectan a muchas o todas las peticiones.

Por ejemplo:

```text
middleware/
├── logging.py
├── cors.py
├── request_id.py
└── timing.py
```

Ejemplos:

### Logging

```text
GET /users
request_id=abc123
→ 200
→ 43 ms
```

### Request ID

Generar:

```text
X-Request-ID: abc123
```

y mantenerlo durante toda la petición.

### CORS

Controlar qué orígenes pueden llamar a la API.

### Medición

Medir:

```text
request → 125 ms
```

---

# ¿Y dónde están los endpoints?

Me llama la atención que no tengas una carpeta como:

```text
routers/
```

o:

```text
routes/
```

Si realmente tu estructura solo es:

```text
src/
├── middleware
├── db
├── models
├── schemas
└── services
```

yo consideraría añadir:

```text
routers/
```

Porque **los endpoints HTTP deberían tener su propia capa**.

Por ejemplo:

```text
src/
├── middleware/
├── db/
├── models/
├── schemas/
├── services/
└── routers/
    ├── auth.py
    ├── users.py
    └── orders.py
```

Entonces tendrías una separación muy limpia.

---

# El flujo completo

Por ejemplo:

```http
POST /users
```

con:

```json
{
    "email": "carlos@example.com",
    "password": "123456"
}
```

El flujo ideal sería:

```text
                 HTTP
                  │
                  ▼
          ┌───────────────┐
          │   middleware  │
          └───────┬───────┘
                  │
                  ▼
          ┌───────────────┐
          │    router     │
          │ POST /users   │
          └───────┬───────┘
                  │
                  │ UserCreate
                  ▼
          ┌───────────────┐
          │    service    │
          │ create_user() │
          └───────┬───────┘
                  │
                  ▼
          ┌───────────────┐
          │     model     │
          │     User      │
          └───────┬───────┘
                  │
                  ▼
          ┌───────────────┐
          │      db       │
          └───────┬───────┘
                  │
                  ▼
              PostgreSQL
```

Y la respuesta vuelve:

```text
PostgreSQL
    ↓
User model
    ↓
Service
    ↓
UserResponse schema
    ↓
Router
    ↓
HTTP 201
```

---

# Un ejemplo real de cómo repartir código

Supongamos que tienes:

```http
POST /auth/login
```

### `schemas/auth.py`

```python
class LoginRequest(BaseModel):
    email: EmailStr
    password: str


class LoginResponse(BaseModel):
    access_token: str
    token_type: str
```

### `models/user.py`

```python
class User(Base):
    __tablename__ = "users"

    id = ...
    email = ...
    hashed_password = ...
```

### `services/auth_service.py`

```python
def authenticate_user(db, email, password):
    user = ...

    if not user:
        return None

    if not verify_password(password, user.hashed_password):
        return None

    return user
```

### `routers/auth.py`

```python
@router.post("/login")
def login(
    data: LoginRequest,
    db: DbSession,
) -> LoginResponse:

    user = authenticate_user(
        db,
        data.email,
        data.password
    )

    if not user:
        raise HTTPException(401)

    token = create_access_token(user)

    return LoginResponse(
        access_token=token,
        token_type="bearer"
    )
```

Fíjate en que el router queda **muy fino**.

No debería tener 100 líneas de lógica.

Su trabajo principal es:

```text
HTTP
 ↓
validar entrada
 ↓
llamar service
 ↓
convertir resultado a respuesta
```

---

# Una regla que te va a servir muchísimo

Cuando no sepas dónde poner algo, piensa:

| Pregunta                                          | Carpeta      |
| ------------------------------------------------- | ------------ |
| ¿Cómo me conecto a PostgreSQL?                    | `db`         |
| ¿Cómo es la tabla `users`?                        | `models`     |
| ¿Qué JSON acepto en `POST /users`?                | `schemas`    |
| ¿Qué JSON devuelvo?                               | `schemas`    |
| ¿Qué ocurre cuando registro un usuario?           | `services`   |
| ¿Qué ocurre cuando autentico un usuario?          | `services`   |
| ¿Cómo defino `POST /users`?                       | `routers`    |
| ¿Cómo añado un request ID a todas las peticiones? | `middleware` |
| ¿Cómo registro cuánto tarda cada request?         | `middleware` |

---

## Y hay una capa que probablemente te falta

Si tu API empieza a crecer, probablemente terminarás queriendo:

```text
src/
├── middleware/
├── db/
├── models/
├── schemas/
├── services/
├── repositories/
└── routers/
```

`repositories/` se encargaría específicamente del **acceso a datos**:

```python
def get_user_by_email(db, email):
    return db.query(User).filter(
        User.email == email
    ).first()
```

Entonces la separación sería todavía más clara:

```text
router
   ↓
service
   ↓
repository
   ↓
model
   ↓
database
```

El **service decide qué hay que hacer** y el **repository sabe cómo sacar/guardar datos**.

No obstante, **no metería repositories por obligación**. Para una API pequeña pueden añadir abstracción innecesaria. Cuando empiezas a tener consultas complejas o muchos services accediendo a las mismas entidades, empiezan a tener bastante sentido.

La idea fundamental es que **no hay que perseguir una arquitectura con 15 carpetas porque "es buena práctica"**. Lo importante es que puedas mirar una pieza de código y saber rápidamente **qué responsabilidad tiene y dónde debería estar**.
