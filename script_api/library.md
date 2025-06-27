---
title: Libraries
parent: Memento JavaScript API
nav_order: 2
layout: default
---

# Libraries
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

# Global Functions

## lib()

Get the Library object of the current event.
{: .fs-5 }

#### Returns
{: .no_toc }
Library object - the current library

#### Example
{: .no_toc }
```javascript
// Get current library and create a new entry
let currentLib = lib();
let newEntry = currentLib.create({
    "Title": "New Task",
    "Status": "Pending"
});
```

## libByName(name)

Find a library by its name.
{: .fs-5 }

Requires appropriate security permissions to access the library. Returns null if library not found or permissions are insufficient.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | The name of the library to find |

#### Returns
{: .no_toc }
Library object or null - the library with the specified name if found

#### Example
{: .no_toc }
```javascript
// Access a related library and find entries
let tasksLib = libByName("Project Tasks");
if (tasksLib) {
    let pendingTasks = tasksLib.find("Pending");
    message("Pending tasks: " + pendingTasks.length);
}
```

## libById(id)

Find a library by its ID.
{: .fs-5 }

Requires appropriate security permissions to access the library. More reliable than libByName() as IDs don't change when libraries are renamed.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `id` | string | The ID of the library to find |

#### Returns
{: .no_toc }
Library object or null - the library with the specified ID if found

#### Example
{: .no_toc }
```javascript
// Access a library using its ID
let projectsLib = libById("lib_projects_001");
if (projectsLib) {
    let activeProjects = projectsLib.find("Active");
    message("PActive projects: " + activeProjects.length);
}
```

# Library Object

The Library object provides access to library entries and operations. It can be obtained through `lib()`, `libByName()`, or `libById()`.

## Properties

| Property | Type | Description |
|:---------|:-----|:------------|
| `name` | string | The name of the library |
| `title` | string | The title of the library (alias for name) |
| `id` | string | The unique identifier of the library (Added in MDB 5.5) |

## create(values)

Create a new entry in the library with specified field values.
{: .fs-5 }

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `values` | object | Key-value pairs where keys are field names and values are field values |

#### Returns
{: .no_toc }
Entry object - the newly created entry

#### Example
{: .no_toc }

```javascript
// Create a new task with initial values
let taskLib = lib();
let newTask = taskLib.create({
    "Title": "Review Documentation",
    "Priority": "High",
    "DueDate": new Date(2024, 2, 15).getTime(),
    "Status": "Not Started"
});
```

## entries()

Get all entries in the library.
{: .fs-5 }

#### Returns
{: .no_toc }
Array of Entry objects - all entries sorted by creation time (newest first)

#### Example
{: .no_toc }
```javascript
// Get all entries and process them
let allEntries = lib().entries();
allEntries.forEach(entry => {
    if (entry.field("Status") === "Overdue") {
        entry.set("Priority", "High");
    }
});
```

## lastEntry()

Get the most recently created entry.
{: .fs-5 }

#### Returns
{: .no_toc }
Entry object or null - the newest entry or null if library is empty

#### Example
{: .no_toc }
```javascript
// Get the latest entry and copy some of its values
let latest = lib().lastEntry();
if (latest) {
    let newEntry = lib().create({
        "Category": latest.field("Category"),
        "Department": latest.field("Department"),
        "CreatedDate": new Date().getTime()
    });
}
```

## firstEntry()

Get the oldest entry in the library.
{: .fs-5 }

#### Returns
{: .no_toc }
Entry object or null - the oldest entry or null if library is empty

#### Example
{: .no_toc }
```javascript
// Archive old entries
let oldest = lib().firstEntry();
if (oldest && oldest.creationTime.getTime() < new Date('2024-01-01').getTime()) {
    oldest.set("Status", "Archived");
}
```

## fields()

Get field names defined in the library.
{: .fs-5 }

Returns an array of field names that are defined in the MAIN page and not within a Subheader. Available since Memento release 4.13.

#### Returns
{: .no_toc } 
Array of strings - field names in definition order

#### Example
{: .no_toc }
```javascript
// Get all field names and log them
let fieldNames = lib().fields();
fieldNames.forEach(fieldName => {
    log(`Field name: ${fieldName}`);
});
```

## find(query)

Search for entries matching the given query.
{: .fs-5 }

Performs a search similar to the Memento user interface search. Returns entries sorted by creation time, from newest to oldest.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `query` | string | The search query string |

#### Returns
{: .no_toc }
Array of Entry objects - entries matching the search criteria

#### Example
{: .no_toc }
```javascript
// Find all high priority tasks
let highPriorityTasks = lib().find("High");
// Process found entries
highPriorityTasks.forEach(task => {
    log(`High priority task: ${task.field("Title")}`);
});
```

## findById(id)

Find an entry by its unique ID.
{: .fs-5 }

Searches for an entry using its unique identifier. This is the most reliable way to find a specific entry.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `id` | string | The unique identifier of the entry |

#### Returns
{: .no_toc }
Entry object or null - the entry with the specified ID if found

#### Example
{: .no_toc }
```javascript
// Find entry by ID and update it
let entry = lib().findById("entry_123");
if (entry) {
    entry.set("LastChecked", new Date().getTime());
} else {
    log("Entry not found");
}
```

## findByKey(name)

Find an entry by its name field value.
{: .fs-5 }

Searches for an entry using the entry name. The library must be configured to use unique entry names for this method to work reliably.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `name` | string | The value of the Entry name field |

#### Returns
{: .no_toc } 
Entry object or null - the entry with the specified name if found

#### Example
{: .no_toc }
```javascript
// Find a project by its name
let project = lib().findByKey("Website Redesign");
if (project) {
    // Update project status
    project.set("Status", "In Progress");
    
    // Get linked tasks
    let linkedTasks = lib().linksTo(project);
    log(`Found ${linkedTasks.length} tasks linked to this project`);
}
```

## linksTo(entry)

Find entries that contain links to the specified entry.
{: .fs-5 }

Searches for entries that have a link to the specified entry in any of their Link to Entry fields.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `entry` | Entry | The entry to search for links to |

#### Returns
{: .no_toc }
Array of Entry objects - entries that link to the specified entry

#### Example
{: .no_toc }
```javascript
// Find all tasks linked to a specific project
let project = lib().findByKey("Website Redesign");
if (project) {
    let linkedTasks = lib().linksTo(project);
    
    // Process linked tasks
    linkedTasks.forEach(task => {
        log(`Linked task: ${task.field("Title")}`);
        if (task.field("Status") === "Completed") {
            // Update project progress
            project.set("CompletedTasks", project.field("CompletedTasks") + 1);
        }
    });
}
```

## show()

Display the library in the user interface.
{: .fs-5 }

Opens the library view in Memento's user interface.

#### Example
{: .no_toc }
```javascript
// Show the library after creating a new entry
let newEntry = lib().create({
    "Title": "Important Task",
    "Priority": "High"
});
lib().show(); // Display the library with the new entry
```

# Examples

### Working with Multiple Libraries
{: .no_toc }
```javascript
// Example of working with related libraries
let projectsLib = lib(); // Current library
let tasksLib = libByName("Tasks");

// Create a new project
let newProject = projectsLib.create({
    "Name": "New Website",
    "StartDate": new Date().getTime()
});

// Create associated tasks
let task1 = tasksLib.create({
    "Title": "Design mockups",
    "DueDate": new Date(2024, 2, 15).getTime()
});

// Link the task to the project using a related field ("Project" should be a relation field)
task1.set("Project" , newProject)
```

### Complex Search and Update Operations
{: .no_toc }
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
