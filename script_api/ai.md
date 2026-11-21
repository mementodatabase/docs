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

Requests go to the AI source chosen in the AI assistant settings, **Memento Cloud** or your own API key
(**OpenAI API** or another OpenAI-compatible provider), and use the AI assistant's models.

{: .note }
AI requests must be executed asynchronously in the last Phase of an Event

{: .note }
With Memento Cloud each AI request spends AI credits, the same balance the AI assistant uses. The device limits how many
requests scripts make (see [Limits](#limits)). Write scripts efficiently to manage usage costs

# Global functions
## ai()
Creates and returns an AI object used to send requests to an AI model.

#### Returns
{: .no_toc } 
A new AI object instance for making AI requests

# AI object
The AI object provides methods for making AI requests within your app scripts. Access it using the global ai() function.

## model(model_name)
Sets the AI model to be used for requests. Without it, the default model for scripts is used: the one chosen in
Settings → AI Assistant → AI Models → Model for scripts, or else the default model of the AI assistant's chats.

A model that is not available with the current AI source is replaced by the default model,
and a warning is written to the script log.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| model_name | String | The name of the AI model |

#### Models
{: .no_toc } 
The models of the AI assistant. With Memento Cloud, the models listed in Settings → AI Assistant → AI Models, with
their prices in credits, except those turned off there. With your own API key, the models added there. The model list
of the Ask AI automation action shows the names to pass here.

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
| attachments (optional) | String or Array | up to 5 images: image fields' values, file paths or URLs. The model must accept images. |

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
With Memento Cloud each request spends AI credits from your account, the same balance the AI assistant uses: the tokens
of the request and of the answer at the model's price, shown in the AI Models settings as credits per million input and
output tokens. Your balance is on the Memento account screen. With your own API key the provider is paid, and no credits
are used.

# Limits
The device limits AI requests of scripts, so that a mistake such as `ai()` in a loop over a thousand entries does not
spend all your credits. The limits are set in Settings → AI Assistant → AI limits for scripts and hold for all scripts,
triggers and the Ask AI automation action on the device together. 0 means no limit.

| Limit | Default | |
|-------|---------|-|
| AI credits per day | 500 | Memento Cloud only |
| Calls per script run | 20 | |
| Calls per day | 500 | all scripts and triggers |

A request over a limit fails with an error that names the limit; in a run past its limit every later request fails too,
even if the script catches the error. Days are counted by the device's local time. Each request counts its credits
rounded up to a whole number and writes the model, its tokens and credits to the script log; the AI limits for scripts
screen shows what scripts have spent today.

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
    .model("gpt-6-sol")
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
    .system("You are an assistant that answers questions using the provided context.")
    .ask("Context:\n" + context + "\n\nQuestion: What are the main themes across these notes?");

message(response);
```

### Passing entry values to AI
{: .no_toc }
```javascript
var data = entry().values();
var response = ai()
    .system("You are a data assistant. Summarize the entry in plain English.")
    .ask("Here is the entry data:\n" + JSON.stringify(data, null, 2));
message(response);
```
