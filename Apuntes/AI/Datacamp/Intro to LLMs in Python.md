#AI 
# Slides

[chapter1](attachment:201a7e65-4668-4e7c-9902-9329e894a552:chapter1_\(2\).pdf)

[chapter2](attachment:456dc889-0680-45a0-ab29-626c34ffbc6a:chapter2_compressed.pdf)

[chapter3](attachment:9f2da21a-df96-4daa-9058-13fb5cd419b0:chapter3_compressed.pdf)

# Notes

## Getting Started with LLMs

### Introduction to LLMs

#### Using a pipeline for summarization

Run a summarization pipeline using the `"cnicu/t5-small-booksum"` model from the Hugging Face hub.

```python
# Load the model pipeline
summarizer = pipeline(task='summarization', model='cnicu/t5-small-booksum')

# Pass the long text to the model
output = summarizer(long_text, max_length=50)

# Access and print the summarized text
print(output[0]["summary_text"])
```

### Using pre-trained LLMs

#### Generating text

LLMs have many capabilities with text generation being one of the most popular.

You need to generate a response to a customer review found in `text`; it contains the same customer review for the _Riverview Hotel_ you've seen before.

```python
# Instantiate the pipeline
generator = pipeline(task='text-generation', model="gpt2")

response = "Dear valued customer, I am glad to hear you had a good stay with us."

# Complete the prompt
prompt = f"Customer review:\n{text}\n\nHotel reponse to the customer:\n{response}"

# Complete the model pipeline
outputs = generator(prompt, max_length=150, pad_token_id=generator.tokenizer.eos_token_id, truncation=True)

print(outputs[0]["generated_text"])
```

#### Translating text

You've seen an example of English-to-Spanish translation. Now it's your turn to try it the other way around with Spanish-to-English translation.

```python
spanish_text = "Este curso sobre LLMs se está poniendo muy interesante"

# Define the pipeline
translator = pipeline(task='translation_es_to_en', model="Helsinki-NLP/opus-mt-es-en")

# Translate the Spanish text
translations = translator(spanish_text, clean_up_tokenization_spaces=True)

print(translations[0]["translation_text"])
```

### Understanding the transformer

#### Using the correct model structure

Question-answering can be either extractive or generative, each requiring a different  
transformer structure to process input and output correctly.

They use either:

- Encoder-only models such as `"distilbert-base-uncased-distilled-squad"`
- Decoder-only models such as `"gpt2"`

Use your knowledge of common models for specific tasks to select the appropriate one. pipeline is loaded, as well as text on the Mona Lisa.

```python
question = "Who painted the Mona Lisa?"

# Define the appropriate model
qa = pipeline(task="question-answering", model='gpt2')

input_text = f"Context: {text}\n\nQuestion: {question}\n\nAnswer:"

output = qa({"context": text, "question": question}, max_length=150)
print(output['answer'])
```

## Fine-Tuning LLMs

### Preparing for fine-tuning

#### Tokenizing text

You want to leverage a pre-trained model from Hugging Face and fine-tune it with data from your company support team to help classify interactions depending on the risk for churn. This will help the team prioritize what to address first, and how to address it, making them more proactive.

Prepare the training and test data for fine-tuning by tokenizing the text.

```python
# Load the model and tokenizer
model = AutoModelForSequenceClassification.from_pretrained("distilbert-base-uncased")
tokenizer = AutoTokenizer.from_pretrained("distilbert-base-uncased")

# Tokenize the data
tokenized_training_data = tokenizer(train_data["interaction"], return_tensors="pt", padding = True, truncation = True, max_length=20)

tokenized_test_data = tokenizer(test_data["interaction"], return_tensors="pt", padding = True, truncation = True, max_length=20)

print(tokenized_training_data)
```

#### Mapping tokenization

You now want to test out having more control over the tokenization and want to try tokenizing the data in rows or batches. This will also give you a result that is a DataSet object, which you'll need for training.

```python
# Complete the function
def tokenize_function(data):
    return tokenizer(data["interaction"], 
                     return_tensors="pt", 
                     padding=True, 
                     truncation=True, 
                     max_length=64)

# Tokenize row by row
tokenized_by_row = train_data.map(tokenize_function, batched = False)

print(tokenized_by_row)
```

### Fine-tuning through training

#### Setting up training arguments

After tokenizing your customer support interactions, you need to set up your training arguments before you can fine-tuned a pre-trained model.

```python
# Set up an instance of TrainingArguments
training_args = TrainingArguments(
  output_dir="./finetuned",
  # Set the evaluation strategy
  evaluation_strategy = 'epoch',
  # Specify the number of epochs
  num_train_epochs=3,
  learning_rate=2e-5,
  # Set the batch sizes
  per_device_train_batch_size=3,
  per_device_eval_batch_size=3,
  weight_decay=0.01
)
```

#### Setting up the trainer

With training arguments set up, you need to move on to the trainer before putting the fine-tuned model to use.

```python
# Set up the trainer object
trainer = Trainer(
    model=model,
    # Assign the training arguments and tokenizer
    args=training_args,
    train_dataset=tokenized_training_data,
    eval_dataset=tokenized_test_data,
    tokenizer=tokenizer
)

# Train the model
trainer.train()
```

#### Using the fine-tuned model

The model has been fine-tuned. Now you ready to use on some new data and generate some classifications. Let's see how well your fine-tuned model does at tagging new interactions as either low or high risk for churn.

```python
input_text = ["I'd just like to say, I love the product! Thank you!"]

# Tokenize the new data
inputs = tokenizer(input_text, return_tensors="pt", padding=True, truncation=True)

# Pass the tokenized inputs through the model
with torch.no_grad():
    outputs = model(**inputs)

# Extract the new predictions
predicted_labels = torch.argmax(outputs.logits, dim=1).tolist()

label_map = {0: "Low risk", 1: "High risk"}
for i, predicted_label in enumerate(predicted_labels):
    churn_label = label_map[predicted_label]
    print(f"\n Input Text {i + 1}: {input_text[i]}")
    print(f"Predicted Label: {predicted_label}")
```

### Fine-tuning approaches

#### Transfer learning with one-shot learning

Transfer learning has many approaches, one of them being one-shot learning, where a model is trained using one example it has seen.

```python
# Include an example in the input ext
input_text = """
Text: "The dinner we had was great and the service too."
Classify the sentiment of this sentence as either positive or negative.
Example:
Text: "The food was delicious"
Sentiment: Positive
Text: "The dinner we had was great and the service too."
Sentiment:
"""

# Apply the example to the model
result = model(input_text, max_length=100)

print(result[0]["label"])
```

## Evaluating LLM performace

### The evaluate library

#### Loading metrics with evaluate

Metrics are needed to evaluate the performance of LLMs, similar to classic machine learning models. The `evaluate` library helps to understand the complexities of LLMs. Start by loading the metrics that can be used to assess classification models.

```python
# Load the metrics
accuracy = evaluate.load("accuracy")
precision = evaluate.load("precision")
recall = evaluate.load("recall")
f1 = evaluate.load("f1")
```

#### Describing metrics

It's never a bad time to revise the definitions of some popular metrics.

```python
# Obtain a description of each metric
print(accuracy.description)
print(precision.description)
print(recall.description)
print(f1.description)

------------------

# See the required data types
print(f"The required data types for accuracy are: {accuracy.features}.")
print(f"The required data types for precision are: {precision.features}.")
print(f"The required data types for recall are: {recall.features}.")
print(f"The required data types for f1 are: {f1.features}.")
```

#### Using evaluate metrics

It's time to evaluate your LLM that classifies customer support interactions. Picking up from where you left your fine-tuned model, you'll now use a new validation dataset to assess the performance of  
your model.

```python
accuracy = evaluate.load("accuracy")
precision = evaluate.load("precision")
recall = evaluate.load("recall")
f1 = evaluate.load("f1")

# Extract the new predictions
predicted_labels = torch.argmax(outputs.logits, dim=1).tolist()

# Compute the metrics by comparing real and predicted labels
print(accuracy.compute(references=validate_labels, predictions=predicted_labels))
print(precision.compute(references=validate_labels, predictions=predicted_labels))
print(recall.compute(references=validate_labels, predictions=predicted_labels))
print(f1.compute(references=validate_labels, predictions=predicted_labels))
```

### Metrics for language tasks: perplexity and BLEU

#### Evaluating perplexity

Try your had at generating text and evaluating the perplexity score.

You've been provided some `input_text` that is the start of a sentence: "Current trends show that by 2030 ".

Use an LLM to generate the rest of the sentence.

```python
# Encode the input text, generate and decode it
input_text_ids = tokenizer.encode(input_text, return_tensors="pt")
output = model.generate(input_text_ids, max_length=20)
generated_text = tokenizer.decode(output[0], skip_special_tokens=True)

print("Generated Text: ", generated_text)

# Load and compute the perplexity score
perplexity = evaluate.load("perplexity", module_type="metric")
results = perplexity.compute(model_id="gpt2", predictions=generated_text)
print("Perplexity: ", results['mean_perplexity'])
```

#### **BLEU translations**

Let's get familiar with the **BLEU** metric.

A pipeline based on the **Helsinki-NLP** Spanish-English translation model and the BLEU metric has been loaded for you, using `evaluate.load("bleu")` from the `evaluate` library.

Given the following inputs and references for evaluation:

```
input_sentence_1 = "Hola, ¿cómo estás?"

reference_1 = [
     ["Hello, how are you?", "Hi, how are you?"]
     ]

input_sentences_2 = ["Hola, ¿cómo estás?", "Estoy genial, gracias."]

references_2 = [
     ["Hello, how are you?", "Hi, how are you?"],
     ["I'm great, thanks.", "I'm great, thank you."]
     ]
```

```python
translator = pipeline("translation", model="Helsinki-NLP/opus-mt-es-en")

# Translate the first input sentence then calucate the BLEU metric for translation quality
translated_output = translator(input_sentence_1)

translated_sentence = translated_output[0]['translation_text']

print("Translated:", translated_sentence)

results = bleu.compute(predictions=[translated_sentence], references=reference_1)
print(results)

------------------------

# Translate the input sentences, extract the translated text, and compute BLEU score
translator = pipeline("translation", model="Helsinki-NLP/opus-mt-es-en")

translated_outputs = translator(input_sentences_2)

predictions = [translated_output['translation_text'] for translated_output in translated_outputs]
print(predictions)

results = bleu.compute(predictions = predictions, references = references_2)
print(results)
```

### Metrics for language tasks: ROGUE, METEOR, EM

#### Evaluating with ROUGE

ROUGE is commonly used to evaluate summarization tasks as it checks for similarities between predictions and references. You have been provided with a model-generated summary, `predictions`, and a `references` summary for validate. Calculate the scores to see how well the model performed.

```python
# Load the rouge metric
rouge = evaluate.load("rouge")

predictions = ["""Pluto is a dwarf planet in our solar system, located in the Kuiper Belt beyond Neptune, and was formerly considered the ninth planet until its reclassification in 2006."""]
references = ["""Pluto is a dwarf planet in the solar system, located in the Kuiper Belt beyond Neptune, and was previously deemed as a planet until it was reclassified in 2006."""]

# Calculate the rouge scores between the predicted and reference summaries
results = rouge.compute(predictions = predictions, references = references)
print("ROUGE results: ", results)
```

#### Evaluating with METEOR

METEOR excels at evaluating some of the more semantic features in text. It works similar to ROUGE by comparing a model-generated output to a reference output. You've been provided these texts as `generated` and `reference`; it's over to you to evaluate the score.

```python
meteor = evaluate.load("meteor")

generated = ["The burrow stretched forward like a narrow corridor for a while, then plunged abruptly downward, so quickly that Alice had no chance to stop herself before she was tumbling into an extremely deep shaft."]
reference = ["The rabbit-hole went straight on like a tunnel for some way, and then dipped suddenly down, so suddenly that Alice had not a moment to think about stopping herself before she found herself falling down a very deep well."]

# Compute and print the METEOR score
results = meteor.compute(predictions = generated, references = reference)
print("Meteor: ", results['meteor'])
```

#### Evaluating with EM

Exact Match helps us evaluate models when it comes to extractive question and answering but looking for, you guessed it, exact matches! Once again, you have been provided some `predictions` and `references` for evaluation. The `evaluate` library has been loaded for you.

```python
# Load the metric
exact_match = evaluate.load('exact_match')

predictions = ["It's a wonderful day", "I love dogs", "DataCamp has great AI courses", "Sunshine and flowers"]
references = ["What a wonderful day", "I love cats", "DataCamp has great AI courses", "Sunsets and flowers"]

# Compute the exact match and print the results
results = exact_match.compute(predictions = predictions, references = references)
print("EM results: ", results)
```

### Safeguarding LLMs

#### Checking toxicity

You work at a social media company and need to evaluate the comments that are being left on the platform to ensure users are respecting the code of conduct.

Your task is to carefully assess the toxicity level of comments of `user_1` and `user_2`.

```python
# Calculate the individual toxicities
toxicity_1 = toxicity_metric.compute(predictions=user_1)
toxicity_2 = toxicity_metric.compute(predictions=user_2)
print("Toxicities (user_1):", toxicity_1['toxicity'])
print("Toxicities (user_2): ", toxicity_2['toxicity'])

# Calculate the maximum toxicities
toxicity_1_max = toxicity_metric.compute(predictions=user_1, aggregation="maximum")
toxicity_2_max = toxicity_metric.compute(predictions=user_2, aggregation="maximum")
print("Maximum toxicity (user_1):", toxicity_1_max['max_toxicity'])
print("Maximum toxicity (user_2): ", toxicity_2_max['max_toxicity'])

# Calculate the toxicity ratios
toxicity_1_ratio = toxicity_metric.compute(predictions=user_1, aggregation="ratio")
toxicity_2_ratio = toxicity_metric.compute(predictions=user_2, aggregation="ratio")
print("Toxicity ratio (user_1):", toxicity_1_ratio['toxicity_ratio'])
print("Toxicity ratio (user_2): ", toxicity_2_ratio['toxicity_ratio'])
```

#### Evaluating regard

Time to use the _regard_ metric to assess polarity in two lists of sample texts associated to  
two groups of product reviewers with different domains, perspectives, and demographics.

```
group1 = ['abc are described as loyal',
          'abc are honest but kind']
group2 = ['abc are known for being confrontational',
          'abc are very blunt']
```

The `evaluate` library has been imported for you

```python
# Load the regard and regard-comparison metrics
regard = evaluate.load("regard")
regard_comp = evaluate.load("regard", "compare")

# Compute the regard (polarities) of each group separately
polarity_results_1 = regard.compute(data=group1)
print("Polarity in group 1:\n", polarity_results_1)
polarity_results_2 = regard.compute(data=group2)
print("Polarity in group 2:\n", polarity_results_2)

# Compute the relative regard between the two groups for comparison
polarity_results_comp = regard_comp.compute(data=group1, references=group2)
print("Polarity comparison between groups:\n", polarity_results_comp)
```