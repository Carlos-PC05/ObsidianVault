---
tags:
  - ai
  - ai/embeddings
---
# Slides

[[chapter1.pdf]]

[[chapter3.pdf]]

[[chapter2.pdf]]
# Notes

## What are Embeddings?

### The wonderful world of embeddings!

#### Creating embeddings

In this exercise, you'll create your very first embeddings using the OpenAI API. Normally, to interact with the OpenAI API, you would need an OpenAI API key, and creating embeddings would incur a cost. However, **you do not need to create or provide an API key in this course**.

```python
# Create an OpenAI client
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create a request to obtain embeddings
response = client.embeddings.create(
  model="text-embedding-3-small",
  input="This can contain any text."
)

# Convert the response into a dictionary
response_dict = response.model_dump()
print(response_dict)
```

#### Digging into the embeddings response

You've been able to successfully use the OpenAI Embeddings endpoint to embed  
text data, and in this exercise, you'll finish this off by extracting information from the API's response.

```python
# Extract the embeddings from response_dict
print(response_dict['data'][0]['embedding'])
```

### Investiagting the vector space

#### Embedding product descriptions

You've been provided with a list of dictionaries called `products`, which contains product information for different products sold by an online retailer. It's your job to embed the `'short_description'` for each product to enable semantic search for the retailer's website.

Here's a preview of the `products` list of dictionaries:

```
products = [
    {
        "title": "Smartphone X1",
        "short_description": "The latest flagship smartphone with AI-powered features and 5G connectivity.",
        "price": 799.99,
        "category": "Electronics",
        "features": [
            "6.5-inch AMOLED display",
            "Quad-camera system with 48MP main sensor",
            "Face recognition and fingerprint sensor",
            "Fast wireless charging"
        ]
    },
    ...
]
```

```python
Embedding product descriptions

You've been provided with a list of dictionaries called products, which contains product information for different products sold by an online retailer. It's your job to embed the 'short_description' for each product to enable semantic search for the retailer's website.

Here's a preview of the products list of dictionaries:

products = [
    {
        "title": "Smartphone X1",
        "short_description": "The latest flagship smartphone with AI-powered features and 5G connectivity.",
        "price": 799.99,
        "category": "Electronics",
        "features": [
            "6.5-inch AMOLED display",
            "Quad-camera system with 48MP main sensor",
            "Face recognition and fingerprint sensor",
            "Fast wireless charging"
        ]
    },
    ...
]

```

# Visualizing the embedded descriptions

Now that you've created embeddings from the product descriptions, it's time to explore them! You'll use [t-SNE](https://www.datacamp.com/tutorial/introduction-t-sne) to reduce the number of dimensions in the embeddings data from 1,536 to two, which will make the data much easier to visualize.

You'll start with the `products` list of dictionaries you worked with in the last exercise, containing product information and the embeddings you created from the `'short_description'`. As a reminder, here's a preview of `products`:

```python
# Create reviews and embeddings lists using list comprehensions
categories = [product['category'] for product in products]
embeddings = [product['embedding'] for product in products]

# Reduce the number of embeddings dimensions to two using t-SNE
tsne = TSNE(n_components=2, perplexity=5)
embeddings_2d = tsne.fit_transform(np.array(embeddings))

# Create a scatter plot from embeddings_2d
plt.scatter(embeddings_2d[:, 0], embeddings_2d[:, 1])

for i, category in enumerate(categories):
    plt.annotate(category, (embeddings_2d[i, 0], embeddings_2d[i, 1]))

plt.show()
```

![[Pasted image 20260806121255.png|365]]
### Text similarity

#### Computing cosine distances

To identify the most semantically similar texts, you need to apply a distance metric. A popular choice is the **cosine distance**.

#### More repeatable embeddings

As you continue to work with embeddings, you'll find yourself making repeated calls to OpenAI's embedding model. To make these calls in a more repeatable and modular way, it would be better to define a custom function called `create_embeddings()` that would output embeddings for any number of text inputs. In this exercise, you'll do just that!

```python
# Define a create_embeddings function
def create_embeddings(texts):
  response = client.embeddings.create(
    model="text-embedding-3-small",
    input=texts
  )
  response_dict = response.model_dump()

  return [data['embedding'] for data in response_dict['data']]

# Embed short_description and print
print(create_embeddings(short_description)[0])

# Embed list_of_descriptions and print
print(create_embeddings(list_of_descriptions))
```

#### Finding the most similar product

Being able to compute similarity between embeddings is a key step within embeddings applications. In this exercise, you'll return to the `products` list of dictionaries that you worked with previously, which contains the embedded short descriptions you also created earlier.

You'll compare a piece of text to these embedded descriptions to identify the most similar description.

```python
# Embed the search text
search_text = "soap"
search_embedding = create_embeddings(search_text)[0]

distances = []
for product in products:
  # Compute the cosine distance for each product description
  dist = distance.cosine(search_embedding, product["embedding"])
  distances.append(dist)

# Find and print the most similar product short_description    
min_dist_ind = np.argmin(distances)
print(products[min_dist_ind]['short_description'])
```

## Embeddings for AI Applications

### Semantic search and enriched embeddings

#### Enriching embeddings

Previously, when you embedded product information, you were limited to only embedding the product `'short_description'`, which captured some, but not all of the relevant product information available. In this exercise, you'll embed `'title'`, `'short_description'`, `'category'`, and `'features'` to capture much more information.

```python
# Define a function to combine the relevant features into a single string
def create_product_text(product):
  return f"""Title: {product['title']}
Description: {product['short_description']}
Category: {product['category']}
Features: {'; '.join(product['features'])}"""

# Combine the features for each product
product_texts = [create_product_text(product) for product in products]

# Create the embeddings from product_texts
product_embeddings = create_embeddings(product_texts)
```

#### Sorting by similarity

Now that you've embedded all of your features, the next step is to compute the similarities. In this exercise, you'll define a function called `find_n_closest()`, which computes the cosine distances between a query vector and a list of embeddings and returns the `n` smallest distances and their indexes.

```python
def find_n_closest(query_vector, embeddings, n=3):
  distances = []
  for index, embedding in enumerate(embeddings):
    # Calculate the cosine distance between the query vector and embedding
    dist = distance.cosine(query_vector, embedding)
    # Append the distance and index to distances
    distances.append({"distance": dist, "index": index})
  # Sort distances by the distance key
  distances_sorted = sorted(distances, key=lambda x: x["distance"])
  # Return the first n elements in distances_sorted
  return distances_sorted[0:n]
```

#### Semantic search for products

Time to put your `find_n_closest()` function to use! You'll test out your semantic product search on a test query, computing a sorted list of the five most semantically similar products, based on the enriched data you gave the model.

```python
# Create the query vector from query_text
query_text = "computer"
query_vector = create_embeddings(query_text)[0]

# Find the five closest distances
hits = find_n_closest(query_vector, product_embeddings, n=5)

print(f'Search results for "{query_text}"')
for hit in hits:
  # Extract the product at each index in hits
  product = products[hit['index']]
  print(product["title"])
```

### Recommendation systems

#### Product recommendation system

In this exercise, you'll make a recommendation system for an online retailer that sells a variety of products. This system recommends three similar products to users who visit a product page but don't purchase, based on the last product they visited.

```python
# Combine the features for last_product and each product in products
last_product_text = create_product_text(last_product)
product_texts = [create_product_text(product) for product in products]

# Embed last_product_text and product_texts
last_product_embeddings = create_embeddings(last_product_text)[0]
product_embeddings = create_embeddings(product_texts)

# Find the three smallest cosine distances and their indexes
hits = find_n_closest(last_product_embeddings, product_embeddings)

for hit in hits:
  product = products[hit['index']]
  print(product['title'])
```

#### Adding user history to the recommendation engine

For many recommendation cases, such as film or purchase recommendation, basing the next recommendation on one data point will be insufficient. In these cases, you'll need to embed all or some of the user's history for more accurate and relevant ecommendations.

```python
# Prepare and embed the user_history, and calculate the mean embeddings
history_texts = [create_product_text(product) for product in user_history]
history_embeddings = create_embeddings(history_texts)
mean_history_embeddings = np.mean(history_embeddings, axis=0)

# Filter products to remove any in user_history
products_filtered = [product for product in products if product not in user_history]

# Combine product features and embed the resulting texts
product_texts = [create_product_text(product) for product in products_filtered]
product_embeddings = create_embeddings(product_texts)

hits = find_n_closest(mean_history_embeddings, product_embeddings)

for hit in hits:
  product = products_filtered[hit['index']]
  print(product['title'])
```

### Embeddings for classification tasks

#### Embedding restaurant reviews

One common classification task that embeddings are great for is sentiment analysis. In this and the following exercises, you'll navigate through the workflow of performing sentiment analysis using embeddings.

```python
# Create a list of class descriptions from the sentiment labels
class_descriptions = [sentiment['label'] for sentiment in sentiments]

# Embed the class_descriptions and reviews
class_embeddings = create_embeddings(class_descriptions)
review_embeddings = create_embeddings(reviews)
```

#### Classifying review sentiment

Now that you've calculated the embeddings, it's time to compute the cosine distances and extract the most similar label.

```python
# Define a function to return the minimum distance and its index
def find_closest(query_vector, embeddings):
  distances = []
  for index, embedding in enumerate(embeddings):
    dist = distance.cosine(query_vector, embedding)
    distances.append({"distance": dist, "index": index})
  return min(distances, key=lambda x: x["distance"])

for index, review in enumerate(reviews):
  # Find the closest distance and its index using find_closest()
  closest = find_closest(review_embeddings[index], class_embeddings)
  # Subset sentiments using the index from closest
  label = sentiments[closest['index']]['label']
  print(f'"{review}" was classified as {label}')
```

#### Embedding more detailed descriptions

One of the last predicted labels didn't seem representative of the review; this was probably down to the lack of information being captured when we're only embedding the class labels. This time, descriptions of each class will be embedded instead, so the model better "understands" that you're classifying restaurant reviews.

```python
# Extract and embed the descriptions from sentiments
class_descriptions = [sentiment['description'] for sentiment in sentiments]
class_embeddings = create_embeddings(class_descriptions)
review_embeddings = create_embeddings(reviews)

def find_closest(query_vector, embeddings):
  distances = []
  for index, embedding in enumerate(embeddings):
    dist = distance.cosine(query_vector, embedding)
    distances.append({"distance": dist, "index": index})
  return min(distances, key=lambda x: x["distance"])

for index, review in enumerate(reviews):
  closest = find_closest(review_embeddings[index], class_embeddings)
  label = sentiments[closest['index']]['label']
  print(f'"{review}" was classified as {label}')
```

## Vector Databases

### Creating vector databases with ChormaDB

#### Getting started with ChromaDB

In the following exercises, you'll use a vector database to embed and query 1000 films and TV shows from the Netflix dataset introduced in the video. The goal will be to use this data to generate recommendations based on a search query. To get started, you'll create the database and collection to store the data.

```python
# Create a persistant client
client = chromadb.PersistentClient()

# Create a netflix_title collection using the OpenAI Embedding function
collection = client.create_collection(
    name="netflix_titles",
    embedding_function=OpenAIEmbeddingFunction(model_name="text-embedding-3-small", api_key="<OPENAI_API_TOKEN>")
)

# List the collections
print(client.list_collections())
```

#### Estimating embedding costs with tiktoken

Now that we've created a database and collection to store the Netflix films and TV shows, we can begin embedding data.

Before embedding a large dataset, it's important to do a cost estimate to ensure you don't go over any budget restraints. Because OpenAI models are priced by number of tokens inputted, we'll use OpenAI's `tiktoken` library to count the number of tokens and convert them into a dollar cost.

```python
# Load the encoder for the OpenAI text-embedding-3-small model
enc = tiktoken.encoding_for_model("text-embedding-3-small")

# Encode each text in documents and calculate the total tokens
total_tokens = sum(len(enc.encode(text)) for text in documents)

cost_per_1k_tokens = 0.00002

# Display number of tokens and cost
print('Total tokens:', total_tokens)
print('Cost:', cost_per_1k_tokens * total_tokens/1000)
```

#### **Adding data to the collection**

```python
# Recreate the netflix_titles collection
collection = client.create_collection(
  name="netflix_titles",
  embedding_function=OpenAIEmbeddingFunction(model_name="text-embedding-3-small", api_key="<OPENAI_API_TOKEN>")
)

# Add the documents and IDs to the collection
collection.add(ids=ids, documents=documents)

# Print the collection size and first ten items
print(f"No. of documents: {collection.count()}")
print(f"First ten documents: {collection.peek()}")
```

### Querying and updating database

#### Querying the Netflix collection

Now that you've created and populated the `netflix_titles` collection, it's time to query it!

As a first trial, you'll use it to provide recommendations for films and TV shows about dogs to one of your colleagues who loves dogs!

```python
# Retrieve the netflix_titles collection
collection = client.get_collection(
  name="netflix_titles",
  embedding_function=OpenAIEmbeddingFunction(model_name="text-embedding-3-small", api_key="<OPENAI_API_TOKEN>")
)

# Query the collection for "films about dogs"
result = collection.query(
  query_texts=["films about dogs"],
  n_results=3
)

print(result)
```

#### Updating and deleting items from a collection

Just because the documents have been stored away in a vector database, that  
doesn't mean that you can't make changes to add to the collection or update existing items.

```python
collection = client.get_collection(
  name="netflix_titles",
  embedding_function=OpenAIEmbeddingFunction(model_name="text-embedding-3-small", api_key="<OPENAI_API_TOKEN>")
)

# Update or add the new documents
collection.upsert(
    ids=[doc['id'] for doc in new_data],
    documents=[doc['document'] for doc in new_data]
)

# Delete the item with ID "s95" and re-run the query
collection.delete(ids=["s95"])

result = collection.query(
    query_texts=["films about dogs"],
    n_results=3
)
print(result)
```

### Multiple queries and filtering

#### Querying with multiple texts

In many cases, you'll want to query the vector database using multiple query texts. Recall that these query texts are embedded using the same embedding function as when the documents were added.

```python
collection = client.get_collection(
  name="netflix_titles",
  embedding_function=OpenAIEmbeddingFunction(model_name="text-embedding-3-small", api_key="<OPENAI_API_TOKEN>")
)

reference_ids = ['s999', 's1000']

# Retrieve the documents for the reference_ids
reference_texts = collection.get(ids=reference_ids)['documents']

# Query using reference_texts
result = collection.query(
  query_texts=reference_texts,
  n_results=3
)

print(result['documents'])
```

#### Filtering using metadata

Having metadata available to use in the database can unlock the ability to more easily filter results based on additional conditions. Imagine that the film recommendations you've be creating could access the user's set preferences and use those to further filter the results.

```python
collection = client.get_collection(
  name="netflix_titles",
  embedding_function=OpenAIEmbeddingFunction(model_name="text-embedding-3-small", api_key="<OPENAI_API_TOKEN>")
)

reference_texts = ["children's story about a car", "lions"]

# Query two results using reference_texts
result = collection.query(
  query_texts=reference_texts,
  n_results=2,
  # Filter for titles with a G rating released before 2019
  where={
    "$and": [
        {"rating": 
        	{"$eq": "G"}
        },
        {"release_year": 
         	{"$lt": 2019}
        }
    ]
  }
)

print(result['documents'])
```