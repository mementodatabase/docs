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

Requests go to the AI source chosen in the AI assistant settings: **Memento Cloud** or your own API key
(**OpenAI API** or another OpenAI-compatible provider).

{: .note }
AI requests must be executed asynchronously in the last Phase of an Event

{: .note }
With Memento Cloud each AI request consumes account credits. For simple tasks, use light models like gpt-6-luna or gpt-5.4-nano. Write scripts efficiently to manage usage costs

# Global functions
## ai()
Creates and returns an AI object used to send requests to an AI model.

#### Returns
{: .no_toc } 
A new AI object instance for making AI requests

# AI object
The AI object provides methods for making AI requests within your app scripts. Access it using the global ai() function.

## model(model_name)
Sets the AI model to be used for requests. Without it, the default model is used:
gpt-6-luna with Memento Cloud, or the default model chosen in the settings with your own API key.

A model that is not available with the current AI source is replaced by the default model,
and a warning is written to the script log.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| model_name | String | The name of the AI model |

#### Memento Cloud models
{: .no_toc } 

| Model | Plan | Credits per 1000 tokens |
|-------|------|-------------------------|
| gpt-6-luna | any | 1 |
| gpt-5.4-mini | any | 1 |
| gpt-5.4-nano | any | 1 |
| gpt-4.1-mini | any | 2 |
| gpt-4.1-nano | any | 1 |
| gpt-5.6-terra | PRO or TEAM | 5 |
| gpt-5.4 | PRO or TEAM | 5 |
| gpt-4.1 | PRO or TEAM | 5 |
| claude-sonnet-5 | PRO or TEAM | 6 |
| claude-sonnet-4-6 | PRO or TEAM | 6 |
| claude-haiku-4-5 | PRO or TEAM | 3 |

#### Your own API key
{: .no_toc } 
The models added in the AI assistant settings. Requests are paid to the provider, not with Memento credits.

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
Converts the model response into a JavaScript object or array (parsed from JSON).
Text around the JSON, such as an explanation or a code fence, is skipped.
Use this when expecting structured data.
#### Returns
{: .no_toc } 
A JavaScript object or array, or null if the response contains no JSON.


# AI credits
With Memento Cloud each request consumes AI credits from your account: the tokens of the request and the answer,
multiplied by the model's rate in the table above (1 credit per 1000 tokens for light models).
With your own API key no credits are used.

# Examples

### Simple text request
{: .no_toc }
```javascript
var response = ai()
    .ask("Write a haiku about databases.");
message(response);
```

### Using system message
{: .no_toc }
```javascript
var response = ai()
    .model("gpt-5.4")
    .system("You are a professional assistant specialized in finance.")
    .ask("Explain compound interest in simple terms.");
message(response);
```


### Passing images as attachments
{: .no_toc }
```javascript
var img = entry().images("Photo");
var response = ai()
    .ask("Describe this image.", [img]);
message(response);
```

### Getting structured JSON output
{: .no_toc }
```javascript
var response = ai()
    .system("Return your answer strictly as a JSON array of strings.")
    .ask("Give me a shopping list of 3 items.");
var items = response.asJson();
message(items[0]); // first item in the list
```

### Collect context from entries
{: .no_toc }
```javascript
var entries = lib().entries();
var context = "";
for (var i = 0; i < entries.length; i++) {
    var e = entries[i];
    context += "- " + e.field("Notes") + "\n";
}

// Ask the AI with context included
var response = ai()
    .model("gpt-5.4")
    .system("You are an assistant that answers questions using the provided context.")
    .ask("Context:\n" + context + "\n\nQuestion: What are the main themes across these notes?");

message(response);
```

### Passing entry values to AI
{: .no_toc }
```javascript
var data = entry().values();
var response = ai()
    .model("gpt-5.4-nano")
    .system("You are a data assistant. Summarize the entry in plain English.")
    .ask("Here is the entry data:\n" + JSON.stringify(data, null, 2));
message(response);
```
