#AI 
# Slides

[chapter1](attachment:4083481f-bd84-47be-994a-4e01ef973a64:chapter1_compressed.pdf)

[chapter2_compressed.pdf](attachment:088b4bc1-fc00-48f0-bb63-a6d0154e28e9:chapter2_compressed.pdf)

[chapter3_compressed.pdf](attachment:7a6749f6-d084-4b92-9f5d-8dd54dfc7bd2:chapter3_compressed.pdf)

[chapter4_compressed.pdf](attachment:ffc70973-20f2-4974-b5c6-6891d3e3e66f:chapter4_compressed.pdf)

# Notes

## Introduction to Prompt Engineering Best Pratices

### Introduction to prompt engineering

#### OpenAI API message roles

You are developing a chatbot for an event management agency that will be used to facilitate networking during events.

Using the OpenAI API, you prepare a dictionary to pass as the message to the `chat.completions` endpoint. The message needs to have 3 roles defined to ensure the model has enough guidance to provide helpful responses.

Throughout the course, you'll write Python code to interact with the OpenAI API. **Entering  
your own API key is not necessary to create requests and complete the  
exercises in this course. You can leave the placeholder `"<OPENAI_API_TOKEN>"` as the key in `api_key`**.

The `OpenAI` package has been pre-loaded for you.

```python
# Create the OpenAI client: you can leave "<OPENAI_API_TOKEN>" as is
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Define the conversation messages
conversation_messages = [
    {"role": "system", "content": "You are a helpful event management assistant."},
    {"role": "user", "content": "What are some good conversation starters at networking events?"},
    {"role": "assistant", "content": ""}
]

response = client.chat.completions.create(
  model="gpt-4o-mini",
  messages=conversation_messages
)
print(response.choices[0].message.content)
```

#### Creating the get_response() function

Most of the exercises in this course will call the `chat.completions` endpoint of the OpenAI API with a user prompt. Here, you will create a `get_response()` function that receives a prompt as input and returns the response as an output, which in future exercises will be pre-loaded for you.

The `OpenAI` package, and OpenAI API Python client have been pre-loaded.

```python
def get_response(prompt):
  # Create a request to the chat completions endpoint
  response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": prompt}], 
    temperature = 0)
  return response.choices[0].message.content

# Test the function with your prompt
response = get_response("Write a short poem about chatGPT")
print(response)
```

#### Exploring prompt engineering

Prompt engineering refers to crafting effective prompts to guide the language model towards the intended response. By refining your prompts, you can achieve better results and guide the model towards generating more accurate and useful responses. Your task in this exercise is to modify  
the prompt you used in the previous exercise.

The `OpenAI` package and the `get_response()` function have been pre-loaded for you.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt that follows the instructions
prompt = "Write a poem about ChatGPT in basic English that a child can understand"

# Get the response
response = get_response(prompt)

print(response)
```

### Key principles of prompt engineering

#### Using delimited prompts with f-strings

You are a junior developer at a dynamic startup that generates content with the help of AI. The company believes this technology can revolutionize storytelling, and you are excited to be a part of it. Today, your task is to generate a continuation of a story with a delimited prompt using an f-string.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create a prompt that completes the story
prompt = f"""Complete the story delimited by triple backticks. 
 ```{story}```"""

# Get the generated response 
response = get_response(prompt)

print("\n Original story: \n", story)
print("\n Generated story: \n", response)
```

#### Building specific and precise prompts

In the previous exercise, you generated text that completes a given `story`. Your team was happy with your achievement, however, they want you to follow specific guidelines when it comes to **length** and **style**. Your task now is to craft a more specific prompt that controls these aspects of the generated story.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create a request to complete the story
prompt = """
    Complete the story delimited with triple backticks with only two paragraphs and with Shakespeare style: '''{story}'''
"""

# Get the generated response
response = get_response(prompt)

print("\n Original story: \n", story)
print("\n Generated story: \n", response)
```

### Structured outputs and conditional prompts

#### Generating a table

Imagine you are a developer working for a renowned online bookstore known for its extensive collection of science fiction novels. Today, you have a task at hand: to create a **table** of **ten must-read science fiction books** for the website's homepage. This will enhance the user experience on  
the website, helping fellow sci-fi enthusiasts discover their next great read.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create a prompt that generates the table
prompt = """
    Generate a table of 10 books with 3 columns (Title, Author and Year) that I should read as i am a science fiction lover
"""

# Get the response
response = get_response(prompt)
print(response)
```

#### Customizing output format

You work as a developer at a startup that offers a text analysis platform for content creators. Your platform helps users automatically categorize and format their content, and you're now working on a new feature that detects the **language** of a given piece of `text` and generates a **suitable title** for that text in a **custom format**. You decide to craft a prompt that guides the language model through this.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create the instructions
instructions = "You will be provided with a text delimited by triple backticks. Infer its language, then generate a suitable title for it. "

# Create the output format
output_format = """Use the following format for the output:
         - Text: <the text>
         - Language: <the text language>
         - Title: <the generated title>"""

# Create the final prompt
prompt = instructions + output_format + f"```{text}```"
response = get_response(prompt)
print(response)
```

#### Using conditional prompts

Building upon the previous task, your next challenge is to enhance the responses you received. When processing a given text, you need to determine its language, count the number of sentences, and generate a suitable title if the text contains more than one sentence. However, here's the new  
twist: if the text consists of only one sentence, no title should be generated, and instead, the model should display "N/A". This modification ensures that the title is generated only for texts with  
multiple sentences, providing a more refined and practical output for your platform's users.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create the instructions
instructions = "You will be provided with a text delimited by triple backticks. Infer its language and the number of sentences it contains. Then, if the text has more than one sentence, generate a suitable title for it. Otherwise, if the text contains only one sentence, write 'N/A' for the title."

# Create the output format
output_format = """The output should follow this format:
          - Text: <the given text>
          - Language: <the text language>
          - Title: <the generated title, or N/A>'."""

prompt = instructions + output_format + f"```{text}```"
response = get_response(prompt)
print(response)
```

## Advanced Prompt Engineering Strategies

### Few-shot prompting

#### Controlling output structure

One way to control the output structure provided by a language model is to give it a sample question-answer in the prompt. The model will learn from it and follow it when generating responses for similar questions. This exercise aims to let you build a one-shot prompt that extracts odd numbers from a given set of numbers and displays them as a set of numbers between brackets, separated by commas as shown in the  
instructions.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create a one-shot prompt
prompt = """
     Q: Extract the odd numbers from {1, 3, 7, 12, 19}. A: Odd numbers = {1, 3, 7, 19}
     Q: Extract the odd numbers from {3, 5, 11, 12, 16}. A:
"""

response = get_response(prompt)
print(response)
```

#### Sentiment analysis with few-shot prompting

You're working on market research and your goal is to use few-shot prompting  
to perform sentiment analysis on customer reviews. You are assigning a number for a given customers conversation: -1 if the sentiment is negative, 1 if positive. You provide the following examples as previous conversations for the model to learn from.

- The product quality exceeded my expectations -> 1
- I had a terrible experience with this product's customer service -> -1

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

response = client.chat.completions.create(
  model = "gpt-4o-mini",
  # Provide the examples as previous conversations
  messages = [{"role": "user",
  		     "content": "The product quality exceeded my expectations"},
              {"role": "assistant",
  		     "content": "1"},
              {"role": "user",
  		     "content": "I had a terrible experience with this product's customer service"},
              {"role": "assistant",
  		     "content": "-1"}, 
              # Provide the text for the model to classify
              {"role": "user",
  		     "content": "The price of the product is really fair given its features"}
             ],
  temperature = 0
)
print(response.choices[0].message.content)
```

### Multi-step prompting

#### single-step prompt to plan a trip

Imagine you're a developer taking a break and you want to apply your prompting  
skills to plan the perfect beach vacation. As an initial step, you decide to use a **standard single-step prompt** to seek assistance.

The `OpenAI` package and the `get_response()` function have been pre-loaded for you.

The `get_response()` function in this exercise employs the `max_tokens` parameter to help this exercise run faster.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create a single-step prompt to get help planning the vacation
prompt = """
    Help me planning a beach vacation
"""

response = get_response(prompt)
print(response)
```

#### Multi-step prompt to plan a trip

You noticed that the single-step prompt was not effective, because the answer was too vague for what you had in mind. You improve your prompt by specifying the steps to follow for planning. The plan should have four potential locations for your beach vacation, and each location should have some accommodation options, some activities, and an evaluation of the pros and cons.

The `OpenAI` package and the `get_response()` function have been pre-loaded for you.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create a prompt detailing steps to plan the trip
prompt = """
    Help me planning a beach vacation, it should include:
    1. Four potential locations
    2. Each with some accommodation options
    3. Some activities
    4. Evaluate of pros and cons
"""

response = get_response(prompt)
print(response)
```

#### Analyze solution correctness

You're back from your relaxing vacation and you've been assigned the task of reviewing and correcting some programming tasks, including a function to calculate of the area of a shape. You are provided with a `code` string that contains the function to calculate the area of a rectangle, and need to assess its correctness. The ideal function for you is a function that has **correct syntax**, **receives two inputs**, and **returns one output**.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

code = '''
def calculate_rectangle_area(length, width):
    area = length * width
    return area
'''

# Create a prompt that analyzes correctness of the code
prompt = f"""
     Analyze the correctness of the function delimited by triple backticks according to the following criteria:
      1- It should have correct syntax
      2- The function should receive only 2 inputs
      3- The function should return only one output
      ```{code}```
    """

response = get_response(prompt)
print(response)
```

### Chain-of-thought and self-consistency prompting

#### Reasoning with chain-of-thought prompts

Chain-of-thought prompting is helpful to explain the reasoning behind the answer that the model is giving, especially in complex tasks such as generating the solution for a mathematical problem or a riddle. In this exercise, you will craft a chain-of-thought prompt to let the language model guess the age of your friend's father based on some information you will provide.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create the chain-of-thought prompt
prompt = "Compute the age of my friend's father in 10 years, given that now he's double my friend's age, and my friend is 20. Give a step by step explanation."

response = get_response(prompt)
print(response)
```

#### One-shot chain-of-thought prompts

When you need to sum the even numbers within a given set, you first have to  
identify these even numbers and then sum them. You can teach this to a language model via one or more examples, and it will follow this strategy to operate on new sets.

Your goal in this exercise is to teach the model how to apply this procedure on the following set: `{9, 10, 13, 4, 2}`, and then ask the model to perform it on a new set: `{15, 13, 82, 7, 14}`. This is how you perform chain-of-thought prompting through one-shot prompting.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Define the example 
example = """Q: Sum the even numbers in the following set: {9, 10, 13, 4, 2}.
             A: Even numbers: {10, 4, 2}. Adding them: 10+4+2=16"""

# Define the question
question = """Q: Sum the even numbers in the following set:  {15, 13, 82, 7, 14}. 
              A:"""

# Create the final prompt
prompt = f"""
    Given the example {example} answer the following question {question}
"""
response = get_response(prompt)
print(response)
```

#### Self-consistency prompts

Imagine you own a store that sells laptops and mobile phones. You start your day with 50 devices in the store, out of which 60% are mobile phones. Throughout the day, three clients visited the store, each of them bought one mobile phone, and one of them bought additionally a laptop. Also, you added to your collection 10 laptops and 5 mobile phones. How many laptops and mobile phones do you have by the end of the day? This problem is defined in the `problem_to_solve` string, and you will use a self-consistency prompt to solve it.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create the self_consistency instruction
self_consistency_instruction = "Imagine three completely independent experts who reason differently are answering this question. The final answer is obtained by majority vote. The question is: "

# Create the problem to solve
problem_to_solve = "If you own a store that sells laptops and mobile phones. You start your day with 50 devices in the store, out of which 60% are mobile phones. Throughout the day, three clients visited the store, each of them bought one mobile phone, and one of them bought additionally a laptop. Also, you added to your collection 10 laptops and 5 mobile phones. How many laptops and mobile phones do you have by the end of the day?"

# Create the final prompt
prompt = self_consistency_instruction + problem_to_solve

response = get_response(prompt)
print(response)
```

### Iterative prompt engineering and reginement

#### Iterative prompt engineering for standard prompts

You are a developer using prompt engineering techniques for your various tasks, and you want to carefully select the right language model. You wrote an initial `prompt` to know what are the top ten pre-trained language models out there. Now, your goal is to refine this prompt to generate a table presenting information on each model's name, release year and its owning company.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Refine the following prompt
prompt = "Generate a table that contains the top 10 pre-trained language models, with columns for language model, release year, and owners."

response = get_response(prompt)
print(response)
```

#### Iterative prompt engineering for few-shot prompts

You are currently working on a project at your content creation company. The project's objective is to develop a text classification model capable of accurately identifying and categorizing different emotions in text, such as happiness, sadness, and fear. In cases where the text does not contain any discernible emotion, you aim for the model to respond with "no explicit emotion."

You decided to use the provided few-shot `prompt`. However, you've noticed that "Time flies like an arrow" is being incorrectly classified as "surprise." Your objective now is to refine the `prompt` so that the model correctly classifies this particular example as "no explicit emotion."

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Refine the following prompt
prompt = """
Receiving a promotion at work made me feel on top of the world -> Happiness
The movie's ending left me with a heavy feeling in my chest -> Sadness
Walking alone in the dark alley sent shivers down my spine -> Fear
Time flies like and arrow -> No explicit emotion
Alberto el del culo abierto -> No explicit emotion
They sat and ate their meal ->
"""

response = get_response(prompt)
print(response)
```

## Prompt Engineering for Business Applications

### Text summarization and expansion

#### Market research report summarization

A market research firm needs to analyze and summarize lengthy reports on market trends and customer behavior. They want to know how AI and data privacy are shaping the market and how they're affecting customers. You are provided with a `report` string about markets trends and how they're affecting customer behavior. Your goal is to craft a prompt to summarize it while focusing on aspects related to AI and data privacy to see their effect on customers.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt to summarize the report
prompt = f"""
Summarize the report delimited by triple backticks with a maximum of 5 sentences, while focusing on aspects related to AI and data privacy.
 ```{report}```
"""

response = get_response(prompt)

print("Summarized report: \n", response)
```

#### Product features summarization

An electronics review website wants to provide concise and easy-to-read summaries of product features for its readers, allowing them to compare and evaluate different products quickly. The review website wants to generate bullet-point summaries, and, to start with, they want you to craft a prompt that summarizes a `product_description` for a smartphone.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt to summarize the product description
prompt = f"""
Summarize the product description delimited by triple backticks, in at most five bullet points.
 ```{product_description}```
"""

response = get_response(prompt)

print("Original description: \n", product_description)
print("Summarized description: \n", response)
```

#### Product description expansion

As you continue your work on the electronics review website, you've come across some products that are already summarized but lack a comprehensive description. Your task now is to expand these concise product descriptions into detailed narratives, ensuring that each product has both a full description and a bulleted summary for easy comparison. The complete description should effectively capture the product's unique features, benefits, and potential applications. You will apply your first prompt on a smart home security camera's product description.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt to expand the product's description
prompt = f"""
Expand the product description for the Smart Home Security Camera delimited by triple backticks to provide a comprehensive overview of its features, benefits, potential applications, without bypassing the limit of one paragraph. 
 ```{product_description}```
"""

response = get_response(prompt)

print("Original description: \n", product_description)
print("Expanded description: \n", response)
```

### Text transformation

#### Translation for multilingual communication

A multinational company wants to expand its reach to more international markets. They need a language translation solution to convert their product descriptions and marketing materials into multiple languages. These will then be verified before being published.

Your task is to design a prompt that translates text from one language to multiple other languages, facilitating effective communication with customers worldwide. You will apply your prompt on a provided `marketing_message` that introduces their latest collection of premium leather handbags.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt that translates
prompt = f"""Translate the English marketing message delimited by triple backticks to French, Spanish, and Japanese
 ```{marketing_message}```
"""

response = get_response(prompt)

print("English:", marketing_message)
print(response)
```

#### Tone adjustment for email marketing

An e-commerce company regularly conducts email marketing campaigns to promote its products, inform customers about new arrivals, and offer exclusive deals. The company has a hypothesis that its current strategy is too informal and wants to test out how tone adjustment to their emails could help boost customer engagement.

Your task is to craft a prompt that can effectively transform the tone of marketing emails. You will apply your prompt on the `sample_email` provided.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt to change the email's tone
prompt = f"""
Write the email delimited by triple backticks using a professional, positive, and user-centric tone:
 ```{sample_email}```
"""

response = get_response(prompt)

print("Before transformation: \n", sample_email)
print("After transformation: \n", response)
```

#### Writing improvement

A popular online community relies on user-generated content to thrive. Users frequently contribute reviews, articles, and comments, but the quality of these contributions varies. To elevate the overall experience for its members, the community aims to enhance user-generated texts. This includes fixing grammar errors and refining writing tones to create a more polished and engaging environment for all users. Your task is to build a multi-step prompt that proofreads and adjusts the tone of a given `text`.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt to transform the text
prompt = f"""Transform the text delimited by triple backticks with the following two steps:
Step 1 - Proofread it without changing its structure
Step 2 - Change the tone to be formal and friendly
 ```{text}```"""

response = get_response(prompt)

print("Before transformation:\n", text)
print("After transformation:\n", response)
```

### Text analysis

#### Customer support ticket routing

A large customer support team receives many tickets related to different business areas, such as technical issues, billing inquiries, and product feedback. Your task is to create a prompt that automatically classifies incoming tickets into these three groups and routes them to the appropriate support specialists, reducing response times and enhancing customer satisfaction. You will test your prompt on a provided sample `ticket`.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt to classify the ticket
prompt = f"""Classify the ticket delimited by triple backticks as technical issue, billing inquiry, or product feedback. Your response should just contain the class and nothing else.
 ```{ticket}```"""

response = get_response(prompt)

print("Ticket: ", ticket)
print("Class: ", response)
```

#### Customer support ticket analysis

The customer support team receive tickets through various channels, such as email, chat, and social media. The company wants to automatically extract key entities to categorize and prioritize the tickets appropriately. Your job is to craft a few-shot prompt that helps them achieve that.

You have three sample tickets (`ticket_1`, `ticket_2`, and `ticket_3`) and their corresponding entities (`entities_1`, `entities_2`, and `entities_3`) to inform the model on what to look for and how to display it. You need the model to extract entities from the new `ticket_4` string.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a few-shot prompt to get the ticket's entities
prompt = f"""Ticket: {ticket_1} -> Entities: {entities_1}
            Ticket: {ticket_2} -> Entities: {entities_2}
            Ticket: {ticket_3} -> Entities: {entities_3}
            Ticket: {ticket_4} -> Entities: """

response = get_response(prompt)

print("Ticket: ", ticket_4)
print("Entities: ", response)
```

### Code generation and explanation

#### Code generation with problem description

You work as an analyst for a retail company and analyze monthly sales data. You need to develop a Python function that accepts a list of 12 numbers representing sales for each month of the year and outputs the month with the highest sales value. This information will help your company identify the most profitable month. You feed the problem description to a language model to get help.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a prompt that asks the model for the function
prompt = """
    You work as an analyst for a retail company and analyze monthly sales data. You need to develop a Python function that accepts a list of 12 numbers representing sales for each month of the year and outputs the month with the highest sales value. This information will help your company identify the most profitable month.
"""

response = get_response(prompt)
print(response)
```

### Input-output examples for code generation

You work as a project manager and need to estimate the time it will take to complete different projects. Your task is to develop a Python function that can predict the estimated completion time for a project based on historical data. You are given a set of examples in the `examples` string where different factors are associated with project completion time. Each example includes the factors' numerical values and the  
corresponding estimated completion time.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

examples="""input = [10, 5, 8] -> output = 23
input = [5, 2, 4] -> output = 11
input = [2, 1, 3] -> output = 6
input = [8, 4, 6] -> output = 18
"""

# Craft a prompt that asks the model for the function
prompt = f"""You are provided with input-output examples delimited by triple backticks for a Python function where different factors are associated with project completion time. Each example includes numerical values for the factors and the corresponding estimated completion time. Write code for this function.
 ```{examples}```"""

response = get_response(prompt)
print(response)
```

#### Modifying code with multi-step prompts

You are a home improvement contractor specializing in flooring installations. You need to develop a Python function that calculates the area and perimeter of a rectangular floor in a room to help you determine the amount of flooring material required for the project.

You are given a string named `function`, which contains a starter function that calculates the area of a rectangular floor given its width and length. Using a multi-step prompt, you need to prompt the language model to modify this function to return the perimeter of the rectangle as well, and to test if the inputs (floor dimensions) are positive, and if not, display appropriate error messages.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

function = """def calculate_area_rectangular_floor(width, length):
					return width*length"""

# Craft a multi-step prompt that asks the model to adjust the function
prompt = f"""Modify the script delimited by triple backticks as follows:
             - The function should return the perimeter of the rectangular floor as well.
             - The inputs (floor dimensions) should be positive. Otherwise, appropriate error messages should be displayed.
           ```{function}```"""

response = get_response(prompt)
print(response)
```

#### Explaining code step by step

As a financial analyst at a consulting firm, you're tasked with analyzing investment portfolios and providing insights to clients. While reviewing a set of financial data files, you come across a Python `function` that seems related to analyzing portfolios, but you're unsure of its exact purpose. You decide to use chain-of-thought prompting to let the language model decipher it step by step.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft a chain-of-thought prompt that asks the model to explain what the function does
prompt = f"""Explain what the function delimited by triple backticks does. Let's think step by step
 ```{function}```
"""
 
response = get_response(prompt)
print(response)
```

## Prompt Engineering for Chatbot Development

#### Creating a dual-prompt get_response() function

The following exercises will be based on calling the `chat.completions` endpoint of the OpenAI API with two prompts (a system prompt and a user prompt). To prepare for this, in this exercise you will create a dual-prompt `get_response()` function that receives two prompts as input (`system_prompt` and `user_prompt`) and returns the response as an output. You will then apply this function to any example of your choice.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

def get_response(system_prompt, user_prompt):
  # Assign the role and content for each message
  messages = [{"role": "user", "content": user_prompt},
      		  {"role": "system", "content": system_prompt}]  
  response = client.chat.completions.create(
      model="gpt-4o-mini", messages= messages, temperature=0)
  
  return response.choices[0].message.content

# Try the function with a system and user prompts of your choice 
response = get_response("You are a chatbot that finds the cheapest options of items through the internet", "Hello, who are you?")
print(response)
```

#### Customer support chatbot

You are tasked with developing a customer support chatbot for an e-commerce  
company specializing in electronics. This chatbot will assist users with inquiries, order tracking, and troubleshooting common issues. You aim to create a system prompt that clearly defines the chatbot's purpose and provides response guidelines that set the tone for interactions and specify the intended audience. A sample user prompt is provided.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Define the purpose of the chatbot
chatbot_purpose = "You are the customer support chatbot for an e-commerce platform specializing in electronics. Your role is to assist customers with inquiries, order tracking, and troubleshooting common issues related to their purchases. "

# Define audience guidelines
audience_guidelines = "Your primary audience consists of tech-savvy individuals who are interested in purchasing electronic gadgets. "

# Define tone guidelines
tone_guidelines = "Maintain a professional and user-friendly tone in your responses."

base_system_prompt = chatbot_purpose + audience_guidelines + tone_guidelines
response = get_response(base_system_prompt, "My new headphones aren't connecting to my device")
print(response)
```

#### Behavioral control of a customer support chatbot

When the company started using your chatbot from the previous exercise, they  
realized they'd like to incorporate two conditions to improve its interactions: they want the customer support chatbot to ask for an order number if not provided, and to express empathy for customers going through technical issues.

They've assigned this update to you. You need to append these conditions to the `base_system_prompt` that represents the prompt you engineered in the previous exercise and obtain a `refined_system_prompt`. You will test the chatbot on two queries.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Define the order number condition
order_number_condition = "If the user is asking about an order, and did not specify the order number, reply by asking for this number. "

# Define the technical issue condition
technical_issue_condition = "If the user is talking about a technical issue, start your response with `I'm sorry to hear about your issue with ...` "

# Create the refined system prompt
refined_system_prompt = base_system_prompt + order_number_condition + technical_issue_condition

response_1 = get_response(refined_system_prompt, "My laptop screen is flickering. What should I do?")
response_2 = get_response(refined_system_prompt, "Can you help me track my recent order?")

print("Response 1: ", response_1)
print("Response 2: ", response_2)
```

### Role-playing prompts for chatbots

#### Learning advisor chatbot

You are developing a personalized learning advisor chatbot that recommends  
textbooks for users. The chatbot's role is to receive queries from learners about their background, experience, and goals, and accordingly, recommends a learning path of textbooks, including both beginner-level and more advanced options. Your job is to create a role-playing `system_prompt` for the textbook recommendation chatbot, highlighting what it is expected to do while interacting with the users.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Craft the system_prompt using the role-playing approach
system_prompt = "Act as a learning advisor who receives queries from users mentioning their background, experience, and goals, and accordingly provides a response that recommends a tailored learning path of textbooks, including both beginner-level and more advanced options."

user_prompt = "Hello there! I'm a beginner with a marketing background, and I'm really interested in learning about Python, data analytics, and machine learning. Can you recommend some books?"

response = get_response(system_prompt, user_prompt)
print(response)
```

#### Adding guidelines for the learning advisor chatbot

In the previous exercise, you built a chatbot to recommend textbooks. However, the company has identified a need for an update to ensure more efficient recommendations. You are provided with a `base_system_prompt`, similar to the one you created previously, and your task is to incorporate `behavior_guidelines` and `response_guidelines`. These guidelines will help control the chatbot's behavior and ensure it offers more effective and tailored textbook recommendations to users.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

base_system_prompt = "Act as a learning advisor who receives queries from users mentioning their background, experience, and goals, and accordingly provides a response that recommends a tailored learning path of textbooks, including both beginner-level and more advanced options."

# Define behavior guidelines
behavior_guidelines = "If the user's query does not include details about their background, experience, or goals, kindly ask them to provide the missing information."

# Define response guidelines
response_guidelines = "Don't recommend more than three textbooks in the learning path"

system_prompt = base_system_prompt + behavior_guidelines + response_guidelines
user_prompt = "Hey, I'm looking for courses on Python and data visualization. What do you recommend?"
response = get_response(system_prompt, user_prompt)
print(response)
```

### Incorporating external context

#### Providing context through sample conversations

Suppose there is a delivery service named **MyPersonalDelivery** that offers a wide range of delivery options for various items. You want to create a customer service chatbot that supports customers with whatever they need. To accomplish this, you will provide a `context_question` and a `context_answer` about items the company delivers via previous conversations, and you will test if the model recognizes this context through a new user prompt.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Define the system prompt
system_prompt = "You are a customer service chatbot for MyPersonalDelivery, a delivery service that offers a wide range of delivery options for various items. You should respond to user queries in a gentle way."

context_question = "What types of items can be delivered using MyPersonalDelivery?"
context_answer = "We deliver everything from everyday essentials such as groceries, medications, and documents to larger items like electronics, clothing, and furniture. However, please note that we currently do not offer delivery for hazardous materials or extremely fragile items requiring special handling."

# Add the context to the model
response = client.chat.completions.create(
  model="gpt-4o-mini",
  messages=[{"role": "system", "content": system_prompt},
            {"role": "user", "content": context_question},
            {"role": "assistant", "content": context_answer},
            {"role": "user", "content": "Do you deliver furniture?"}])
response = response.choices[0].message.content
print(response)
```

#### Providing context through system prompt

Now you want to use system prompts in order to provide context for the chatbot about **MyPersonalDelivery** instead of relying on sample conversations. You are provided with a detailed `service_description` that introduces the services being offered and the benefits of choosing this service. You will then test a user query to see if the model recognizes the context effectively.

```python
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Define the system prompt
system_prompt = f"""You are a customer service chatbot for MyPersonalDelivery whose service description is delimited by triple backticks. You should respond to user queries in a gentle way.
 ```{service_description}```
"""

user_prompt = "What benefits does MyPersonalDelivery offer?"

# Get the response to the user prompt
response = get_response(system_prompt, user_prompt)

print(response)
```