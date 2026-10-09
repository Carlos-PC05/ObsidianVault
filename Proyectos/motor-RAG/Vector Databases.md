---
aliases:
  - vector databases
tags:
  - ai/embeddings
  - ai/vector-databases
  - motor-RAG
source: "[[Vector Databases for Embeddings with Pinecone]]"
---
### Qué es Pinecone?
Pinecone es un servicio de **vector database** gestionado (serverless) al que accedemos mediante el cliente Python de Pinecone.

**Index**: estructura que almacena los *records* (vectores + metadata asociada) y sirve consultas y otras manipulaciones de vectores. Puedes crear múltiples índices.

**Dos tipos de infraestructura**:
- **Pod-Based**: eliges el hardware (pods); el tipo de pod determina almacenamiento, latencia y throughput de las queries.
- **Serverless** (el que usa el curso): sin gestión de recursos, los índices escalan automáticamente, corre en la nube y almacena en blob. Más fácil de usar y suele costar menos.

### Preparación
1. Crear una cuenta **Pinecone Starter** (gratis) en https://www.pinecone.io/
2. Ir a la página **API Keys** y crear tu key

```python
# Import the Pinecone library
from pinecone import Pinecone

# Initialize the Pinecone client
pc = Pinecone(api_key="<PINECONE_TOKEN>")
```

### Crear un índice serverless
```python
from pinecone import Pinecone, ServerlessSpec

pc = Pinecone(api_key="API_KEY")

pc.create_index(
    name='datacamp-index',
    dimension=1536,
    spec=ServerlessSpec(
        cloud='aws',
        region='us-east-1'
    )
)
```
- `dimension` debe coincidir con la dimensionalidad de las embeddings que se van a ingerir (p.ej. 1536 para `text-embedding-3-small`).

### Comprobar los índices
```python
pc.list_indexes()
# {'indexes': [{'dimension': 1536, 'metric': 'cosine', 'name': 'datacamp-index', ...}]}
```

### Conectarse a un índice
```python
index = pc.Index('datacamp-index')
```
- Si el índice no existe se lanza un error `NotFoundException (404)`.
- El objeto `index` tiene métodos para ingerir, manipular y explorar el contenido del índice.

### Estadísticas del índice
```python
index.describe_index_stats()
# {'dimension': 1536, 'index_fullness': 0.0, 'namespaces': {}, 'total_vector_count': 0}
```

### Namespaces
**Contenedores para particionar índices**:
- Separar datasets
- Data versioning
- Separar grupos de usuarios

### Organizaciones
- Permisos a nivel de organización entera
- Gestionar billing, usuarios y todos los proyectos
- Los usuarios son invitados a proyectos específicos, donde pueden convertirse en owners

### Borrar índices
```python
pc.delete_index('datacamp-index')
pc.list_indexes()
# {'indexes': []}
```
> **Warning**: borrar un índice también borra todos los datos que contiene.

**Ver ejemplos prácticos aquí ->** [[Apuntes/AI/Datacamp/Vector Databases for Embeddings/chapter1.pdf]]

## Ingesta de vectores (Vector ingestion)

### Estructura de los vectores
Lista de dicts, cada uno con:
```python
vectors = [
    {
        "id": "0",
        "values": [0.025525547564029694, ..., 0.0188823901116848],
        "metadata": {"genre": "action", "year": 2024}
    },
    ...
]
```
- `metadata`: *data about data*, filtrable en las queries (capítulo 2)

### Comprobar la dimensionalidad
Antes de ingerir hay que verificar que los vectores son compatibles con la dimensionalidad del índice:
```python
vector_dims = [len(vector['values']) == 1536 for vector in vectors]
print(all(vector_dims))
# True
```
- Si no coinciden se lanza un error: `"Vector dimension 256 does not match the dimension of the index 1536"`

### Upsertear vectores
`.upsert()`: update **o** insert (si el ID existe lo actualiza, si no lo crea).
```python
index.upsert(
    vectors=vectors
)
index.describe_index_stats()
# {'namespaces': {'': {'vector_count': 10}}, 'total_vector_count': 10}
```

## Recuperar y consultar vectores

### Fetching vs Querying
Dos formas principales de acceder a vectores:
- **Fetching**: recuperar vectores por sus IDs
- **Querying**: recuperar los vectores más similares a un vector de entrada

### Fetching
```python
index.fetch(
    ids=['0', '1']
)
# {'vectors': {'0': {'id': '0', 'metadata': {...}, 'values': [...]}, ...}}
```
- **Read units (RU)**: medida de los recursos consumidos en operaciones de lectura. Fetching → 1 RU / 10 records.
- Se puede especificar un namespace: `index.fetch(ids=[...], namespace='namespace1')`

### Querying
```python
query_result = index.query(
    vector=vector,
    top_k=3
)
```
- Devuelve `matches` con los `id`, `score` y `values` de los vectores más similares.
- `include_values=True` → incluye los valores de los vectores devueltos.
- `include_metadata=True` → incluye la metadata de los vectores devueltos.
- Los RUs de una query dependen de: nº de records del namespace, tamaño de los records, dimensionalidad de los vectores y cantidad de metadata.

### Distance metrics
- **cosine** (por defecto): medida de similitud por ángulo
- **euclidean**: distancia euclidiana
- **dotproduct**: producto punto

Se eligen al crear el índice:
```python
pc.create_index(
    name="dotproduct-index",
    dimension=1536,
    metric='dotproduct',
    spec=ServerlessSpec(cloud='aws', region='us-east-1')
)
```

## Metadata filtering
La metadata puede ser **strings, números, booleans y listas de strings**. El filtrado por metadata **reduce el espacio de búsqueda y la latencia** de las queries.

### Filtrar queries
```python
query_result = index.query(
    vector=vector,
    top_k=1,
    filter={
        "year": 2024
    }
)
```

### Operadores de filtrado
- `$eq` - igual a (number, string, boolean)
- `$ne` - distinto de (number, string, boolean)
- `$gt` - mayor que (number)
- `$gte` - mayor o igual que (number)
- `$lt` - menor que (number)
- `$lte` - menor o igual que (number)
- `$in` - dentro de un array (string o number)
- `$nin` - fuera de un array (string o number)

### Múltiples filtros
```python
query_result = index.query(
    vector=vector,
    top_k=1,
    filter={
        "genre": "thriller",
        "year": {"$lt": 2018}
    }
)
```

**Ver ejemplos prácticos aquí ->** [[Apuntes/AI/Datacamp/Vector Databases for Embeddings/chapter2.pdf]]

## Actualizar y borrar vectores
Mantener los datos actualizados: dato actual, rendimiento de queries optimizado, integridad de los datos.

### Actualizar valores
```python
index.update(
    id="7",
    values=vector
)
```
- Asegurar que la longitud de la lista = dimensionalidad del índice.

### Actualizar metadata
```python
index.update(
    id="7",
    set_metadata={
        "genre": "thriller",
        "year": 2024
    }
)
```
- La metadata existente se conserva: se añaden/sobrescriben solo los campos indicados.

### Actualizar valores y metadata a la vez
```python
index.update(
    id="7",
    values=vector,
    set_metadata={"genre": "thriller", "ratings": 4}
)
```

### Borrar vectores
```python
# Por IDs
index.delete(ids=["3", "4"])

# Por metadata
index.delete(filter={"genre": {"$eq": "action"}})

# Todo un namespace (¡también elimina el namespace!)
index.delete(delete_all=True, namespace='namespace1')
```
- Borrar vectores innecesarios optimiza el rendimiento: evita saturar el almacenamiento y acelera las queries.

**Ver código de los ejercicios aquí ->** [[Vector Databases for Embeddings with Pinecone]]

## Performance tuning y aplicaciones AI

### Batching de upserts
Función auxiliar para partir una lista de vectores en chunks:
```python
def chunks(iterable, batch_size=100):
    """A helper function to break an iterable into chunks of size batch_size."""
    it = iter(iterable)
    chunk = tuple(itertools.islice(it, batch_size))
    while chunk:
        yield chunk
        chunk = tuple(itertools.islice(it, batch_size))
```

**En serie** (lote a lote):
```python
for chunk in chunks(vectors):
    index.upsert(vectors=chunk)
```

**En paralelo** (asíncrono con pool de threads):
```python
pc = Pinecone(api_key="API_KEY", pool_threads=20)
index = pc.Index('datacamp-index')

with pc.Index('datacamp-index', pool_threads=20) as index:
    async_results = [index.upsert(vectors=chunk, async_req=True) for chunk in chunks(vectors, batch_size=200)]
    [async_result.get() for async_result in async_results]
```

### Multitenancy con namespaces
Los namespaces separan vectores dentro de un único índice, permitiendo queries dirigidas y minimizando el número de records escaneados.
```python
index.upsert(vectors=vector_set1, namespace="namespace1")
index.upsert(vectors=vector_set2, namespace="namespace2")

# Query dirigida a un solo namespace
query_result = index.query(
    vector=vector,
    namespace="namespace1",
    top_k=3
)
```

## Búsqueda semántica con Pinecone
1. Crear el índice (`'pinecone-datacamp'`, dimension 1536)
2. Embeber el texto con OpenAI y hacer upsert (en batches) con metadatos
```python
batch_limit = 100

for batch in np.array_split(df, len(df) / batch_limit):
    metadatas = [{
      "text_id": row['id'],
      "text": row['text'],
      "title": row['title']} for _, row in batch.iterrows()]
    texts = batch['text'].tolist()

    ids = [str(uuid4()) for _ in range(len(texts))]

    # Encode texts using OpenAI
    response = client.embeddings.create(input=texts, model="text-embedding-3-small")
    embeds = [np.array(x.embedding) for x in response.data]

    # Upsert vectors to the correct namespace
    index.upsert(vectors=zip(ids, embeds, metadatas), namespace='squad_dataset')
```
3. Embeber la query y recuperar los vectores más similares
```python
query_response = client.embeddings.create(input=query, model="text-embedding-3-small")
query_emb = query_response.data[0].embedding

retrieved_docs = index.query(
    vector=query_emb,
    top_k=5,
    namespace="squad_dataset")

for result in retrieved_docs['matches']:
    print(f"{result['id']}: {round(result['score'], 2)}")
```

## Chatbot RAG con Pinecone y OpenAI
Flujo completo de **Retrieval Augmented Generation (RAG)** con transcripciones de YouTube y metadatos (`text_id`, `title`, `url`, `published`).

1. **Upsert de transcripciones** en el índice (namespace `youtube_rag_dataset`), igual que en la búsqueda semántica.

2. **Función de retrieval** (pieza clave del flujo RAG):
```python
def retrieve(query, top_k, namespace, emb_model):
    query_response = client.embeddings.create(input=query, model=emb_model)
    query_emb = query_response.data[0].embedding

    docs = index.query(vector=query_emb, top_k=top_k, namespace=namespace, include_metadata=True)

    retrieved_docs = []
    sources = []
    for doc in docs['matches']:
        retrieved_docs.append(doc['metadata']['text'])
        sources.append((doc['metadata']['title'], doc['metadata']['url']))

    return retrieved_docs, sources
```

3. **Construir el prompt con contexto**:
```python
def prompt_with_context_builder(query, docs):
    delim = '\n\n---\n\n'
    prompt_start = 'Answer the question based on the context below.\n\nContext:\n'
    prompt_end = f'\n\nQuestion: {query}\nAnswer:'

    prompt = prompt_start + delim.join(docs) + prompt_end
    return prompt
```

4. **Responder con un LLM** (`gpt-4o-mini`) e incluir las fuentes:
```python
def question_answering(prompt, sources, chat_model):
    sys_prompt = "You are a helpful assistant that always answers questions."

    res = client.chat.completions.create(
        model=chat_model,
        messages=[
            {"role": "system", "content": sys_prompt},
            {"role": "user", "content": prompt}
        ],
        temperature=0
    )
    answer = res.choices[0].message.content.strip()
    answer += "\n\nSources:"
    for source in sources:
        answer += "\n" + source[0] + ": " + source[1]

    return answer
```