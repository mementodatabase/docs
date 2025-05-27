---
title: System
parent: Memento JavaScript API
nav_order: 8
layout: default
---

# System
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

# Global functions

## cancel()
Stops the current system operation that triggered the event. Useful for preventing operations during validation phases.

#### Returns
{: .no_toc } 
void

#### Example
{: .no_toc } 
```javascript
// Prevent saving if validation fails
if (!isValid) {
    log("Validation failed - canceling save");
    cancel();
}
```

## exit()
Terminates script execution immediately.

#### Returns
{: .no_toc } 
void

#### Example
{: .no_toc } 
```javascript
log("Performing early exit");
exit();
```

## system()
Returns information about the current system environment.

#### Returns
{: .no_toc } 
System object

#### Example
{: .no_toc } 
```javascript
let sys = system();
log("Operating system: " + sys.os);
log("Current user: " + sys.username);
```

## guid()
Generates a random unique identifier string.

#### Returns
{: .no_toc } 
String - Random identifier

#### Example
{: .no_toc } 
```javascript
let uniqueId = guid();
log("Generated ID: " + uniqueId);
```

## intent(action)
{: .d-inline-block }
android
{: .label .label-green }
Creates an Intent object for inter-application communication (Android only).

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| action | String | Standard Android intent action (e.g., "android.intent.action.VIEW") |

#### Returns
{: .no_toc } 
Intent object

#### Example
{: .no_toc } 
```javascript
// Create intent to dial a phone number
let i = intent("android.intent.action.DIAL");
i.data("tel:+1234567890");
i.send();
```

# System Object

### Properties
{: .no_toc } 

| Property | Type | Description |
|----------|------|-------------|
| os | String | Operating system name |
| username | String | Current user's username |

# Intent Object
Represents an Android Intent for inter-application communication.

## data(uri)
Sets the data URI for the intent.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| uri | String | URI for the data (e.g., "tel:", "mailto:", file path) |

#### Returns
{: .no_toc } 
void

#### Example
{: .no_toc } 
```javascript
let i = intent("android.intent.action.VIEW");
i.data("http://example.com");
```

## extra(key, value)
Adds extra data to the intent as key-value pairs.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| key | String | Key identifier for the extra data |
| value | Any | Value to associate with the key |

#### Returns
{: .no_toc } 
void

#### Example
{: .no_toc } 
```javascript
let i = intent("android.intent.action.SEND");
i.extra("android.intent.extra.TEXT", "Hello World");
```

## extraLong(key, value)
Adds extra data specifically as Long type values.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| key | String | Key identifier for the extra data |
| value | Number | Long value to associate with the key |

#### Returns
{: .no_toc } 
void

#### Example
{: .no_toc } 
```javascript
// Calendar event example
let i = intent("android.intent.action.INSERT");
i.data("content://com.android.calendar/events");
i.extraLong("beginTime", new Date().getTime());
```

## mimeType(mime)
Sets the MIME type for the intent data.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| mime | String | MIME type (e.g., "text/plain", "image/jpeg") |

#### Returns
{: .no_toc } 
void

#### Example
{: .no_toc } 
```javascript
let i = intent("android.intent.action.SEND");
i.mimeType("text/plain");
```

## send()
Executes the intent, sending it to the Android system.

#### Returns
{: .no_toc } 
void

#### Example
{: .no_toc } 
```javascript
// Complete SMS example
let i = intent("android.intent.action.SENDTO");
i.data("smsto:+1234567890");
i.extra("sms_body", "Hello from Memento!");
i.send();
```
