#AI
# Slides

[chapter1_compressed.pdf](attachment:b23b5696-2570-4a8f-9ad6-0786a9da021e:chapter1_compressed.pdf)

[chapter2_compressed.pdf](attachment:587b52e2-578f-4a7a-893c-73bb85ab608b:chapter2_compressed.pdf)

# Notes

## Getting started with hugging face

### Running Hugging Face models

#### Building a text generation pipeline

Hugging Face pipelines make it simple to use machine learning models for a variety of tasks. In this exercise, you'll build **a text generation pipeline** using the `gpt2` model and customize the output by adjusting its parameters.

Feel free to experiment with different prompts in the pipeline, such as _"What if …?"_, _"How to …?"_, or any other creative idea you'd like to explore.

```python
from transformers import pipeline 

gpt2_pipeline = pipeline(task="text-generation", model="openai-community/gpt2")

# Generate three text outputs with a maximum length of 10 tokens
results = gpt2_pipeline("What if AI", max_new_tokens=10, num_return_sequences=2)

for result in results:
    print(result['generated_text'])
```

### Hugging Face Datasets

#### Loading datasets

After you've chosen your dataset, it's time to load it with the `datasets` library!

In this case, you'll load the `"validation"` split of the `"TIGER-Lab/MMLU-Pro"` dataset, which is a benchmark evaluation dataset.

The `load_dataset` module from the `datasets` package is already loaded for you.

```python
# Load the "validation" split of the TIGER-Lab/MMLU-Pro dataset
my_dataset = load_dataset("TIGER-Lab/MMLU-Pro", split="validation")

# Display dataset details
print(my_dataset)
```

#### Manipulating datasets

There will likely be many occasions when you will need to manipulate a dataset before using it within a ML task. Two common manipulations are filtering and selecting (or slicing). Given the size of these datasets, Hugging Face leverages arrow file types.

This means performing manipulations are slightly different than what you might be used to. Fortunately, there's already methods to help with this!

The dataset is already loaded for you under `wikipedia`.

```python
# Filter the documents
filtered = wikipedia.filter(lambda row: "football" in row["text"])

# Create a sample dataset
example = filtered.select(range(1))

print(example[0]["text"])
```

## Building Pipelines with Hugging face

### Text classification

#### Grammatical correctness

Text classification is the process of labeling an input text into a pre-defined category. This can take the form of sentiment - `positive` or `negative` - spam detection - `spam` or `not spam` - and even grammatical errors.

Explore the use of a `text-classification` pipeline for checking an input sentence for grammatical errors.

```python
# Create a pipeline for grammar checking
grammar_checker = pipeline(
  "text-classification", 
  model="abdulmatinomotoso/English_Grammar_Checker"
)

# Check grammar of the input text
output = grammar_checker("I will walk dog")
print(output)
```

#### Question Natural Language Inference

Another task under the text classification umbrella is Question Natural Language Inference, or QNLI. This checks if a premise contains enough information to answer a posed question, determining whether the answer can be found in the given text.

Performing different tasks with the `text-classification` pipeline can be done by choosing different models. Each model is trained to predict specific labels and optimized for learning different context within a text.

```python
# Create the pipeline
classifier = pipeline("text-classification", model="cross-encoder/qnli-electra-base")

# Predict the output
output = classifier("Where is the capital of France?, Brittany is known for its stunning coastline.")

print(output)
```

#### Dynamic category assignment

Dynamic category assignment enables a model to classify text into predefined categories, even without prior training for those categories.

Using Hugging Face’s `pipeline()` for the `zero-shot-classification` task, provide the text and predefined categories to identify the best match.

Build a classifier to predict the label for the input `text`, which is a news headline already loaded for you.

The `pipelines` from the `transformers` library is preloaded for your convenience.

**Note:** We are using a customized version of the pipeline to help you learn how to use these functions without needing to download the model.

```python
text = "AI-powered robots assist in complex brain surgeries with precision."

# Create the pipeline
classifier = pipeline("zero-shot-classification", model="facebook/bart-large-mnli")

# Create the categories list
categories = ["politics", "science", "sports"]

# Predict the output
output = classifier(text, categories)

# Print the top label and its score
print(f"Top Label: {output['labels'][0]} with score: {output['scores'][0]}")
```

### Text summarization

#### Summarizing long text

Summarization reduces large text into manageable content, helping readers quickly grasp key points from lengthy articles or documents.

There are two main types: **extractive**, which selects key sentences from the original text, and **abstractive**, which generates new sentences summarizing main ideas.

In this exercise, you’ll create an **abstractive summarization pipeline** using Hugging Face's `pipeline()` function and the `cnicu/t5-small-booksum` model. You’ll summarize text from a Wikipedia page on Greece, comparing the abstractive model's rephrased output to the original.

```python
# Create the summarization pipeline
summarizer = pipeline(task="summarization", model="cnicu/t5-small-booksum")

# Summarize the text
summary_text = summarizer(original_text)

# Compare the length
print(f"Original text length: {len(original_text)}")
print(f"Summary length: {len(summary_text[0]['summary_text'])}")
```

#### Adjusting the summary length

The `pipeline()` function, has two important parameters: `min_new_tokens` and `max_new_tokens`.  
These are useful for adjusting the length of the resulting summary text to be short, longer, or within a certain number of words. You might want to do this if there are space constraints (i.e., small storage), to enhance readability, or improve the quality of the summary.

You'll experiment with a short and long summarizer by setting these two parameters to a small range, then a wider range.

```python
# Repeat for a summary between 50 and 150 tokens
long_summarizer = pipeline(task="summarization", model="cnicu/t5-small-booksum", min_new_tokens=50, max_new_tokens=150)

long_summary_text = long_summarizer(original_text)

print(long_summary_text[0]["summary_text"])
```

### Auto Models and Tokenizers

#### Tokenizing text with AutoTokenizer

AutoTokenizers simplify text preparation by automatically handling cleaning, normalization, and tokenization. They ensure the text is processed just as the model expects.

In this exercise, explore how AutoTokenizer transforms text into tokens ready for machine learning tasks.

```python
# Import necessary library for tokenization
from transformers import AutoTokenizer

# Load the tokenizer
tokenizer = AutoTokenizer.from_pretrained("distilbert-base-uncased-finetuned-sst-2-english")

# Split input text into tokens
tokens = tokenizer.tokenize("AI: Making robots smarter and humans lazier!")

# Display the tokenized output
print(f"Tokenized output: {tokens}")
```

#### Using AutoClasses

You’ve seen how tokenizers work and explored their role in preparing text for models. Now, let’s take it a step further by combining AutoModels and AutoTokenizers with the `pipeline()` function. It's a nice balance of control and convenience.

Continue with the sentiment analysis task and combine AutoClasses with the pipeline module.

```python
# Download the model and tokenizer
my_model = AutoModelForSequenceClassification.from_pretrained("distilbert-base-uncased-finetuned-sst-2-english")
my_tokenizer = AutoTokenizer.from_pretrained("distilbert-base-uncased-finetuned-sst-2-english")

# Create the pipeline
my_pipeline = pipeline(task="sentiment-analysis", model = my_model, tokenizer=my_tokenizer)

# Predict the sentiment
output = my_pipeline("This course is pretty good, I guess.")
print(f"Sentiment using AutoClasses: {output[0]['label']}")
```

### Document Q&A

#### Extracting text with PyPDF

PyPDF lets us extract text from PDFs, making it easy to work with multi-page documents like policy files.

In this exercise, you’ll load the `US_Employee_Policy.pdf`, extract its content page by page, and combine it into a single string, preparing the text for a question-answering pipeline.

```python
from pypdf import PdfReader

# Extract text from the PDF
reader = PdfReader("US_Employee_Policy.pdf")

# Extract text from all pages
document_text = ""
for page in reader.pages: 
    document_text += page.extract_text()

print(document_text)
```

#### Building a Q&A pipeline

In the previous exercise, we extracted text from a multi-page document using PyPDF and prepared it as a single string.

Now, you’ll build **a question-answering pipeline** using Hugging Face to retrieve specific answers from the document.

```python
# Load the question-answering pipeline
qa_pipeline = pipeline(task="question-answering", model="distilbert-base-cased-distilled-squad")

question = "What is the notice period for resignation?"

# Get the answer from the QA pipeline
result = qa_pipeline(question=question, context=document_text)

# Print the answer
print(f"Answer: {result['answer']}")
```