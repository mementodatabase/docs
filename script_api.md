---
layout: default
title: JavaScript API
nav_order: 2
has_children: true
---

# JavaScript API
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Overview

Memento Database provides JavaScript capabilities for extending its functionality beyond the standard user interface. This documentation covers the available JavaScript APIs, including global functions, library operations, and entry manipulation.

## Global Functions

### entry()

Get the Entry object of the current event.
{: .fs-5 }

Returns a clone of the actual Entry object. If the entry is saved, the clone becomes the actual entry. If `cancel()` is called, the clone and any changes are discarded.

**Note:** Not available during 'Creating an entry > Opening an Entry Edit card' event (use `entryDefault()` instead)
{: .note }

**Returns**  
Entry object - the current entry

#### Example

```javascript
// Get the current entry and update its status
let currentEntry = entry();
currentEntry.set("Status", "In Progress");
```

### entryDefault()

Get default field values for a not-yet-created entry.
{: .fs-5 }

Only available during 'Creating an entry > Opening an Entry Edit card' event. Used to set default values for new entries.

**Returns**  
DefaultEntry object - contains default values for the new entry

#### Example

```javascript
// Set default values for a new entry
let defaults = entryDefault();
defaults.set("Status", "New");
defaults.set("CreatedDate", new Date().toISOString());
```

### buildDefaultEntry()

Customize default field values during entry creation or update events.
{: .fs-5 }

Available during 'Creating an entry' or 'Updating an entry' trigger events. Specifically designed for the 'Opening an Entry Edit card' phase.

#### Properties

| Property | Description |
|:---------|:------------|
| `created` | Indicates a new empty entry |
| `duplicated` | Indicates a duplicate of an existing entry |
| `prefilled` | Indicates creation from a template |

**Returns**  
DefaultEntry object - contains methods for setting default values

#### Example

```javascript
// Set different defaults based on creation method
if (buildDefaultEntry().duplicated) {
    buildDefaultEntry().set("Status", "Duplicate");
    buildDefaultEntry().set("DuplicatedDate", new Date().toISOString());
} else if (buildDefaultEntry().created) {
    buildDefaultEntry().set("Status", "New");
    buildDefaultEntry().set("Priority", "Medium");
}
```

### exit()

Stop script execution immediately.
{: .fs-5 }

#### Example

```javascript
if (entry().field("Status") === "Locked") {
    // Stop execution if entry is locked
    exit();
}
```

### lib()

Get the Library object of the current event.
{: .fs-5 }

**Returns**  
Library object - the current library

#### Example

```javascript
// Get current library and create a new entry
let currentLib = lib();
let newEntry = currentLib.create({
    "Title": "New Task",
    "Status": "Pending"
});
```

### libByName(name)

Find a library by its name.
{: .fs-5 }

Requires appropriate security permissions to access the library. Returns null if library not found or permissions are insufficient.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | The name of the library to find |

**Returns**  
Library object or null - the library with the specified name if found

#### Example

```javascript
// Access a related library and find entries
let tasksLib = libByName("Tasks");
if (tasksLib) {
    let pendingTasks = tasksLib.find("Status: Pending");
    console.log("Pending tasks:", pendingTasks.length);
}
```

### libById(id)

Find a library by its ID.
{: .fs-5 }

Added in MDB 5.5. Requires appropriate security permissions to access the library. More reliable than libByName() as IDs don't change when libraries are renamed.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `id` | string | The ID of the library to find |

**Returns**  
Library object or null - the library with the specified ID if found

#### Example

```javascript
// Access a library using its ID
let projectsLib = libById("lib_projects_001");
if (projectsLib) {
    let activeProjects = projectsLib.find("Status: Active");
    console.log("Active projects:", activeProjects.length);
}
```

## Library Object

The Library object provides access to library entries and operations. It can be obtained through `lib()`, `libByName()`, or `libById()`.

### Properties

| Property | Type | Description |
|:---------|:-----|:------------|
| `name` | string | The name of the library |
| `title` | string | The title of the library (alias for name) |
| `id` | string | The unique identifier of the library (Added in MDB 5.5) |

### Methods

#### create(values)

Create a new entry in the library with specified field values.
{: .fs-5 }

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `values` | object | Key-value pairs where keys are field names and values are field values |

**Returns**  
Entry object - the newly created entry

#### Example

```javascript
// Create a new task with initial values
let taskLib = lib();
let newTask = taskLib.create({
    "Title": "Review Documentation",
    "Priority": "High",
    "DueDate": new Date(2024, 2, 15).toISOString(),
    "Status": "Not Started"
});
```

#### entries()

Get all entries in the library.
{: .fs-5 }

**Returns**  
Array of Entry objects - all entries sorted by creation time (newest first)

#### Example

```javascript
// Get all entries and process them
let allEntries = lib().entries();
allEntries.forEach(entry => {
    if (entry.field("Status") === "Overdue") {
        entry.set("Priority", "High");
    }
});
```

#### lastEntry()

Get the most recently created entry.
{: .fs-5 }

**Returns**  
Entry object or null - the newest entry or null if library is empty

#### Example

```javascript
// Get the latest entry and copy some of its values
let latest = lib().lastEntry();
if (latest) {
    let newEntry = lib().create({
        "Category": latest.field("Category"),
        "Department": latest.field("Department"),
        "CreatedDate": new Date().toISOString()
    });
}
```

#### firstEntry()

Get the oldest entry in the library.
{: .fs-5 }

**Returns**  
Entry object or null - the oldest entry or null if library is empty

#### Example

```javascript
// Archive old entries
let oldest = lib().firstEntry();
if (oldest && new Date(oldest.creationTime) < new Date('2023-01-01')) {
    oldest.set("Status", "Archived");
}
```
