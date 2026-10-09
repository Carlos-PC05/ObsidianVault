#AI 
# Slides

[chapter1_compressed.pdf](attachment:7bd2be5b-0576-4578-8e2b-97d224cf3b65:chapter1_compressed.pdf)

[chapter2_compressed.pdf](attachment:8266d9d5-9ee8-422c-a626-b5de1c1422fd:chapter2_compressed.pdf)

[chapter3_compressed.pdf](attachment:824ea6e3-a3fe-4586-a96b-5746c4abd32b:chapter3_compressed.pdf)

# Notes

## **Introduction to LangChain & Chatbot Mechanics**

### The LangChain ecosystem

#### OpenAI models in LangChain!

OpenAI's GPT-series models are some of the highest performing LLMs around. They  
are available via OpenAI's API, which you can easily interact with using LangChain.

Normally, using OpenAI's models would require a personal API key used for billing the cost of the models. In this course, **you do not need to create or provide an OpenAI API key.** The `"<OPENAI_API_TOKEN>"` placeholder will send valid requests to the API. If you make large number of requests in a short period, you may encounter a `RateLimitError`. If you see this, please pause for a moment and try again.

```python
# Define the LLM
llm = ChatOpenAI(model="gpt-4o-mini", api_key="<OPENAI_API_TOKEN>")

# Predict the words following the text in question
prompt = 'Three reasons for using LangChain for LLM application development.'
response = llm.invoke(prompt)

print(response.content)
```

#### Hugging Face models in LangChain!

There are thousands of models freely available to download and use on [Hugging Face](https://huggingface.co/models). Hugging Face integrates really nicely into LangChain via its partner library, `langchain-huggingface`, which is available for you to use.

```python
# Import the class for defining Hugging Face pipelines
from langchain_huggingface import HuggingFacePipeline

# Define the LLM from the Hugging Face model ID
llm = HuggingFacePipeline.from_model_id(
    model_id="crumb/nano-mistral",
    task="text-generation",
    pipeline_kwargs={"max_new_tokens": 20}
)

prompt = "Hugging Face is"

# Invoke the model
response = llm.invoke(prompt)
print(response)
```

### Prompt templates

#### Prompt templates and chaining

In this exercise, you'll begin using two of the core components in LangChain: **prompt templates** and **chains**!

```python
# Create a prompt template from the template string
template = "You are an artificial intelligence assistant, answer the question. {question}"
prompt = PromptTemplate.from_template(
    template=template
)

llm = ChatOpenAI(model="gpt-4o-mini", api_key='<OPENAI_API_TOKEN>')	

# Create a chain to integrate the prompt template and LLM
llm_chain = prompt | llm

# Invoke the chain on the question
question = "How does LangChain make LLM application development easier?"
print(llm_chain.invoke({"question": question}))
```

#### Chat prompt templates

Given the importance of chat models in many LLM applications, LangChain provides functionality for creating prompt templates to structure messages to different chat _roles_.

```python
llm = ChatOpenAI(model="gpt-4o-mini", api_key='<OPENAI_API_TOKEN>')		

# Create a chat prompt template
prompt_template = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a geography expert that returns the colors present in a country's flag."),
        ("human", "France"),
        ("ai", "blue, white, red"),
        ("human", "{country}")
    ]
)

# Chain the prompt template and model, and invoke the chain
llm_chain = prompt_template | llm

country = "Japan"
response = llm_chain.invoke({"country": country})
print(response.content)
```

### Few-shot prompting

#### Creating the few-shot example set

`PromptTemplate` and `ChatPromptTemplate` are great for integrating variables, but struggle with integrating datasets containing many examples. This is where `FewShotPromptTemplate`  
comes in! In this exercise, you'll create a dataset, in the form of a list of dictionaries, to contain the follow question-answer pairs.

```python
# Create the examples list of dicts
examples = [
  {
    "question": "How many DataCamp courses has Jack completed?",
    "answer": "36"
  },
  {
    "question": "How much XP does Jack have on DataCamp?",
    "answer": "284,320XP"
  },
  {
    "question": "What technology does Jack learn about most on DataCamp?",
    "answer": "Python"
  }
]
```

#### Building the few-shot prompt template

With your examples in a structured format, it's now time to create the few-shot prompt template! You'll create a template that converts the question-answer pairs into the following format:

```
Question: Example question
Example Answer
```

```python
# Complete the prompt for formatting answers
example_prompt = PromptTemplate.from_template("Question: {question}\n{answer}")

# Create the few-shot prompt
prompt_template = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    suffix="Question: {input}",
    input_variables=["input"],
)

prompt = prompt_template.invoke({"input": "What is Jack's favorite technology on DataCamp?"})
print(prompt.text)
```

#### Implementing few-shot prompting

Time to combine your components together into a chain! The few-shot prompt  
you created in the previous exercise is still available for you to use, along with `examples` and `example_prompt`.

```python
prompt_template = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    suffix="Question: {input}",
    input_variables=["input"],
)

# Create an OpenAI chat LLM
llm = ChatOpenAI(model="gpt-4o-mini", api_key='<OPENAI_API_TOKEN>')

# Create and invoke the chain
llm_chain = prompt_template | llm
print(llm_chain.invoke({"input": "What is Jack's favorite technology on DataCamp?"}))
```

## Chains and Agents

### Sequential chains

#### Building prompts for sequential chains

Over the next couple of exercises, you'll work to create a system for helping people learn new skills. This system needs to be built sequentially, so learners can modify plans based on their preferences and constraints. You'll utilize your LangChain LCEL skills to build a  
sequential chain to build this system, and the first step is to design the **prompt templates** that will be used by this system.

```python
# Create a prompt template that takes an input activity
learning_prompt = PromptTemplate(
    input_variables=["activity"],
    template="I want to learn how to {activity}. Can you suggest how I can learn this step-by-step?"
)

# Create a prompt template that places a time constraint on the output
time_prompt = PromptTemplate(
    input_variables=["learning_plan"],
    template="I only have one week. Can you create a plan to help me hit this goal: {learning_plan}."
)

# Invoke the learning_prompt with an activity
print(learning_prompt.invoke({"activity": "play golf"}))
```

#### Sequential chains with LCEL

With your prompt templates created, it's time to tie everything together, including the LLM, using chains and LCEL. An `llm` has already been defined for you that uses OpenAI's `gpt-4o-mini` model

```python
learning_prompt = PromptTemplate(
    input_variables=["activity"],
    template="I want to learn how to {activity}. Can you suggest how I can learn this step-by-step?"
)

time_prompt = PromptTemplate(
    input_variables=["learning_plan"],
    template="I only have one week. Can you create a concise plan to help me hit this goal: {learning_plan}."
)

# Complete the sequential chain with LCEL
seq_chain = ({"learning_plan": learning_prompt | llm | StrOutputParser()}
    | time_prompt
    | llm
    | StrOutputParser())

# Call the chain
print(seq_chain.invoke({"activity": "play the harmonica"}))
```

### Introduction to LangChain agents

#### ReAct agents

Time to have a go at creating your own ReAct agent! Recall that ReAct stands for Reason and Act, which describes how they make decisions. In this exercise, you'll load the built-in `wikipedia` tool to integrate external data from Wikipedia with your LLM. An `llm` has already been defined for you that uses OpenAI's `gpt-4o-mini` model

```python
# Define the tools
tools = load_tools(["wikipedia"])

# Define the agent
agent = create_react_agent(llm, tools)

# Invoke the agent
response = agent.invoke({"messages": [("human", "How many people live in New York City?")]})
print(response['messages'][-1].content)
```

### Custom tools for agents

#### Defining a function for tool use

You're working for a SaaS (software as a service) company with big goals for rolling out tools to help employees at all levels of the organization to make data-informed decisions. You're creating a PoC for an application that allows customer success managers to interface with company data using natural language to retrieve important customer data.

```python
# Define a function to retrieve customer info by-name
def retrieve_customer_info(name: str) -> str:
    """Retrieve customer information based on their name."""
    # Filter customers for the customer's name
    customer_info = customers[customers['name'] == name]
    return customer_info.to_string()
  
# Call the function on Peak Performance Co.
print(retrieve_customer_info("Peak Performance Co."))
```

#### Creating custom tools

Now that you have a function for extracting customer data from the `customers` DataFrame, it's time to convert this function into a tool that's compatible with LangChain agents.

```python
# Convert the retrieve_customer_info function into a tool
@tool
def retrieve_customer_info(name: str) -> str:
    """Retrieve customer information based on their name."""
    customer_info = customers[customers['name'] == name]
    return customer_info.to_string()
  
# Print the tool's arguments
print(retrieve_customer_info.args)
```

#### Integrating custom tools with agents

Now that you have your tools at-hand, it's time to set up your agentic workflow! You'll again be using a ReAct agent, which, recall, reasons on the steps it should take, and selects tools using this context and the tool descriptions. An `llm` has already been defined for you that uses OpenAI's `gpt-4o-mini` model

```python
@tool
def retrieve_customer_info(name: str) -> str:
    """Retrieve customer information based on their name."""
    customer_info = customers[customers['name'] == name]
    return customer_info.to_string()

# Create a ReAct agent
agent = create_react_agent(llm, [retrieve_customer_info])

# Invoke the agent on the input
messages = agent.invoke({"messages": [("human", "Create a summary of our customer: Peak Performance Co.")]})
print(messages['messages'][-1].content)
```

## Retrieval Augmented Generation (RAG)

### Integrating document loaders

#### PDF document loaders

To begin implementing Retrieval Augmented Generation (RAG), you'll first need to load the documents that the model will access. These documents can come from a variety of sources, and LangChain supports document loaders for many of them.

```python
# Import library
from langchain_community.document_loaders import PyPDFLoader

# Create a document loader for rag_vs_fine_tuning.pdf
loader = PyPDFLoader('rag_vs_fine_tuning.pdf')

# Load the document
data = loader.load()
print(data[0])
```

#### CSV document loaders

Comma-separated value (CSV) files are an extremely common file format, particularly in  
data-related fields. Fortunately, LangChain provides different document loaders for different formats, keeping almost all of the syntax the same!

```python
# Import library
from langchain_community.document_loaders.csv_loader import CSVLoader

# Create a document loader for fifa_countries_audience.csv
loader = CSVLoader('fifa_countries_audience.csv')

# Load the document
data = loader.load()
print(data[0])
```

#### HTML document loaders

It's possible to load documents from many different formats, including complex formats like HTML.

```python
from langchain_community.document_loaders import UnstructuredHTMLLoader 

# Create a document loader for unstructured HTML
loader = UnstructuredHTMLLoader("white_house_executive_order_nov_2023.html")

# Load the document
data = loader.load()

# Print the first document
print(data[0])

# Print the first document's metadata
print(data[0].metadata)
```

### Splitting external data for retrieval

### Splitting by character

A key process in implementing Retrieval Augmented Generation (**RAG**) is splitting documents into chunks for storage in a vector database.

There are several splitting strategies available in LangChain, some with more complex routines than others. In this exercise, you'll implement a _character text splitter_, which splits documents based on characters and measures the chunk length by the number of characters.

Remember that there is no ideal splitting strategy, you may need to experiment with a few to find the right one for your use case.

```python
# Import the character splitter
from langchain_text_splitters import CharacterTextSplitter

quote = 'Words are flowing out like endless rain into a paper cup,\nthey slither while they pass,\nthey slip away across the universe.'
chunk_size = 24
chunk_overlap = 10

# Create an instance of the splitter class
splitter = CharacterTextSplitter(
    separator='\n',
    chunk_size=chunk_size,
    chunk_overlap=chunk_overlap)

# Split the string and print the chunks
docs = splitter.split_text(quote) 
print(docs)
print([len(doc) for doc in docs])
```

#### Recursively splitting by character

Many developers are using a recursive character splitter to split documents based on a specific list of characters. These characters are paragraphs, newlines, spaces, and empty strings, by default: `["\n\n", "\n", " ", ""]`.

Effectively, the splitter tries to split by paragraphs, checks to see if the `chunk_size` and `chunk_overlap` values are met, and if not, splits by sentences, then words, and individual characters.

```python
# Import the recursive character splitter
from langchain_text_splitters import RecursiveCharacterTextSplitter

quote = 'Words are flowing out like endless rain into a paper cup,\nthey slither while they pass,\nthey slip away across the universe.'
chunk_size = 24
chunk_overlap = 10

# Create an instance of the splitter class
splitter = RecursiveCharacterTextSplitter(
    separators=["\n", " ", ""],
    chunk_size=chunk_size,
    chunk_overlap=chunk_overlap)

# Split the document and print the chunks
docs = splitter.split_text(quote) 
print(docs)
print([len(doc) for doc in docs])
```

#### Splitting HTML

In this exercise, you'll split an HTML containing an executive order on AI created by the US White House in October 2023. To retain as much context as possible in the chunks, you'll split using larger `chunk_size` and `chunk_overlap` values.

```python
# Load the HTML document into memory
loader = UnstructuredHTMLLoader("white_house_executive_order_nov_2023.html")
data = loader.load()

# Define variables
chunk_size = 300
chunk_overlap = 100

# Split the HTML
splitter = RecursiveCharacterTextSplitter(
    chunk_size=chunk_size,
    chunk_overlap=chunk_overlap,
    separators=['.'])

docs = splitter.split_documents(data) 
print(docs)
```

### RAG storage and retrueval using vector databases

#### Preparing the documents and vector database

Over the next few exercises, you'll build a full RAG workflow to have a conversation with a PDF document containing the paper, _RAG VS Fine-Tuning: Pipelines, Tradeoffs, and a Case Study on Agriculture_ by Balaguer et al. (2024). This works by splitting the documents into chunks, storing them in a vector database, defining a prompt to connect  
the retrieved documents and user input, and building a retrieval chain for the LLM to access this external data.

```python
loader = PyPDFLoader('rag_vs_fine_tuning.pdf')
data = loader.load()

# Split the document using RecursiveCharacterTextSplitter
splitter = RecursiveCharacterTextSplitter(
    chunk_size=300,
    chunk_overlap=50)
docs = splitter.split_documents(data) 

# Embed the documents in a persistent Chroma vector database
embedding_function = OpenAIEmbeddings(api_key='<OPENAI_API_TOKEN>', model='text-embedding-3-small')
vectorstore = Chroma.from_documents(
    docs,
    embedding=embedding_function,
    persist_directory=os.getcwd()
)

# Configure the vector store as a retriever
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}
)
```

#### Building a retrieval prompt template

Now your documents have been ingested into vector database and are ready for retrieval, you'll need to design a chat prompt template to combine the retrieved document chunks with the user input question.

```python
# Add placeholders to the message string
message = """
Answer the following question using the context provided:

Context:
{context}

Question:
{question}

Answer:
"""

# Create a chat prompt template from the message string
prompt_template = ChatPromptTemplate.from_messages([("human", message)])
```

#### Creating a RAG chain

Now to bring all the components together in your RAG workflow! You've prepared the documents and ingested them into a Chroma database for retrieval. You created a prompt template to include the retrieved chunks from the academic paper and answer questions.

```python
vectorstore = Chroma.from_documents(
    docs,
    embedding=OpenAIEmbeddings(api_key='<OPENAI_API_TOKEN>', model='text-embedding-3-small'),
    persist_directory=os.getcwd()
)

retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}
)

# Create a chain to link retriever, prompt_template, and llm
rag_chain = ({"context": retriever, "question": RunnablePassthrough()}
            | prompt_template
            | llm)

# Invoke the chain
response = rag_chain.invoke("Which popular LLMs were considered in the paper?")
print(response.content)
```