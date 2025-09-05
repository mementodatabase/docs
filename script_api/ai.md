---
title: AI
parent: Memento JavaScript API
nav_order: 10
layout: default
---

# AI
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

The AI API provides functions for sending requests to Large Language Models (LLMs) from your scripts.
Use it to generate text, analyze data, or work with attached images directly within Memento Database.

{: .note }
AI requests must be executed asynchronously in the last Phase of an Event

{: .note }
Each AI request consumes account credits. For simple tasks, use light models like gpt-4o-mini or gpt-4.1-nano. Write scripts efficiently to manage usage costs

# Global functions
## ai()
Creates and returns an AI object used to send requests to an AI model.

#### Returns
{: .no_toc } 
A new AI object instance for making AI requests

# AI object
The AI object provides methods for making AI requests within your app scripts. Access it using the global ai() function.

## model(model_name)
Sets the AI model to be used for requests.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| model_name | String | The name of the AI mode |

#### Supported models:
{: .no_toc } 
* gpt-4o-mini
* gpt-4.1
* gpt-4.1-mini
* gpt-4.1-nano
* claude-sonnet-4-0
* claude-3-5-haiku-latest

#### Returns
{: .no_toc } 
The AI object.

## system( message )
Sets a system instruction message for the model. System messages define the model’s behavior, style, or role.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| message | String | string containing the system instruction. |

#### Returns
{: .no_toc } 
The AI object.

## ask(message , attachments)
Sends a message to the AI model.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| message | String | string, the user message |
| attachments (optional) | String or Array | one or more image file paths |

#### Returns
{: .no_toc } 
An AIResult object containing the model’s response. AIResult can be used directly as a string.

# AIResult Object

AIResult objects represent the model's response and can be used as strings.

## asJson()
Converts the model response into a JavaScript object (parsed from JSON).
Use this when expecting structured data.
#### Returns
{: .no_toc } 
A JavaScript object.


# AI credits
Each request consumes AI credits from your account:
* 1 credit per 1000 tokens for lightweight models (e.g. gpt-4o-mini, gpt-4.1-nano)
* 5 credits per 1000 tokens for premium models (e.g. gpt-4.1, claude-sonnet-4-0)

# Examples

```javascript
// Simple text request
var response = ai()
    .model("gpt-4o-mini")
    .ask("Write a haiku about databases.");
message(response);
```

```javascript
// Using system message
var response = ai()
    .model("gpt-4.1")
    .system("You are a professional assistant specialized in finance.")
    .ask("Explain compound interest in simple terms.");
message(response);
```

```javascript
// Passing images as attachments
var img = entry().images("Photo");
var response = ai()
    .ask("Describe this image.", [img]);
message(response);
```


```javascript
// Getting structured JSON output
var response = ai()
    .system("Return your answer strictly as JSON.")
    .ask("Give me a shopping list of 3 items.");
var json = response.asJson();
message(json); // first item in the list
```

```javascript
// Collect context from entries
var entries = lib().entries();
var context = "";
for (var i = 0; i < entries.length; i++) {
    var e = entries[i];
    context += "- " + e.field("Notes") + "\n";
}

// Ask the AI with context included
var response = ai()
    .model("gpt-4.1")
    .system("You are an assistant that answers questions using the provided context.")
    .ask("Context:\n" + context + "\n\nQuestion: What are the main themes across these notes?");

message(response);
```

