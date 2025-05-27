---
title: Triggers
parent: Scripts
nav_order: 1
layout: default
---

# Triggers
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

Trigger scripts are JavaScript programs that execute automatically when specific events occur in your Memento Database. They allow you to automate actions, validate data, and extend the functionality of your database.

## Basic Concepts

A trigger script's execution time is determined by:
1. An **event** (such as creating an entry or updating a field)
2. A **phase** (the specific moment when the script should run)

Triggers can perform various actions, including:
- Modifying entries
- Creating new entries
- Making HTTP requests
- Creating files
- Validating data
- And more

## Execution Modes

Triggers run in one of two modes:

### Synchronous Mode
{: .no_toc } 
- Pauses user interaction while the script runs
- Used for "Before..." phases
- Can prevent actions using `cancel()`
- Should avoid time-consuming operations
- Example: Data validation before saving

### Asynchronous Mode
{: .no_toc } 
- Runs in the background
- User can continue interacting with the app
- Used for "After..." phases
- Ideal for tasks that don't need immediate completion
- Example: Logging changes after saving

{: .note }
Triggers only execute in response to user actions through the user interface (UI).

## Events and Phases

### Library Events

| Event Type | Phase | Execution Mode |
|------------|-------|----------------|
| **Opening a library** | Opening the library | synchronous |

{: .note }
Use the `lib()` method to access the current library This method is also available in other events to access the library associated with the event

### Entry Creation & Modification Events

| Event Type | Phase | Execution Mode |
|------------|-------|----------------|
| **Creating an entry** | Opening an Entry Edit card | synchronous |
| | Before saving the entry | synchronous |
| | After saving the entry | asynchronous |
| **Updating an entry** | Opening an Entry Edit card | synchronous |
| | Before saving the entry | synchronous |
| | After saving the entry | asynchronous |

{: .note }
When handling entry creation events with the "Opening an Entry Edit card" phase, you must use `defaultEntry()` instead of `entry()` because the entry object doesn't exist yet in the database For other cases use `entry()`

### Field Events

| Event Type | Phase | Execution Mode |
|------------|-------|----------------|
| **Updating a field** | Before saving | synchronous |
| | After saving | asynchronous |

{: .note }
The field update event occurs only in specific cases when quickly editing choice fields from the entry list or when editing fields in the table view It does not trigger when editing an entry through the entry edit form Use the `field()` method to access the updated field

### Entry Link Events

| Event Type | Phase | Execution Mode |
|------------|-------|----------------|
| **Linking an entry** | Before saving the entry | synchronous |
| | After saving the entry | asynchronous |
| **Unlinking an entry** | Before saving the entry | synchronous |
| | After saving the entry | asynchronous |

{: .note}
Link events provide special methods for accessing related entries including masterLib() to access the parent library masterEntry() to access the parent entry attr(name) to get the link attribute value and setAttr(name, value) to set the link attribute value.

### Entry Management Events

| Event Type | Phase | Execution Mode |
|------------|-------|----------------|
| **Deleting an entry** | Before deleting the entry | synchronous |
| | After deleting the entry | asynchronous |
| **Opening an Entry View card** | Before window display | synchronous |
| | After window display | asynchronous |

{: .note}
Deletion events are triggered only when moving entries to the recycle bin

### Favorites Events

| Event Type | Phase | Execution Mode |
|------------|-------|----------------|
| **Adding an entry to Favorites** | Before the operation | synchronous |
| | After the operation | asynchronous |
| **Removing an entry from Favorites** | Before the operation | synchronous |
| | After the operation | asynchronous |

### Scheduled Events

| Event Type | Phase | Execution Mode |
|------------|-------|----------------|
| **At scheduled time** | - | asynchronous |

{: .note }
Scheduled event execution timing may not be exact. On Android devices, the actual execution time depends on system settings and optimization features. The system might delay or batch scheduled tasks to optimize battery life and system resources.

## Creating and Using Triggers

### How to Create a Trigger
{: .no_toc } 

1. Open your library
2. Open the menu and select "Scripts"
3. Click the + button
4. Select "Trigger"
5. Select the event type and phase
6. Write your JavaScript code
7. Save the trigger

### Basic Structure
{: .no_toc } 
```javascript
// Access the current library
var currentLib = lib();

// Access the current entry
var currentEntry = entry();

// For new entries, use defaultEntry()
var newEntry = defaultEntry();

// Perform actions
if (someCondition) {
    // Make changes
    currentEntry.set("FieldName", "New Value");
} else {
    // Cancel the operation if needed
    message("Cannot proceed because...");
    cancel();
}
```

## Best Practices

1. **Keep Users Informed**
   - Use `message()` to explain what's happening
   - Provide clear error messages when using `cancel()`

2. **Handle Errors**
   - Use try/catch blocks for error handling
   - Log errors using `log()`

3. **Performance**
   - Keep synchronous scripts fast
   - Move time-consuming operations to asynchronous phases

## Security and Permissions

Trigger scripts require specific permissions that must be granted on each device:

1. **Library Access**
   - Required for `libByName()`
   - Control which libraries the script can access

2. **File System**
   - Read permission for file access
   - Write permission for creating/modifying files

3. **Network**
   - Required for HTTP requests
   - Must be explicitly granted

To set permissions:
1. Open the library triggers list
2. Click the Shield icon
3. Configure required permissions

{: .note }
Permissions must be set separately on each device

## Examples

### Default values
{: .no_toc } 
```javascript
// Set default values when creating a new entry
var newEntry = defaultEntry();
newEntry.set("Status", "New");
newEntry.set("CreationDate", new Date());
```

### Data Validation
{: .no_toc } 
```javascript
// Before saving entry
if (entry().field("Price") < 0) {
    message("Price cannot be negative");
    cancel();
}
```

### Auto-updating Fields
{: .no_toc } 
```javascript
// Before saving entry
var price = entry().field("Price");
var tax = price * 0.2;
entry().set("Tax", tax);
```

### Creating Related Entries
{: .no_toc } 
```javascript
// After saving entry
var relatedLib = libByName("Related Library");
relatedLib.create()
    .set("ParentID", entry().id())
    .set("Status", "New")
    .save();
```




