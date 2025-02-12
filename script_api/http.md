---
title: HTTP
parent: Memento JavaScript API
nav_order: 6
layout: default
---

# HTTP
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

The HTTP API allows scripts to send HTTP requests to web services, enabling integration between Memento Database and external systems. All HTTP operations are performed through the `Http` object, which is accessed via the global `http()` function.

{: .note }
HTTP requests must be executed asynchronously in the last Phase of an Event

{: .note }
The library must have the "Network" permission enabled

# Global functions
## http()
Global function that returns an Http object for making HTTP requests.

#### Returns
{: .no_toc } 
A new Http object instance for making HTTP requests

#### Example
{: .no_toc } 
```javascript
var httpClient = http();
log("Created new HTTP client");
```

# Http Object
The Http object provides methods for making HTTP requests and managing request headers. It is obtained through the global `http()` function.

## get(url)
Executes an HTTP GET request to the specified URL.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| url | String | The complete URL starting with http:// or https:// |

#### Returns
{: .no_toc } 

| Type | Description |
|------|-------------|
| HttpResult | Object containing the response from the HTTP request |

#### Example
{: .no_toc } 
```javascript
var result = http().get("http://api.fixer.io/latest?base=USD");
log("Response code: " + result.code);
log("Response body: " + result.body);
```

## post(url, body)
Executes an HTTP POST request to the specified URL with the given body content.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| url | String | The complete URL starting with http:// or https:// |
| body | String | The content to send in the POST request body |

#### Returns
{: .no_toc } 

| Type | Description |
|------|-------------|
| HttpResult | Object containing the response from the HTTP request |

#### Example
{: .no_toc } 
```javascript
var result = http().post("http://httpbin.org/post", "Hello World");
log("POST response code: " + result.code);
```

## headers(info)
Sets HTTP headers for subsequent requests.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| info | Object | Key-value pairs of header names and values |

#### Returns
{: .no_toc } 

| Type | Description |
|------|-------------|
| Http | The Http object (for method chaining) |

#### Example
{: .no_toc } 
```javascript
var httpClient = http();
httpClient.headers({"User-Agent": "Memento Script", "Accept": "application/json"});
var result = httpClient.get("http://httpbin.org/get");
log("Request with custom headers completed: " + result.code);
```

# HttpResult Object

The HttpResult object contains the response from an HTTP request.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| body | String | The response body as text |
| code | Number | The HTTP status code of the response (e.g., 200 for success) |

## header(tag)
Retrieves a specific header from the response.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| tag | String | The name of the header to retrieve |

#### Returns
{: .no_toc } 

| Type | Description |
|------|-------------|
| String | The value of the specified header |

#### Example
{: .no_toc } 
```javascript
var result = http().get("http://httpbin.org/get");
var etag = result.header("etag");
log("Response ETag: " + etag);
```

# Examples

```javascript
// Get current exchange rate from API
var result = http().get("https://data.fixer.io/api/latest?access_key=[API Key]");

// Parse the JSON response
var usdToEur = JSON.parse(result.body)["rates"]["EUR"];
log("Current USD to EUR rate: " + usdToEur);

// Update entry with converted price
var usdPrice = entry().field("PriceUSD");
var eurPrice = usdPrice * usdToEur;
entry().set("PriceEUR", eurPrice);
log("Converted " + usdPrice + " USD to " + eurPrice + " EUR");
```
