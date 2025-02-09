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
[Previous content remains the same...]

#### fields()

Get field names defined in the library.
{: .fs-5 }

Returns an array of field names that are defined in the MAIN page and not within a Subheader. Available since Memento release 4.13.

**Returns**  
Array of strings - field names in definition order

#### Example

```javascript
// Get all field names and log them
let fieldNames = lib().fields();
fieldNames.forEach(fieldName => {
    console.log(`Field name: ${fieldName}`);
});
```

#### find(query)

Search for entries matching the given query.
{: .fs-5 }

Performs a search similar to the Memento user interface search. Returns entries sorted by creation time, from newest to oldest.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `query` | string | The search query string |

**Returns**  
Array of Entry objects - entries matching the search criteria

#### Example

```javascript
// Find all high priority tasks
let highPriorityTasks = lib().find("Priority: High");

// Find tasks with specific status and category
let pendingProjects = lib().find("Status: Pending Category: Project");

// Process found entries
highPriorityTasks.forEach(task => {
    console.log(`High priority task: ${task.field("Title")}`);
});
```

#### findById(id)

Find an entry by its unique ID.
{: .fs-5 }

Searches for an entry using its unique identifier. This is the most reliable way to find a specific entry.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `id` | string | The unique identifier of the entry |

**Returns**  
Entry object or null - the entry with the specified ID if found

#### Example

```javascript
// Find entry by ID and update it
let entry = lib().findById("entry_123");
if (entry) {
    entry.set("LastChecked", new Date().toISOString());
} else {
    console.log("Entry not found");
}
```

#### findByKey(name)

Find an entry by its name field value.
{: .fs-5 }

Searches for an entry using the entry name. The library must be configured to use unique entry names for this method to work reliably.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | The value of the Entry name field |

**Returns**  
Entry object or null - the entry with the specified name if found

#### Example

```javascript
// Find a project by its name
let project = lib().findByKey("Website Redesign");
if (project) {
    // Update project status
    project.set("Status", "In Progress");
    
    // Get linked tasks
    let linkedTasks = lib().linksTo(project);
    console.log(`Found ${linkedTasks.length} tasks linked to this project`);
}
```

#### linksTo(entry)

Find entries that contain links to the specified entry.
{: .fs-5 }

Searches for entries that have a link to the specified entry in any of their Link to Entry fields.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `entry` | Entry | The entry to search for links to |

**Returns**  
Array of Entry objects - entries that link to the specified entry

#### Example

```javascript
// Find all tasks linked to a specific project
let project = lib().findByKey("Website Redesign");
if (project) {
    let linkedTasks = lib().linksTo(project);
    
    // Process linked tasks
    linkedTasks.forEach(task => {
        console.log(`Linked task: ${task.field("Title")}`);
        if (task.field("Status") === "Completed") {
            // Update project progress
            project.set("CompletedTasks", project.field("CompletedTasks") + 1);
        }
    });
}
```

#### show()

Display the library in the user interface.
{: .fs-5 }

Opens the library view in Memento's user interface.

#### Example

```javascript
// Show the library after creating a new entry
let newEntry = lib().create({
    "Title": "Important Task",
    "Priority": "High"
});
lib().show(); // Display the library with the new entry
```

## Advanced Library Usage Examples

### Working with Multiple Libraries

```javascript
// Example of working with related libraries
let projectsLib = lib(); // Current library
let tasksLib = libByName("Tasks");
let resourcesLib = libByName("Resources");

// Create a new project
let newProject = projectsLib.create({
    "Name": "New Website",
    "StartDate": new Date().toISOString()
});

// Create associated tasks
let task1 = tasksLib.create({
    "Title": "Design mockups",
    "Project": newProject.name,
    "DueDate": new Date(2024, 2, 15).toISOString()
});

// Find available resources
let designers = resourcesLib.find("Department: Design Status: Available");
if (designers.length > 0) {
    task1.set("AssignedTo", designers[0].name);
}
```

### Complex Search and Update Operations

```javascript
// Find and update multiple entries based on complex criteria
let overdueTasks = lib().find("Status: In Progress");
let today = new Date();

overdueTasks.forEach(task => {
    let dueDate = new Date(task.field("DueDate"));
    if (dueDate < today) {
        // Task is overdue
        task.set("Status", "Overdue");
        
        // Get the assigned resource
        let assignee = task.field("AssignedTo");
        if (assignee) {
            let resourcesLib = libByName("Resources");
            let resource = resourcesLib.findByKey(assignee);
            if (resource) {
                resource.set("OverdueTasks", resource.field("OverdueTasks") + 1);
            }
        }
    }
});
```

[Previous content remains the same...]

## Entry Object

The Entry object represents a single entry in a library and provides methods for accessing and modifying its field values.

### Properties

| Property | Type | Description |
|:---------|:-----|:------------|
| `author` | string | ID of the user who created the entry |
| `creationTime` | string | Date and time when the entry was created |
| `deleted` | boolean | True if the entry is in the Recycle Bin |
| `description` | string | Entry description |
| `favorites` | boolean | True if the entry is in Favorites |
| `id` | string | Unique entry identifier |
| `lastModifiedTime` | string | Date and time of last modification |
| `name` | string | Entry name |
| `title` | string | Entry name (alias for name) |

### Methods

#### field(name)

Get the value of a specified field.
{: .fs-5 }

Returns the value of the specified field. The return type depends on the field type.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the field to retrieve |

**Returns**  
The field value in its appropriate type:
- Text fields: string
- Number fields: number
- Date fields: string (ISO date format)
- Time fields: string (HH:mm format)
- Checkbox fields: boolean
- Multiple-choice fields: array of strings
- Link to Entry fields: array of Entry objects
- Link to File fields: array of file path strings
- Image fields: array of image objects

#### Example

```javascript
// Get various field types
let entry = lib().lastEntry();

// Text field
let title = entry.field("Title");
console.log(`Title: ${title}`);

// Number field
let quantity = entry.field("Quantity");
console.log(`Quantity: ${quantity}`);

// Date field
let dueDate = new Date(entry.field("DueDate"));
console.log(`Due date: ${dueDate.toLocaleDateString()}`);

// Multiple-choice field
let categories = entry.field("Categories");
categories.forEach(category => {
    console.log(`Category: ${category}`);
});

// Link to Entry field
let linkedTasks = entry.field("RelatedTasks");
linkedTasks.forEach(task => {
    console.log(`Related task: ${task.field("Title")}`);
});
```

#### images(name)

Get images associated with an image field.
{: .fs-5 }

Returns an array of image objects, each containing information about the image and methods to manipulate it.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the image field |

**Returns**  
Array of image objects, each containing:
- `caption`: string - Get or set the image caption
- `uri`: string - Get the image URI

#### Example

```javascript
// Work with images in an entry
let entry = lib().lastEntry();
let photos = entry.images("Photos");

// Process all images
photos.forEach(photo => {
    // Get image information
    console.log(`Image URI: ${photo.uri}`);
    console.log(`Caption: ${photo.caption}`);
    
    // Update caption if needed
    if (!photo.caption) {
        photo.caption = "Photo taken on " + new Date().toLocaleDateString();
    }
});
```

#### link(name, entry)

Add a link to another entry in a Link to Entry field.
{: .fs-5 }

Creates a link to the specified entry in the named Link to Entry field.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the Link to Entry field |
| `entry` | Entry | Entry to link to |

#### Example

```javascript
// Link a task to a project
let tasksLib = lib();
let projectsLib = libByName("Projects");

// Get the project and task
let project = projectsLib.findByKey("Website Redesign");
let task = tasksLib.lastEntry();

if (project && task) {
    // Create link from task to project
    task.link("Project", project);
    
    // Update task metadata
    task.set("ProjectStartDate", project.field("StartDate"));
    task.set("Department", project.field("Department"));
}
```

#### recalc()

Recalculate all calculated fields in the entry.
{: .fs-5 }

Triggers a recalculation of all fields that contain formulas or calculated values.

#### Example

```javascript
// Update quantities and recalculate totals
let entry = lib().lastEntry();
entry.set("Quantity", 5);
entry.set("UnitPrice", 10.99);
// Recalculate the Total field that might depend on Quantity and UnitPrice
entry.recalc();
```

#### set(name, value)

Set the value of a specified field.
{: .fs-5 }

Updates the value of the specified field. The value type should match the field type.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the field to update |
| `value` | any | Value to set (type must match field type) |

#### Example

```javascript
// Examples of setting different field types
let entry = lib().lastEntry();

// Text field
entry.set("Title", "Updated Task Title");

// Number field
entry.set("Quantity", 42);

// Date field
entry.set("DueDate", new Date().toISOString());

// Multiple-choice field
entry.set("Categories", ["Work", "Important", "Project"]);

// Checkbox field
entry.set("Completed", true);

// Link to Entry field with multiple values
let relatedEntries = [entry1, entry2].map(e => e.name).join(",");
entry.set("RelatedItems", relatedEntries);
```

#### show()

Display the entry in the user interface.
{: .fs-5 }

Opens the entry view in Memento's user interface.

#### Example

```javascript
// Create entry and show it
let entry = lib().create({
    "Title": "New Task",
    "Priority": "High"
});
entry.show(); // Display the new entry
```

#### trash()

Move the entry to the Recycle Bin.
{: .fs-5 }

Marks the entry as deleted and moves it to the Recycle Bin.

#### Example

```javascript
// Move completed old entries to trash
let oldEntries = lib().find("Status: Completed");
let monthAgo = new Date();
monthAgo.setMonth(monthAgo.getMonth() - 1);

oldEntries.forEach(entry => {
    let completionDate = new Date(entry.field("CompletionDate"));
    if (completionDate < monthAgo) {
        entry.trash();
    }
});
```

#### untrash()

Restore the entry from the Recycle Bin.
{: .fs-5 }

Removes the deleted mark from the entry and restores it from the Recycle Bin.

#### Example

```javascript
// Restore accidentally deleted entries
let deletedEntries = lib().find("Status: Active");
deletedEntries.forEach(entry => {
    if (entry.deleted) {
        entry.untrash();
        entry.set("RestoredDate", new Date().toISOString());
        entry.set("RestoredBy", "Script");
    }
});
```

#### unlink(name, entry)

Remove a link to another entry from a Link to Entry field.
{: .fs-5 }

Removes the specified entry link from the named Link to Entry field.

#### Parameters

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the Link to Entry field |
| `entry` | Entry | Entry to unlink |

#### Example

```javascript
// Remove completed tasks from project links
let project = lib().lastEntry();
let linkedTasks = project.field("Tasks");

linkedTasks.forEach(task => {
    if (task.field("Status") === "Completed") {
        project.unlink("Tasks", task);
        task.set("ProjectArchiveDate", new Date().toISOString());
    }
});
```
