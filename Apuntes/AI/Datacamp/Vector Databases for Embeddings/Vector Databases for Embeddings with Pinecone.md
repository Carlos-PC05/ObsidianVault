---
tags:
  - ai
  - ai/embeddings
  - ai/vector-databases
---
# Slides

[[Apuntes/AI/Datacamp/Vector Databases for Embeddings/chapter1.pdf]]

[[Apuntes/AI/Datacamp/Vector Databases for Embeddings/chapter2.pdf]]
# Notes

## Introduction to Pinecone

### Introduction to Pinecone indexes

#### Creating a Pinecone client

Throughout the course, you'll write Python code to interact with ==Pinecone== via the Pinecone Python client. As a first step, you'll need to create your own Pinecone API key. **Pinecone API keys used in this course's exercises will not be stored in any way.**

To create a key, you'll first need to create a Pinecone starter account, which is free, by visiting [https://www.pinecone.io/](https://www.pinecone.io/). Next, navigate to the API Keys page to create your key.

```python
# Import the Pinecone library
from pinecone import Pinecone

# Initialize the Pinecone client
pc = Pinecone(api_key="<PINECONE_TOKEN>")
```

#### Your first Pinecone index

With your Pinecone client initialized, you're all set to begin creating an **index**! Indexes are used to store records, including the _vectors_ and associated _metadata_, as well as serving _queries_ and other manipulations. As you progress through the course, you'll see  
how these different steps build up to a modern AI system built on a _vector database_.

**If you accidentally create a valid index that doesn't meet the specifications detailed in the instructions, you'll need to add the following code before your** `.create_index()` **code to delete it and re-create it**:

```
pc.delete_index('my-first-index')
```

```python
# Import ServerlessSpec
from pinecone import ServerlessSpec

# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

# Create your Pinecone index
pc.create_index(
    name="my-first-index", 
    dimension=256, 
    spec=ServerlessSpec(
        cloud='aws', 
        region='us-east-1'
    )
)
```

### Managing indexes

#### Connecting to an index

To begin ingesting vectors and performing vector manipulations in your newly-created Pinecone index, you'll first need to connect to it! The resulting index object has a number of methods for ingesting, manipulating, and exploring the contents of the index in Python.

```python
# Set up the client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

# Connect to your index
index = pc.Index("my-first-index")

# Print the index statistics
print(index.describe_index_stats())
```

#### Deleting an index

If you have an index that has gone stale, perhaps it's time to delete it! **Deleting an index will also delete all of the data it contains**, so be cautious when doing this in your own projects!

```python
# Set up the client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

# Delete your Pinecone index
pc.delete_index("my-first-index")

# List your indexes
print(pc.list_indexes())
```

### Vector ingestion

### Checking dimensionality

You now have the know-how to begin ingesting vectors into a new Pinecone index! Before you jump in, you should check that your vectors are compatible with the dimensionality of your new index.

A list of dictionaries containing records to ingest has been provided as `vectors`. Here's a preview of its structure:

```
vectors = [
    {
        "id": "0",
        "values": [0.025525547564029694, ..., 0.0188823901116848]
        "metadata": {"genre": "action", "year": 2024}
    },
        ...,
]
```

If you accidentally create a valid index that doesn't meet the specifications detailed in the instructions, you'll need to add the following code before your `.create_index()` code:

```
pc.delete_index('datacamp-index')
```

```python
# Initialize the Pinecone client using your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

#Create your Pinecone index
pc.create_index(
    name="datacamp-index", 
    dimension=1536, 
    spec=ServerlessSpec(
        cloud='aws', 
        region='us-east-1'
    )
)

# Check that each vector has a dimensionality of 1536
vector_dims = [len(vector['values']) == 1536 for vector in vectors]
print(all(vector_dims))
```

#### Ingesting vectors with metadata

It's ingesting time! You'll be ingesting `vectors`, which is a list of dictionaries containing the vector values, IDs, and associated metadata. They're already been provided in a format that can be directly inserted into the index without further manipulation.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

# Connect to your index
index = pc.Index("datacamp-index")

# Ingest the vectors and metadata
index.upsert(
    vectors=vectors
)

# Print the index statistics
print(index.describe_index_stats())
```

## Pinecone Vector Manipulation in Python

### Retrieving vectors

#### Querying vs. fetching

In Pinecone, there are two primary methods for accessing vectors from an index: **fetching** and **querying**.

#### Fetching vectors

In this exercise, you've been provided with a list of `ids` containing IDs of different records in your `'datacamp-index'` index. You'll use these IDs to retrieve the associated records and explore their metadata.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')
ids = ['2', '5', '8']

# Fetch vectors from the connected Pinecone index
fetched_vectors = index.fetch(ids=ids)

# Extract the metadata from each result in fetched_vectors
metadatas = [fetched_vectors['vectors'][id]['metadata'] for id in ids]
print(metadatas)
```

### Querying vectors

#### Returning the most similar vectors

Querying vectors is foundational to so many AI applications. It involves embedding a user input, comparing it to the vectors in the database, and returning the most similar vectors.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Retrieve the top three most similar records
query_result = index.query(
    vector=vector,
    top_k=3
)

print(query_result)
```

#### Changing distance metrics

By default, Pinecone indexes using the cosine similarity distance metric to compute similarity scores between vectors, which are used when querying to find the most similar vectors. Pinecone also supports other distance metrics, including Euclidean distance and the dot product.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

#Create an index that uses the dot product distance metric
pc.create_index(
    name="dotproduct-index",
    dimension=1536,
    metric='dotproduct',
    spec=ServerlessSpec(
        cloud='aws',
        region='us-east-1'
    )
)

# Print a list of your indexes
print(pc.list_indexes())
```

### Metadata filtering

#### Filtering queries

In this exercise, you'll practice querying the `'datacamp-index'` Pinecone index. You'll connect to the index and query it using the `vector` provided to retrieve similar vectors. You'll also use metadata filtering to optimize your querying and return the most relevant search results.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Retrieve the MOST similar vector with the year 2024
query_result = index.query(
    vector=vector,
    top_k=1,
    filter={
        "year": 2024
    }
)
print(query_result)
```

#### Multiple metadata filters

As well as equality operations, Pinecone provides operators for other core comparison operations, as well as enabling multiple filters in a single query. In this exercise, you'll create multiple filters using Pinecone's other comparison operators and use them to re-query your index.

```python
# Initialize the Pinecone client using your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Retrieve the MOST similar vector with genre and year filters
query_result = index.query(
    vector=vector,
    top_k=1,
    filter={
        "genre": "thriller",
        "year": {"$lt": 2018}
    }
)
print(query_result)
```

### Updating and deleting vectors

#### Updating vector values

In dynamic environments, data evolves rapidly, and being able to seamlessly integrate new values and metadata into existing vectors keeps your applications relevant and accurate.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Update the values of vector ID 7
index.update(
    id="7",
    values=vector
)

# Fetch vector ID 7
fetched_vector = index.fetch(ids=['7'])
print(fetched_vector)
```

#### Updating vector metadata

This time, you'll practice updating vectors in the `datacamp-index` Pinecone index with new metadata. You'll again verify these changes were successful by retrieving the vector and checking its metadata.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Update the metadata of vector ID 7
index.update(
    id="7",
    set_metadata={
        "genre": "thriller", 
        "year": 2024} 
)

# Fetch vector ID 7
fetched_vector = index.fetch(ids=['7'])
print(fetched_vector)
```

#### Deleting vectors

Deleting vectors isn't just about tidying up our databases; it's about optimizing performance. As your indexes grow, unnecessary or outdated vectors can clutter your storage and slow down query performance. By removing redundant data, you streamline your operations, leading to faster responses and better resource utilization.

In this exercise, you'll practice deleting vectors from the `'datacamp-index'` Pinecone index. You'll check the index metrics to verify the deletion took place.

```python
# Initialize the Pinecone client using your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Delete vectors
index.delete(
  ids=["3", "4"]
)

# Retrieve metrics of the connected Pinecone index
print(index.describe_index_stats())
```

## Performance Tuning and AI applications

#### Defining a function for chunking

To be able to batch upserts in a reproducible way, you'll need to define a function to split your list of vectors into chunks.

```python
def chunks(iterable, batch_size=100):
    """A helper function to break an iterable into chunks of size batch_size."""
    # Convert the iterable into an iterator
    it = iter(iterable)
    # Slice the iterator into chunks of size batch_size
    chunk = tuple(itertools.islice(it, batch_size))
    while chunk:
        # Yield the chunk
        yield chunk
        chunk = tuple(itertools.islice(it, batch_size))
```

#### Batching upserts in chunks

In this exercise, you'll practice ingesting vectors into the `'datacamp-index'` Pinecone index in series, batch-by-batch.

The `chunks()` helper function you created in the previous exercise is available to use:

```
def chunks(iterable, batch_size=100):
    """A helper function to break an iterable into chunks of size batch_size."""
    it = iter(iterable)
    chunk = tuple(itertools.islice(it, batch_size))
    while chunk:
        yield chunk
        chunk = tuple(itertools.islice(it, batch_size))
```

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Upsert vectors in batches of 100
for chunk in chunks(vectors):
    index.upsert(vectors=chunk) 

# Retrieve statistics of the connected Pinecone index
print(index.describe_index_stats())
```

#### Batching upserts in parallel

In this exercise, you'll practice ingesting vectors into the `'datacamp-index'` Pinecone index in parallel. You'll need to connect to the index, upsert vectors in batches asynchronously, and check the updated metrics of the `'datacamp-index'` index.

```python
# Initialize the client
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k", pool_threads=20)

index = pc.Index('datacamp-index')

# Upsert vectors in batches of 200 vectors
with pc.Index('datacamp-index', pool_threads=20) as index:
    async_results = [index.upsert(vectors=chunk, async_req=True) for chunk in chunks(vectors, batch_size=200)]
    [async_result.get() for async_result in async_results]

# Retrieve statistics of the connected Pinecone index
print(index.describe_index_stats())
```

### Multitenancy and namespaces

#### Namespaces

Namespaces are used to separate vectors within a single index, allowing targeted  
queries and minimizing the number of scanned records.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Upsert vector_set1 to namespace1
index.upsert(
    vectors=vector_set1,
    namespace="namespace1"
)

# Upsert vector_set2 to namespace2
index.upsert(
    vectors=vector_set2,
    namespace="namespace2"
)

# Print the index statistics
index.describe_index_stats()
```

#### Querying namespaces

Now that you've created separate namespaces and embraced multitenancy, it's time to reap the rewards! In this exercise, you'll query one of these new namespaces to search through only a segment of your full index.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

index = pc.Index('datacamp-index')

# Query namespace1 with the vector provided
query_result = index.query(
    vector=vector,
    namespace="namespace1",
    top_k=3
)
print(query_result)
```

### Semantic search with Pinecone

#### Creating and configuring a Pinecone index

To kickstart your semantic search application, you'll create and configure a new inecone index named `'pinecone-datacamp'`. You'll use this in subsequent exercises to host Wikipedia articles from the SQuAD dataset.

```python
# Initialize the Pinecone client with your API key
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")

# Create Pinecone index
pc.create_index(
    name='pinecone-datacamp', 
    dimension=1536,
    spec=ServerlessSpec(cloud='aws', region='us-east-1')
)

# Connect to index and print the index statistics
index = pc.Index('pinecone-datacamp')
print(index.describe_index_stats())
```

#### Upserting vectors for semantic search

Time to embed some text data and upsert the vectors and metadata into your `pinecone-datacamp'` index! You've be given a dataset named [`squad_dataset.csv`](https://assets.datacamp.com/production/repositories/6574/datasets/1f41cf7838de4538a9ab99ff7fbd363643f96a1d/squad_dataset.csv), and a sample of 200 rows has been loaded in the DataFrame, `df`.

```python
# Initialize the Pinecone client
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")
index = pc.Index('pinecone-datacamp')

batch_limit = 100

for batch in np.array_split(df, len(df) / batch_limit):
    # Extract the metadata from each row
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

#### Querying vectors for semantic search

In this exercise, you'll create a query vector from the question, `'What is in front of the Notre Dame Main Building?'`. Using this embedded query, you'll query the `'squad_dataset'` namespace from the `'pinecone-datacamp'` index and return the top five most similar vectors.

```python
# Initialize the Pinecone client
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")
index = pc.Index('pinecone-datacamp')

query = "What is in front of the Notre Dame Main Building?"

# Create the query vector
query_response = client.embeddings.create(
    input=query,
    model="text-embedding-3-small"
)
query_emb = query_response.data[0].embedding

# Query the index and retrieve the top five most similar vectors
retrieved_docs = index.query(
    vector=query_emb,
    top_k=5,
    namespace="squad_dataset")

for result in retrieved_docs['matches']:
    print(f"{result['id']}: {round(result['score'], 2)}")
    print('\n')
```

### RAG chatbot with Pinecone and OpenAI

#### Upserting YouTube transcripts

In this following exercises, you'll create a chatbot that can answer questions about YouTube videos by ingesting video transcripts and additional metadata into your `'pinecone-datacamp'` index.

To start, you'll prepare data from the [`youtube_rag_data.csv`](https://assets.datacamp.com/production/repositories/6574/datasets/19867f01bd9ec4120f05018604da13a0aeb32cfa/youtube_rag_data_small.csv) file and upsert the vectors with all of their metadata into the `'pinecone-datacamp'` index. The data is provided in the DataFrame `youtube_df`.

Here's an example transcript from the `youtube_df` DataFrame:

```
id:
35Pdoyi6ZoQ-t0.0

title:
Training and Testing an Italian BERT - Transformers From Scratch #4

text:
Hi, welcome to the video. So this is the fourth video in a Transformers from Scratch
mini series. So if you haven't been following along, we've essentially covered what
you can see on the screen. So we got some data. We built a tokenizer with it...

url:
<https://youtu.be/35Pdoyi6ZoQ>

published:
01-01-2024
```

```python
# Initialize the Pinecone client
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")
index = pc.Index('pinecone-datacamp')

batch_limit = 100

for batch in np.array_split(youtube_df, len(youtube_df) / batch_limit):
    # Extract the metadata from each row
    metadatas = [{
      "text_id": row['id'],
      "text": row['text'],
      "title": row['title'],
      "url": row['url'],
      "published": row['published']} for _, row in batch.iterrows()]
    texts = batch['text'].tolist()
    
    ids = [str(uuid4()) for _ in range(len(texts))]
    
    # Encode texts using OpenAI
    response = client.embeddings.create(input=texts, model="text-embedding-3-small")
    embeds = [np.array(x.embedding) for x in response.data]
    
    # Upsert vectors to the correct namespace
    index.upsert(vectors=zip(ids, embeds, metadatas), namespace='youtube_rag_dataset')

print(index.describe_index_stats())
```

#### Building a retrieval function

A key process in the Retrieval Augmented Generation (RAG) workflow is retrieving data from the database. In this exercise, you'll design a custom function called `retrieve()` that will perform this crucial process in the final exercise of the course.

```python
# Initialize the Pinecone client
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")
index = pc.Index('pinecone-datacamp')

# Define a retrieve function that takes four arguments: query, top_k, namespace, and emb_model
def retrieve(query, top_k, namespace, emb_model):
    # Encode the input query using OpenAI
    query_response = client.embeddings.create(
        input=query,
        model=emb_model
    )
    
    query_emb = query_response.data[0].embedding
    
    # Query the index using the query_emb
    docs = index.query(vector=query_emb, top_k=top_k, namespace=namespace, include_metadata=True)
    
    retrieved_docs = []
    sources = []
    for doc in docs['matches']:
        retrieved_docs.append(doc['metadata']['text'])
        sources.append((doc['metadata']['title'], doc['metadata']['url']))
        
    return retrieved_docs, sources

documents, sources = retrieve(
  query="How to build next-level Q&A with OpenAI",
  top_k=3,
  namespace='youtube_rag_dataset',
  emb_model="text-embedding-3-small"
)
print(documents)
print(sources)
```

#### RAG questions answering function

You're almost there! The final piece in the RAG workflow is to integrate the retrieved documents with a question-answering model.

A `prompt_with_context_builder()` function has already been defined and made available to you. This function takes the documents retrieved from the Pinecone index, and integrates them into a prompt that the question-answering model can ingest:

```
def prompt_with_context_builder(query, docs):
    delim = '\n\n---\n\n'
    prompt_start = 'Answer the question based on the context below.\n\nContext:\n'
    prompt_end = f'\n\nQuestion: {query}\nAnswer:'

    prompt = prompt_start + delim.join(docs) + prompt_end
    return prompt
```

You'll implement the `question_answering()` function, which will provide OpenAI's language model `gpt-4o-mini` with additional context and sources with which it can answer your questions.

```python
# Initialize the Pinecone client
pc = Pinecone(api_key="pcsk_4KYkP1_Q3EFc9Yy83FBDEN4jrCZdbwBD2xKw5ihbAbm1btjcu8jUYZ2g8MJRVfog6dgp5k")
index = pc.Index('pinecone-datacamp')

query = "How to build next-level Q&A with OpenAI"

# Retrieve the top three most similar documents and their sources
documents, sources = retrieve(query, top_k=3, namespace='youtube_rag_dataset', emb_model="text-embedding-3-small")

prompt_with_context = prompt_with_context_builder(query, documents)
print(prompt_with_context)

def question_answering(prompt, sources, chat_model):
    sys_prompt = "You are a helpful assistant that always answers questions."
    
    # Use OpenAI chat completions to generate a response
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

answer = question_answering(
  prompt=prompt_with_context,
  sources=sources,
  chat_model='gpt-4o-mini')
print(answer)
```