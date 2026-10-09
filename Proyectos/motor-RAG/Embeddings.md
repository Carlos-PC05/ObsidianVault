---
aliases:
  - embeddings
tags:
  - ai
  - motor-RAG
  - ai/embeddings
source: "[[Embeddings with the OpenAI API]]"
---
### Qué son las embeddings?
NLP (Natural Language Processing) -> es una rama de la inteligencia artificial y la informática que permite a los ordenadores leer, interpretar, entender y generar el lenguaje humano.

Las embeddings son representaciones numéricas de texto.

### Cómo funcionan?
El texto se mapea a un vector especial multidimensional. Los números escupidos por el modelo son las localizaciones en el espacio del texto.
Palabras similares aparecen cerca en le espacio mientras que palabras diferentes aparecen lejos las unas de las otras.

### Por qué son útiles? 
Las embeddings permiten capturar el significado semántico del texto.
**Significado semántico**: contexto e intención detrás del texto.

Los motores de búsqueda tradicionales buscan usando patrones de palabras clave por lo que pueden perderse la verdadera intención del mensaje.

### Cómo medir la distancia entre puntos
##### Distancia Coseno
La distancia coseno mide el ángulo entre dos vectores de embeddings. Ignora el tamaño del texto y evalúa solo su dirección semántica. Su valor va de 0 (apuntan al mismo sitio y son idénticos) a 2 (apuntan en sentidos totalmente opuestos), calculándose como 1 - similitud

- **Similitud vs Distancia**: La similitud va de -1 a 1. La distancia coseno es el resultado de restar esa similitud a 1. 

- **Independencia de escala**: No importa si un texto es corto o largo; si hablan del mismo tema, la distancia será baja. 

- **Rango de valores**:
    - **0**: Significado idéntico.
    - **1**: Sin relación (vectores perpendiculares).
    - **2**: Significados totalmente opuestos.

**Ver ejemplos prácticos aquí ->** [[chapter1.pdf]]

## Búsqueda Semántica
1. Embed the search query and other texts
2. Compute the cosine distances
3. Extract the texts with the smallest cosine distance

## Sistemas de recomendación con embeddings
Muy similares a la búsqueda semántica:
1. Embed the potential recommendations and data point
2. Calculate cosine distances
3. Recommend closest items

#### Recomendaciones con múltiples data points
1. Combine multiple vectors into one by taking the mean
2. Compute cosine distances
3. Recommend closest vector
	1. Ensure that it's unread.

## Tareas de clasificación
**Asignar etiquetas a objetos**
- Categorización
	- Ejemplo: headlines into topics
- Análisis de sentimiento
	- Exjemplo: Clasificar una review como positiva o negativa

### Zero-shot classification
No se usan datos etiquetados
**Proceso**:
1. Embed class descriptions
2. Embed the item to classify 
3. Compute cosine distances
4. Assign the most similar labels

**Ver ejemplos de código aquí ->** [[chapter2.pdf]]

## Vector databases para embedding systems

### Limitaciones del enfoque actual
- Cargar todas las embeddings en memoria (1536 floats ~ 13kB / embedding)
- Recalcular embeddings para cada nueva query
- Calcular las distancias coseno para cada embedding y ordenarlas es lento y escala de forma lineal

### Qué es una vector database
Los documentos embebidos se almacenan y consultan desde la vector database.

- **NoSQL Database**: estructura más flexible que permite consultas más rápidas.
- **SQL / Relational Database**: datos estructurados en tablas, filas y columnas.

### Componentes a almacenar
- **Embeddings**
- **Source texts**
- **Metadatos**: IDs y referencias, y datos adicionales útiles para filtrar resultados

> **Top tip**: ¡No almacenar el source text como metadata!

### ¿Qué solución es mejor?
- **Gestión de la base de datos**:
    - Managed → más caro pero reduce la carga de trabajo
    - Self-managed → más barato pero requiere tiempo y expertise
- **¿Open source o comercial?**
    - Open source → flexible y coste-efectivo
    - Comercial → mejor soporte, features más avanzadas y compliance
- **Data models**: ¿el tipo de datos se presta a un tipo de base de datos concreto?
- **Specific features**: ¿tu caso de uso depende de funcionalidades específicas, como almacenamiento multi-modal?

## Creando vector databases con ChromaDB
ChromaDB es una vector database simple pero potente. Tiene dos variantes:
- **Local**: ideal para desarrollo y prototipado
- **Client/Server**: pensada para producción

### Conectarse a la base de datos
```python
import chromadb
client = chromadb.PersistentClient(path="/path/to/save/to")
```
Los datos se persisten a disco.

### Crear una colección
Las colecciones son análogas a las tablas de una base de datos SQL.
```python
from chromadb.utils.embedding_functions import OpenAIEmbeddingFunction
collection = client.create_collection(
    name="my_collection",
    embedding_function=OpenAIEmbeddingFunction(
        model_name="text-embedding-3-small",
        api_key="..."
    )
)
```
Las colecciones son capaces de crear embeddings automáticamente.

### Inspeccionar colecciones
```python
client.list_collections()
# [Collection(name=my_collection)]
```

### Insertar embeddings
**Documento único**:
```python
collection.add(ids=["my-doc"], documents=["This is the source text"])
```
- Los IDs deben ser proporcionados
- Las embeddings las creará la colección

**Múltiples documentos**:
```python
collection.add(
  ids=["my-doc-1", "my-doc-2"],
  documents=["This is document 1", "This is document 2"]
)
```

### Inspeccionar una colección
- **Contar documentos**: `collection.count()`
- **Ver los primeros 10 items**: `collection.peek()` → devuelve un dict con `ids`, `embeddings`, `documents`, `metadatas`
- **Recuperar items concretos**: `collection.get(ids=["s59"])` → devuelve `ids`, `embeddings`, `metadata`, `documents`, `uris`, `data`

### Estimar el coste de las embeddings
El modelo `text-embedding-3-small` cuesta **$0.00002 / 1k tokens**:
```python
cost = 0.00002 * len(tokens) / 1000
```
Los tokens se cuentan con la librería **tiktoken**:
```python
import tiktoken

enc = tiktoken.encoding_for_model("text-embedding-3-small")

total_tokens = sum(len(enc.encode(text)) for text in documents)

cost_per_1k_tokens = 0.00002

print('Total tokens:', total_tokens)
print('Cost:', cost_per_1k_tokens * total_tokens / 1000)
# Total tokens: 444463
# Cost: 0.00888926
```
Precios actualizados en https://openai.com/pricing

**Ver ejemplos prácticos aquí ->** [[chapter3.pdf]]

## Consultar y actualizar la base de datos

### Recuperar la colección
```python
from chromadb.utils.embedding_functions import OpenAIEmbeddingFunction
collection = client.get_collection(
    name="netflix_titles",
    embedding_function=OpenAIEmbeddingFunction(api_key="...")
)
```
Se debe especificar la **misma embedding function** que se usó al añadir los datos.

### Consultar la colección
```python
result = collection.query(
  query_texts=["movies where people sing a lot"],
  n_results=3
)
```
`query()` devuelve un dict con múltiples keys:
- `ids`: los ids de los items devueltos
- `embeddings`: las embeddings de los items devueltos
- `documents`: los source texts de los items devueltos
- `metadatas`: los metadatos de los items devueltos
- `distances`: las distancias de los items devueltos respecto al query text

Cada lista del resultado corresponde a un `query_text`: varios query texts → varias listas.

### Actualizar una colección
```python
collection.update(
  ids=["id-1", "id-2"],
  documents=["New document 1", "New document 2"]
)
```
- Incluir solo los campos a actualizar; el resto quedará igual
- La colección creará las embeddings automáticamente

### Upsert
```python
collection.upsert(
  ids=["id-1", "id-2"],
  documents=["New document 1", "New document 2"]
)
```
- Si los IDs faltan → los añade
- Si los IDs existen → los actualiza

### Borrar
```python
collection.delete(ids=["id-1", "id-2"])
```
Borrar toda la base de datos:
```python
client.reset()
```
> **Warning**: ¡esto elimina todo lo que hay en la base de datos!

## Múltiples queries y filtrado

### Recomendaciones basadas en múltiples data points
```python
reference_ids = ['s8170', 's8103']

reference_texts = collection.get(ids=reference_ids)["documents"]
result = collection.query(
  query_texts=reference_texts,
  n_results=3
)
```
El resultado es una lista de listas: cada sublista corresponde a un query text, por lo que cada una contiene sus propias recomendaciones.

### Añadir metadatos
Crear una lista de dicts para los metadatos y una lista de IDs, y añadirlos a los items existentes:
```python
import csv

ids = []
metadatas = []

with open('netflix_titles.csv') as csvfile:
  reader = csv.DictReader(csvfile)
  for i, row in enumerate(reader):
    ids.append(row['show_id'])
    metadatas.append({
      "type": row['type'],
      "release_year": int(row['release_year'])
    })

collection.update(ids=ids, metadatas=metadatas)
```

### Consultar con filtros de metadatos
```python
result = collection.query(
    query_texts=reference_texts,
    n_results=3,
    where={
        "type": "Movie"
    }
)
```

### Operadores `where`
```python
where = {"type": "Movie"}
```
es lo mismo que
```python
where = {"type": {"$eq": "Movie"}}
```
Lista de operadores:
- `$eq` - equal to (string, int, float)
- `$ne` - not equal to (string, int, float)
- `$gt` - greater than (int, float)
- `$gte` - greater than or equal to (int, float)
- `$lt` - less than (int, float)
- `$lte` - less than or equal to (int, float)

### Múltiples filtros `where`
```python
where = {
    "$and": [
        {"type": {"$eq": "Movie"}},
        {"release_year": {"$gt": 2020}}
    ]
}
```
- `$and`: deben cumplirse todas las condiciones
- `$or`: filtrar por al menos una condición

## Resumen y siguientes pasos
- Cap. 1: embeddings como representación vectorial del texto, similitud con distancia coseno.
- Cap. 2: búsqueda semántica, sistemas de recomendación y clasificación.
- Cap. 3: vector databases → almacenan embeddings + source texts + metadatos; ChromaDB local o client/server; operaciones `add`, `get`, `query`, `update`, `upsert`, `delete`; filtrado con `where`.

**Dónde seguir**:
- **Pinecone** → Semantic Search with Pinecone (Code-along)
- **Weaviate** → Vector Databases for Data Science with Weaviate in Python (Code-along)
- **LangChain** → How to Build LLM Applications with LangChain / Introduction to Large Language Models with GPT & LangChain

