---
title: Entries
parent: Memento JavaScript API
nav_order: 3
layout: default
---

# Entries
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

# Global Functions

## entry()

Get the Entry object of the current event.
{: .fs-5 }

Returns a clone of the actual Entry object. If the entry is saved, the clone becomes the actual entry. If `cancel()` is called, the clone and any changes are discarded.

{: .note }
Not available during 'Creating an entry > Opening an Entry Edit card' event (use `entryDefault()` instead)

#### Returns
{: .no_toc } 
Entry object - the current entry

#### Example
{: .no_toc }
```javascript
// Get the current entry and update its status
let currentEntry = entry();
currentEntry.set("Status", "In Progress");
```

## entryDefault()

Get default field values for a not-yet-created entry.
{: .fs-5 }

Only available during 'Creating an entry > Opening an Entry Edit card' event. Used to set default values for new entries.

#### Returns
{: .no_toc }
DefaultEntry object - contains default values for the new entry

#### Example
{: .no_toc }
```javascript
// Set default values for a new entry
let defaults = entryDefault();
defaults.set("Status", "New");
defaults.set("CreatedDate", new Date().getTime());
```

## buildDefaultEntry()

Customize default field values during entry creation or update events.
{: .fs-5 }

Available during 'Creating an entry' or 'Updating an entry' trigger events. Specifically designed for the 'Opening an Entry Edit card' phase.

#### Properties
{: .no_toc }

| Property | Description |
|:---------|:------------|
| `created` | Indicates a new empty entry |
| `duplicated` | Indicates a duplicate of an existing entry |
| `prefilled` | Indicates creation from a template |

#### Returns
{: .no_toc }
DefaultEntry object - contains methods for setting default values

#### Example
{: .no_toc }
```javascript
// Set different defaults based on creation method
if (buildDefaultEntry().duplicated) {
    buildDefaultEntry().set("Status", "Duplicate");
    buildDefaultEntry().set("DuplicatedDate", new Date().getTime());
} else if (buildDefaultEntry().created) {
    buildDefaultEntry().set("Status", "New");
    buildDefaultEntry().set("Priority", "Medium");
}
```

# Entry Object

The Entry object represents a single entry in a library and provides methods for accessing and modifying its field values.

## Properties

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

## field(name)

Get the value of a specified field.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the field to retrieve |

#### Returns
{: .no_toc }

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
{: .no_toc }

```javascript
// Get various field types
let entry = lib().lastEntry();

// Text field
let title = entry.field("Title");
message(`Title: ${title}`);

// Number field
let quantity = entry.field("Quantity");
message(`Quantity: ${quantity}`);

// Date field
let dueDate = entry.field("DueDate");
message(`Due date: ${dueDate.toLocaleDateString()}`);

// Multiple-choice field
let categories = entry.field("Categories");
categories.forEach(category => {
    message(`Category: ${category}`);
});

// Link to Entry field
let linkedTasks = entry.field("RelatedTasks");
linkedTasks.forEach(task => {
    message(`Related task: ${task.field("Title")}`);
});
```

## images(name)

Get images associated with an image field.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the image field |

#### Returns
{: .no_toc }

Array of image objects, each containing:
- `caption`: string - Get or set the image caption
- `uri`: string - Get the image URI

#### Example
{: .no_toc }

```javascript
// Work with images in an entry
let entry = lib().lastEntry();
let photos = entry.images("Photos");

// Process all images
photos.forEach(photo => {
    message(`Image URI: ${photo.uri}`);
    message(`Caption: ${photo.caption}`);
    
    // Update caption if needed
    if (!photo.caption) {
        photo.caption = "Photo taken on " + new Date().toLocaleDateString();
    }
});
```

## link(name, entry)

Add a link to another entry in a Link to Entry field.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the Link to Entry field |
| `entry` | Entry | Entry to link to |

#### Example
{: .no_toc }

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

## recalc()

Recalculate all calculated fields in the entry.

#### Example
{: .no_toc }

```javascript
// Update quantities and recalculate totals
let entry = lib().lastEntry();
entry.set("Quantity", 5);
entry.set("UnitPrice", 10.99);
// Recalculate the Total field that might depend on Quantity and UnitPrice
entry.recalc();
```

## set(name, value)

Set the value of a specified field.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the field to update |
| `value` | any | Value to set (type must match field type) |

#### Example
{: .no_toc }

```javascript
// Examples of setting different field types
let entry = lib().lastEntry();

// Text field
entry.set("Title", "Updated Task Title");

// Number field
entry.set("Quantity", 42);

// Date field
entry.set("DueDate", new Date().getTime());

// Multiple-choice field
entry.set("Categories", ["Work", "Important", "Project"]);

// Checkbox field
entry.set("Completed", true);

// Link to Entry field with multiple values
entry.set("RelatedItems",  [entry1, entry2]);
```

## show()

Display the entry in the user interface.

#### Example
{: .no_toc }

```javascript
// Create entry and show it
let entry = lib().create({
    "Title": "New Task",
    "Priority": "High"
});
entry.show(); // Display the new entry
```

## trash()

Move the entry to the Recycle Bin.

#### Example
{: .no_toc }

```javascript
// Move completed old entries to trash
let oldEntries = lib().find("Status: Completed");
let monthAgo = new Date();
monthAgo.setMonth(monthAgo.getMonth() - 1);

oldEntries.forEach(entry => {
    let completionDate = entry.field("CompletionDate");
    if (completionDate.getTime() < monthAgo.getTime()) {
        entry.trash();
    }
});
```

## untrash()

Restore the entry from the Recycle Bin.

#### Example
{: .no_toc }

```javascript
// Restore accidentally deleted entries
lib().entries().forEach(entry => {
    if (entry.deleted) {
        entry.untrash();
        entry.set("RestoredDate", new Date().getTime());
        entry.set("RestoredBy", "Script");
    }
});
```

## unlink(name, entry)

Remove a link to another entry from a Link to Entry field.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | Name of the Link to Entry field |
| `entry` | Entry | Entry to unlink |

#### Example
{: .no_toc }

```javascript
// Remove completed tasks from project links
let project = lib().lastEntry();
let linkedTasks = project.field("Tasks");

linkedTasks.forEach(task => {
    if (task.field("Status") === "Completed") {
        project.unlink("Tasks", task);
        task.set("ProjectArchiveDate", new Date().getTime());
    }
});
```
