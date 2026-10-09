#AI 
# Slides

[chapter1](attachment:3ff413cf-d9a6-40d1-a8be-51dd261ed809:chapter1_compressed.pdf)

[chapter2](attachment:713f8b68-63b1-459b-b13b-3d975561982d:chapter2-1_compressed.pdf)

# Notes

## Understanding LLMs and Llama

### What is Llama?

#### Chatting with Llama 3

A data analytics company is developing an internal AI assistant to help employees quickly access technical information. As part of this project, they want to integrate Llama 3, and have asked you to test it.

To get started, you initialize the Llama 3 model locally and generate a response to a question.

```python
# Import the Llama class
from llama_cpp import Llama

# Initialize the Llama model
llm = Llama(model_path=llama_path)

question = "What is the most used database for data storage?"
response = llm(question)
print(response)
```

#### Extracting the response

The data analytics AI assistant must return clean and readable responses to users, and the company has asked you to extract only the relevant text from the model's response.

```python
# Extract the first choice and generated text
extracted = response["choices"][0]

# Print the extracted text
print(extracted)
```

### Tuning Llama 3 parameters

#### Guiding customer support responses

You work for an e-commerce company and are integrating Llama into a customer support assistant. The assistant answers frequently asked questions, but you've noticed that responses are too repetitive.

You need to modify decoding parameters to encourage **more varied wording** while keeping responses informative.

```python
output = llm(
		"Can I exchange an item I purchased?", 
  		# Set the temperature parameter to provide more varied responses
		temperature=0.8,
        max_tokens=15
) 

print(output['choices'][0]['text'])
```

#### Ensuring safe responses

You're configuring an internal chatbot for a medical team. To ensure consistent responses, you need to limit variability by setting a token limit and restricting token selection.

```python
output = llm(
		"What are the symptoms of strep throat?", 
  		# Set the model parameters 
      	max_tokens=10, #Limit response length
		top_k=2 #Restrict word choices
) 

print(output['choices'][0]['text'])
```

#### Generating creative copy

You're developing an AI-powered content assistant for a SaaS marketing team. The team needs to automate social media posts about their latest software updates, and you need to adjust **response diversity** so that multiple calls to the model result in different variations.

```python
output = llm(
      	"Write a tweet announcing a new analytics dashboard feature for enterprise users.", 
		max_tokens=15,
		# Set top-p to a value in the upper range for more varied responses
		top_p=0.75
	) 

print(output['choices'][0]['text'])
```

### Assigning chat roles

#### Building a conversation

The Llama model you've been working with can accept prompts in the form of messages from different **roles**. In this exercise, you'll create messages to prompt a customer support chatbot for an internet service provider.

```python
prompt = "Give me four short steps to troubleshoot my internet connection."

conv = [
	# Complete the user message
	{
        "role": "user",
	    "content": prompt
    }
]

# Pass the conversation to the model
result = llm.create_chat_completion(messages=conv, max_tokens=20)
print(result)
```

#### System messages

Your previous messages gave you a valid response, but it also allows users to ask any question they like, even if it doesn't relate to internet service support. In this exercise, you'll utilize a **system message** to steer the model into only answering customer questions about their internet service.

```python
# Add a system message to the conversation list
conv = [
	{
        "role": "system",
        "content": "You are a helpful and professional customer support assistant for an internet service provider. If the question or instruction doesn't relate to internet service, quote the response: 'Sorry, I can't answer that.'"},
	{
        "role": "user",
	    "content": "Help me decide which stocks to invest in."
    }
]

result = llm.create_chat_completion(messages=conv, max_tokens=15)
# Extract the model response from the result object
assistant_content = result["choices"][0]["message"]["content"]
print(assistant_content)
```

## Using Llama locally

### Guiding unstructured responses

#### Formatting prompts for Llama

Models can sometimes struggle to separate the task, expected output, and additional context from a long, unstructured prompt. To remedy this, you can insert clear labels to break up and differentiate this information for the model.

```python
# Add formatting to the prompt
prompt="""
Instruction: Explain the concept of gravity in simple terms.
Question: What is gravity?
Answer:
"""

# Send the prompt to the model
output = llm(prompt, max_tokens=15, stop=["Question:"]) 
print(output['choices'][0]['text'])
```

#### Few-shot prompting with Llama

You're using a Llama model to identify the sentiment of customer reviews from Google and Yelp as `Positive` or `Negative`. To ensure these labels are consistent for each review, you'll design a **few-shot prompt** containing three examples.

Here are the examples you want to provide to the model:

- I ordered from this place last night, and I'm impressed! → **Positive**
- My order was delayed by over an hour without any updates. Disappointing! → **Negative**
- The food quality is top-notch. Highly recommend! → **Positive**

```python
# Complete the few-shot prompt
prompt="""Review 1: I ordered from this place last night, and I'm impressed! 
Sentiment 1: Positive,
Review 2: My order was delayed by over an hour without any updates. Disappointing!  
Sentiment 2: Negative,
Review 3: The food quality is top-notch. Highly recommend! 
Sentiment 3: Positive,
Review 4: Delicious food, and excellent customer service! 
Sentiment 4:"""

# Send the prompt to the model with a stop word
output = llm(prompt, max_tokens=2, stop=["Review"]) 
print(output['choices'][0]['text'])
```

### Generating structured output

#### Creating a JSON inventory list

Being able to use LLMs to generate structured outputs means that they can be used to reliably exchange information between systems with minimal processing.

You'll test your prompting skills by converting a stock inventory list written in natural language into JSON format. A model and system prompt have already been defined for you to use.

```python
output = llm.create_chat_completion(
        messages=[
            {"role": "system", "content": "You convert inventory lists from text to JSON, extracting item counts and names from the text as keys and values in the form: item: count; for example, 'banana': 32.",},
            {"role": "user", "content": "Fifteen apples, thirty-three oranges, and five thousand fifty-two potatoes."},
        ],
		# Specify output format to JSON
        response_format={
            "type": 'json_object'
        }
)

print(output['choices'][0]['message']['content'])
```

#### Specifying structured JSON schema

You are part of a team working on an online education platform designing new interactive exercise where students are able to ask questions and their answer is displayed through a graphical view. This question-answering feature is powered by an LLM, but the graphical view requires a JSON input with the fields `Question` and `Answer` to correctly show the question and answer:

```
{
    "Question": "...",
    "Answer": "..."
}
```

```python
output = llm.create_chat_completion(
    messages=messages,
    response_format={
        "type": "json_object",
        "schema": {
            "type": "object",
            # Set the properties of the JSON fields and their data types
            "properties": {"Question": {"type": "string"}, "Answer": {"type": "string"}}
        }
    }
)

print(output['choices'][0]['message']['content'])
```

### Building conversations

#### Creating a conversational class

One of the most popular applications for LLMs like Llama is conversational chatbots, which allow users to send messages, get a response, and ask follow-up questions using a message history.

In this exercise, you'll create a `class` called `Conversation` that will allow you to create conversation with minimal code.

```python
class Conversation:
    # Complete the __init__ method of the Conversation class
    def __init__(self, llm: Llama, system_prompt='', history=[]):
        self.llm = llm
        self.system_prompt = system_prompt
        self.history = [{"role": "system", "content": self.system_prompt}] + history

    def create_completion(self, user_prompt=''):
        # Add the user prompt to the history
        self.history.append({"role": "user", "content": user_prompt})
        # Send the history messages to the LLM
        output = self.llm.create_chat_completion(messages=self.history)
        conversation_result = output['choices'][0]['message']
        # Append the conversation_result to the history
        self.history.append(conversation_result)
        return conversation_result['content']
```

#### Single-turn conversation

Time to give your `Conversation` class a go! You'll use this class to create a travel recommendation  
chatbot, that takes a description or specification of what you're looking for, and the model returns a location.

The `Conversation` class you created in the previous exercise is still available with the following methods:

- `__init__(self, llm: Llama, system_prompt='', history=[])`
- `create_completion(self, user_prompt='')`

```python
instruction = "You are a travel expert that recommends a travel destination based on a specification. Return the location name only in City, Country form."

# Define a chatbot using the Conversation class
chatbot = Conversation(llm, system_prompt=instruction)

# Send a prompt to the model
result = chatbot.create_completion("I'd like to learn about the Aztecs.")
print(result)
```

#### Multi-turn conversations

Let's extend the travel chatbot to allow users to respond to the model's initial recommendation. You'll again use the `Conversation` class, but this time, you'll make repeated calls to the model to see how the model handles previous information.

```python
chatbot = Conversation(llm, system_prompt="You are a travel expert that recommends a travel destination based on a prompt. Return the location name only as 'City, Country'.")

# Ask for the initial travel recommendation
first_recommendation = chatbot.create_completion("Recommend a Spanish-speaking city.")
print(first_recommendation)

# Add an additional request to update the recommendation
second_recommendation = chatbot.create_completion("A different city in the same country")
print(second_recommendation)
```