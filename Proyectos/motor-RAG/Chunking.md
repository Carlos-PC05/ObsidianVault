---
aliases:
  - chunking
  - chunking de documentos
tags:
  - ai
  - ai/embeddings
  - ai/vector-databases
  - motor-RAG
source: "https://www.pinecone.io/learn/chunking-strategies/"
---
### Qué es el chunking y por qué es necesario
El **chunking** es el proceso de dividir texto largo en segmentos más pequeños llamados *chunks* (Pinecone). Cada chunk se embebe por separado y se almacena en la vector database. Como la recuperación opera a nivel de chunk, **el chunk es la unidad de respuesta**: la similitud entre una query y un documento se decide comparando los vectores de los chunks, no del documento completo.

**Dos razones fundamentales:**

1. **Context window limitada**: todos los modelos de embeddings tienen una longitud máxima de entrada. Si se supera, los tokens sobrantes se **truncan o descartan** antes de vectorizar, perdiendo contexto que podría aparecer en la búsqueda.
2. **Autocontención**: los chunks deben contener información relevante por sí mismos. Regla práctica de Pinecone: *"si el chunk tiene sentido para un humano sin el contexto circundante, también lo tendrá para el modelo de lenguaje"*.

**Trade-off fundamental:**
- Chunks muy pequeños → resultados imprecisos, contexto perdido
- Chunks muy grandes → ruido y dilución del significado de cada frase, mayor latencia y coste

**Restricciones de los modelos concretos (importante para este proyecto):**
- `all-MiniLM-L6-v2` (sentence-transformers): vectores de **384 dimensiones** y **por defecto trunca el texto de más de 256 tokens** → límite duro de ~256 tokens por chunk. Es además la **función de embeddings por defecto de ChromaDB**.
- `text-embedding-3-small` (OpenAI): **1536 dimensiones**, máximo **8192 tokens** por entrada.
- `text-embedding-ada-002`: 1536 dimensiones, máximo 8192 tokens.

## Chunking de tamaño fijo (fixed-size) con solapamiento
**Mecanismo**: se fija un número de tokens (o caracteres) por chunk y se divide por ese número. Lo habitual es usar como tamaño el máximo de la context window del modelo de embeddings.

**Pinecone recomienda empezar por aquí**: el chunking de tamaño fijo "será el mejor camino en la mayoría de los casos" y se debe iterar solo si resulta insuficiente.

**Chunk Size vs Chunk Overlap** (definiciones del tutorial de referencia, Greg Kamradt):
- **Chunk Size**: número de caracteres (o tokens) por chunk.
- **Chunk Overlap**: solapamiento entre chunks consecutivos, para evitar cortar una pieza de contexto en varias. Ejemplo: con chunk_size=35 y overlap=4, `'This is the text I would like to ch'` y `'o chunk up. It is the example text'` comparten `'o ch'`.
- El solapamiento **duplica datos** entre chunks: es un coste a tener en cuenta (tokens extra embebidos y almacenados).

**Caracteres vs tokens**: LangChain mide el tamaño en caracteres por defecto (`length_function`, `len`); OpenAI recomienda contar tokens con **`tiktoken`** (encoding `cl100k_base` para los modelos text-embedding-3). Los tokens no equivalen a caracteres: cada modelo tokeniza distinto.

**Valores típicos de solapamiento**: ~10-20% del chunk (p.ej. LlamaIndex usa `chunk_size=1024, chunk_overlap=20`; Kamradt `chunk_size=200, chunk_overlap=15`).

**Limitación**: el corte fijo parte frases y pensamientos por la mitad, lo que motivó los splitters recursivos y estructurales.

## Splitting recursivo por caracteres (LangChain)
Es **el splitter recomendado para texto genérico** por LangChain. Está parametrizado por una lista de separadores que se intentan **en orden** hasta que los chunks son suficientemente pequeños. La lista por defecto es:

```python
["\n\n", "\n", " ", ""]
```

El efecto: mantiene juntos los párrafos (y luego frases y palabras) tanto como sea posible, por ser las piezas de texto semánticamente más relacionadas.

**Lógica en cascada**: primero intenta `\n\n` (saltos de párrafo); si el trozo sigue siendo mayor que `chunk_size`, recurre con `\n` (líneas), luego ` ` (palabras) y finalmente `""` (caracteres).

**Parámetros oficiales**:
- `chunk_size`: tamaño máximo del chunk (medido por `length_function`)
- `chunk_overlap`: solapamiento objetivo; "mitiga la pérdida de información cuando el contexto se divide entre chunks"
- `length_function`: qué define el tamaño del chunk
- `is_separator_regex`: si los separadores son regex

Notas importantes:
- `chunk_size` es **un máximo, no una garantía**: los chunks "encajan" en el separador más cercano, así que suelen quedar por debajo del objetivo.
- Para idiomas sin separación de palabras (chino, japonés, tailandés) hay que añadir separadores propios para no partir palabras.

## Splitting estructural (content-aware)
Divide el documento **respetando su estructura** para conservar las fronteras coherentes del contenido (cabeceras, tablas, bloques de código).

**Splitting por frases (sentencias)**: naive (por `.` y saltos de línea), **NLTK** (tokenizador entrenado) o **spaCy** (segmentación sofisticada).

**MarkdownTextSplitter de LangChain** (lista de separadores verificada):
`\n#{1,6}` (cabeceras H1–H6) → bloques de código → reglas horizontales (`***`, `---`, `___`) → `\n\n` → `\n` → ` ` → `""`

**CodeSplitter de LangChain**: separadores `\nclass`, `\ndef`, `\n\tdef`, `\n\n`... mantiene la clase junto a sus métodos.

**Node parsers de LlamaIndex** (su forma de hacer chunking):
- `SentenceSplitter`: respeta las fronteras de las frases
- `HTMLNodeParser` (tags por defecto `p`, `h1`–`h6`, `li`, `b`, `i`, `u`, `section`)
- `MarkdownNodeParser`, `JSONNodeParser`
- `CodeSplitter(language, chunk_lines=40, chunk_lines_overlap=15, max_chars=1500)`
- `SentenceWindowNodeParser`: divide en frases individuales y guarda una **ventana de contexto en la metadata** (por defecto 3 frases a cada lado), **invisible para el modelo de embeddings**; tras la recuperación, `MetadataReplacementNodePostProcessor` inyecta la ventana completa al LLM
- `HierarchicalNodeParser`: jerarquía padre/hijo, p.ej. `chunk_sizes=[2048, 512, 128]`

**Tablas en PDFs**: el split por caracteres corrompe las tablas. Práctica recomendada: extraer la tabla, embeber un **resumen** de la misma, y pasar la tabla original al LLM cuando haya match.

## Semantic chunking y Late Chunking

### Semantic chunking (fronteras basadas en embeddings)
Introducido por Greg Kamradt ("5 Levels of Text Splitting", nivel 4). Motivación: ¿por qué un tamaño fijo global, si el contenido es distinto? Hipótesis: "chunks semánticamente similares deberían mantenerse juntos".

**Algoritmo exacto**:
1. Dividir el documento en frases y agruparlas con sus vecinas usando una **ventana deslizante de 3 frases** (embeddings de frases individuales resultaban demasiado ruidosos)
2. Calcular la embedding de cada grupo
3. Distancia coseno entre grupos consecutivos: `distance = 1 - cosine_similarity(grupo_i, grupo_{i+1})`
4. **Umbral de corte = percentil 95 de todas las distancias** (único parámetro; bajar el percentil → más chunks)
5. Cortar en cada índice cuya distancia supere el umbral

Pinecone lo describe igual (grupos de frases, comparación semántica con el grupo anterior, corte donde cambia el tema) y lo marca como **experimental**. Coste: una llamada de embedding extra por cada grupo de frases, y los chunks resultantes tienen **tamaño variable sin tope de tokens**.

### Late Chunking (Jina AI, arXiv:2409.04701)
**Problema que resuelve**: las embeddings por chunk pierden "información contextual de los chunks circundantes", produciendo representaciones subóptimas.

**Método**: usar un **modelo de embeddings de contexto largo** para embeber **todos los tokens del texto completo**, y aplicar el chunking **después del transformer y justo antes del mean pooling** (de ahí "late"). Las embeddings resultantes capturan el contexto de todo el documento. Funciona **sin entrenamiento adicional** y es genérico para modelos de contexto largo.

> **Restricción práctica**: late chunking requiere un modelo de embeddings de contexto largo (p.ej. jina-embeddings-v2/v3, 8k+ tokens). **No es posible con `all-MiniLM-L6-v2`** (trunca a 256 tokens): el documento completo debe caber en la ventana del modelo.

## Qué dice la literatura sobre el tamaño de chunk

**Survey de RAG (Gao et al., arXiv:2312.10997)**: el método más común es dividir en un **número fijo de tokens (100, 256, 512)**. Trade-off: "chunks más grandes capturan más contexto, pero generan más ruido, requieren más tiempo y coste; los más pequeños pueden no transmitir todo el contexto, pero tienen menos ruido". Escala de granularidad (fino → grueso): Token, Phrase, Sentence, Proposition, Chunk, Document.

**Small2Big**: usar frases (pequeño) como unidad de recuperación y proporcionar frases vecinas (grande) como contexto al LLM — la misma idea que el `SentenceWindowNodeParser` de LlamaIndex.

**Procedimiento de tuning de Pinecone**: probar un rango — chunks pequeños (p.ej. **128 o 256 tokens**) para información semántica granular, y grandes (p.ej. **512 o 1024 tokens**) para más contexto — embebiendo cada tamaño en un índice distinto o en **múltiples namespaces**, y evaluando con un set representativo de queries.

**Chunk expansion**: recuperar top-k chunks y expandir cada uno con sus vecinos (párrafos, páginas o el documento completo) — desacopla la granularidad de indexación de la granularidad de la respuesta.

> **Restricción decisiva para este proyecto**: con `all-MiniLM-L6-v2` el límite de 256 tokens manda sobre cualquier recomendación genérica. Los rangos de Pinecone (256/512) y OpenAI (512–1024) deben ajustarse a ≤256 tokens (≈150–200 tokens por chunk es seguro).

**No hay tamaño universalmente óptimo**: el survey concluye que ni la granularidad fina ni la gruesa logran "equilibrar completitud semántica y longitud de contexto".

## Chunking en pipelines RAG: chunk ↔ top_k ↔ ventana de contexto del LLM

**Forma del pipeline** (survey): Indexing (documentos → chunks → embeddings → vector DB) → Retrieval (top-k por similitud) → Generation (chunks + pregunta en el prompt).

**Relación de presupuesto**: los tokens totales que entran al LLM = `top_k × chunk_size` (+ query e instrucciones). Tamaño de chunk y `top_k` están **acoplados por la ventana de contexto del LLM**; chunks grandes aumentan latencia y coste.

**Más contexto NO es automáticamente mejor — "Lost in the Middle" (Liu et al., TACL 2023, arXiv:2307.03172)**:
- QA multi-documento con pasajes de **≤100 tokens** como chunks, variando la posición del pasaje con la respuesta
- **Curva de rendimiento en forma de U**: mejor rendimiento cuando la información está al principio (primacía) o al final (recencia); **caída de más del 20%** cuando está en el medio. El peor caso de GPT-3.5-Turbo con 20 y 30 documentos queda **por debajo de su precisión sin contexto**
- Los modelos de contexto extendido **no aprovechan mejor** su contexto (GPT-3.5-Turbo vs su variante 16K ≈ mismas curvas)
- En QA open-domain la precisión del lector **se satura mucho antes que el recall del retriever**: 50 vs 20 documentos recuperados solo gana ~1.5%. Recomendación: **rerankear** para que los chunks relevantes queden cerca de los bordes del prompt, o truncar la lista ordenada

**Receta práctica derivada de las fuentes**:
1. Limitar `chunk_size` por la ventana del modelo de embeddings — para `all-MiniLM-L6-v2`: ≤ ~200 tokens (256 = truncación dura)
2. Elegir primero `chunk_size` (survey: 100/256/512 tokens son los comunes), luego fijar `top_k` de forma que `top_k × chunk_size` quepa en la ventana del LLM con margen
3. Preferir ~10-20 chunks recuperados de ≤512 tokens; no llenar la ventana entera a ciegas
4. Post-recuperación: rerankear, comprimir, o expandir con SentenceWindow / Small2Big

## Aplicación a este proyecto (motor-RAG)
Siguiendo el Roadmap del proyecto:
- Empezar con **chunking de tamaño fijo** por tokens con **solapamiento** (recomendación de Pinecone)
- Respetar el límite del modelo: `all-MiniLM-L6-v2` → **chunks de ~150-200 tokens** con overlap de ~10-20% (≈15-30 tokens)
- Para `text-embedding-3-small` (si se cambia el modelo) se podría subir a 512-1024 tokens
- Medir el conteo de tokens con `tiktoken` y verificar que ningún chunk supera la ventana del modelo antes de embebber
- Evaluar después con un set de queries reales probando distintos tamaños en namespaces separados

## Fuentes (primarias)
- Pinecone — Chunking strategies: https://www.pinecone.io/learn/chunking-strategies/
- HuggingFace — Model card `all-MiniLM-L6-v2` (384 dims, truncación a 256 tokens): https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2
- OpenAI — Embeddings docs (dimensiones y máximos 8192): https://platform.openai.com/docs/guides/embeddings
- ChromaDB — Embedding functions (default = all-MiniLM-L6-v2): https://docs.trychroma.com/docs/embeddings/embedding-functions
- LangChain — RecursiveCharacterTextSplitter: https://docs.langchain.com/oss/python/integrations/splitters/recursive_text_splitter
- Greg Kamradt — 5 Levels of Text Splitting (semantic chunking, 95th percentile): https://github.com/FullStackRetrieval-com/RetrievalTutorials/blob/main/tutorials/LevelsOfTextSplitting/5_Levels_Of_Text_Splitting.ipynb
- LlamaIndex — Node parsers: https://docs.llamaindex.ai/en/stable/module_guides/loading/node_parsers/
- Liu et al. — Lost in the Middle (TACL 2023): https://arxiv.org/abs/2307.03172
- Jina AI — Late Chunking: https://arxiv.org/abs/2409.04701
- Gao et al. — RAG survey (tamaños 100/256/512, Small2Big): https://arxiv.org/abs/2312.10997