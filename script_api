---
layout: default
title: Memento JavaScript API
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

Returns the Entry object associated with the current event.
{: .fs-6 .fw-300 }

Returns a clone of the actual Entry object. If the entry is saved, the clone becomes the actual entry. If `cancel()` is called, the clone and any changes are discarded.

**Note:** Not available during 'Creating an entry > Opening an Entry Edit card' event (use `entryDefault()` instead)
{: .note }

#### Return value

Returns an Entry object
{: .label .label-green }

#### Example

```javascript
// Get the current entry and update its status
let currentEntry = entry();
currentEntry.set("Status", "In Progress");
```

### entryDefault()

Returns an Entry object containing default field values for a not-yet-created entry.
{: .fs-6 .fw-300 }

Only available during 'Creating an entry > Opening an Entry Edit card' event. Used to set default values for new entries.

#### Return value

Returns a DefaultEntry object
{: .label .label-green }

#### Example

```javascript
// Set default values for a new entry
let defaults = entryDefault();
defaults.set("Status", "New");
defaults.set("CreatedDate", new Date().toISOString());
```

### buildDefaultEntry()

Facilitates the customization of default field values during entry creation or update events.
{: .fs-6 .fw-300 }

Available during 'Creating an entry' or 'Updating an entry' trigger events. Specifically designed for the 'Opening an Entry Edit card' phase.

#### Properties

| Property | Description |
|:---------|:------------|
| `created` | Indicates a new empty entry |
| `duplicated` | Indicates a duplicate of an existing entry |
| `prefilled` | Indicates creation from a template |

#### Return value

Returns a DefaultEntry object
{: .label .label-green }

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

Stops script execution immediately.
{: .fs-6 .fw-300 }

#### Example

```javascript
if (entry().field("Status") === "Locked") {
    // Stop execution if entry is locked
    exit();
}
```

### lib()

Returns the Library object associated with the current event.
{: .fs-6 .fw-300 }

#### Return value

Returns a Library object
{: .label .label-green }

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

Finds and returns a library by its name.
{: .fs-6 .fw-300 }

Requires appropriate security permissions to access the library. Returns null if library not found or permissions are insufficient.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | The name of the library to find |

#### Return value

Returns a Library object or null
{: .label .label-green }

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

Finds and returns a library by its ID.
{: .fs-6 .fw-300 }

Added in MDB 5.5. Requires appropriate security permissions to access the library. More reliable than libByName() as IDs don't change when libraries are renamed.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `id` | string | The ID of the library to find |

#### Return value

Returns a Library object or null
{: .label .label-green }

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
{: .fs-6 .fw-300 }

### Properties

| Property | Type | Description |
|:---------|:-----|:------------|
| `name` | string | The name of the library |
| `title` | string | The title of the library (alias for name) |
| `id` | string | The unique identifier of the library (Added in MDB 5.5) |

### Methods

#### create(values)

Creates a new entry in the library with specified field values.
{: .fs-6 .fw-300 }

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `values` | object | Key-value pairs where keys are field names and values are field values |

#### Return value

Returns an Entry object
{: .label .label-green }

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

Returns all entries in the library, sorted by creation time (newest first).
{: .fs-6 .fw-300 }

#### Return value

Returns an array of Entry objects
{: .label .label-green }

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

Returns the most recently created entry in the library.
{: .fs-6 .fw-300 }

#### Return value

Returns an Entry object or null if library is empty
{: .label .label-green }

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

Returns the oldest entry in the library.
{: .fs-6 .fw-300 }

#### Return value

Returns an Entry object or null if library is empty
{: .label .label-green }

#### Example

```javascript
// Archive old entries
let oldest = lib().firstEntry();
if (oldest && new Date(oldest.creationTime) < new Date('2023-01-01')) {
    oldest.set("Status", "Archived");
}
```
