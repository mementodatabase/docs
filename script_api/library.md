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
| `notes` | string | Library notes (as specified in the library structure) |


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

## entries(options)

Get the entries of the library: all of them, or those that match a filter, sorted, a page at a time.
{: .fs-5 }

Without options, returns every entry, newest first, loading the whole library. With options, only the matching entries are read and loaded: on a large library this is much faster than loading every entry and checking it in JavaScript. The filter is described in [Filters](#filters).

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `options` | object | Optional. `where` - a filter; `sort` - an array of `{field, desc}`, the first deciding first; `limit` - the most entries to return; `offset` - how many matching entries to skip |

`sort` takes `creationTime`, `lastModifiedTime` and the fields Memento can sort a list by; `desc: true` sorts from the largest. Entries that compare equal stay newest first. Any other option is an error.

#### Returns
{: .no_toc }
Array of Entry objects - newest first unless `sort` says otherwise

#### Example
{: .no_toc }
```javascript
// Every entry
let allEntries = lib().entries();

// Open tasks due before today, the earliest first, at most 50
let overdue = lib().entries({
    where: { Status: "Open", "Due date": { $lt: new Date() } },
    sort: [{ field: "Due date" }],
    limit: 50
});
overdue.forEach(task => task.set("Priority", "High"));

// The second page of 20, the most expensive first
let page = lib().entries({ sort: [{ field: "Amount", desc: true }], limit: 20, offset: 20 });
```

## count(where)

Count the entries that match a filter.
{: .fs-5 }

Counts without loading entries. Without `where`, counts every entry of the library.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `where` | object | Optional. A filter, as described in [Filters](#filters) |

#### Returns
{: .no_toc }
number - how many entries match

#### Example
{: .no_toc }
```javascript
let open = lib().count({ Status: "Open" });
let all = lib().count();
message(open + " of " + all + " tasks are open");
```

## sum(field, where)

Sum a number field over the entries that match a filter.
{: .fs-5 }

Sums without loading entries. Entries where the field is empty are skipped; the sum is 0 when no entry matches. Without `where`, sums over every entry.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `field` | string | The name of a number field: Integer, Real number, Currency, Rating, Duration or Integer values |
| `where` | object | Optional. A filter, as described in [Filters](#filters) |

#### Returns
{: .no_toc }
number - the sum, in the values `entry.field()` reads

#### Example
{: .no_toc }
```javascript
// Expenses of October 2026
let spent = libByName("Expenses").sum("Amount", { Date: { $gte: "2026-10-01", $lt: "2026-11-01" } });
message("Spent in October: " + spent);
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

Performs a search similar to the Memento user interface search. Returns entries sorted by creation time, from newest to oldest. To select entries by the values of their fields, use `entries({ where: ... })`; passing an object to `find()` is an error.

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
    let linkedTasks = libByName("Tasks").linksTo(project);
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
    let linkedTasks = libByName("Tasks").linksTo(project);
    
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

# Filters

`entries()`, `count()` and `sum()` take a filter: an object whose keys are field names and whose values say what each field must hold. It is written like a MongoDB query.

```javascript
{
    Status: "Open",                                         // equals
    Amount: { $gte: 10, $lt: 100 },                         // every operator holds
    $or: [{ Priority: "High" }, { "Due date": { $lt: "2026-10-20" } }],
    Labels: { $all: ["home", "urgent"] },                   // a multiple-choice field has both
    Notes: null,                                            // empty
    Title: { $not: { $contains: "draft" } }
}
```

- Every key must hold; `{}` matches every entry.
- Field names ignore case, as in `entry.field()`. `creationTime` and `lastModifiedTime` are the entry's creation and last modification times, unless the library has fields with these names.
- A field's value is either the value to equal or an object of operators, not both.
- `$and` and `$or` take a non-empty array of filters. `$not` negates the operators of one field.
- Entries in the trash are never included.
- In a script widget whose settings set a filter, `entries()`, `count()` and `sum()` see only the entries that pass it.

## Operators

| Operator | Operand | Matches when the field |
|:---------|:--------|:-----------------------|
| value, `$eq` | value | equals it |
| `$ne` | value | does not equal it; an empty field matches |
| `$gt`, `$gte`, `$lt`, `$lte` | value | is greater, greater or equal, less, less or equal; an empty field never matches |
| `$in` | array | equals one of them; `null` among them matches an empty field |
| `$nin` | array | equals none of them; an empty field matches unless `null` is among them |
| `$exists` | boolean | `true`: is not empty; `false`: is empty |
| `$contains` | text | contains it, ignoring case |
| `$startsWith` | text | starts with it, ignoring case |
| `$all` | array | a multiple-choice field has every item |
| `$not` | operators | does not satisfy them |

`$in: []` and `$all: []` match nothing, `$nin: []` matches everything.

## Field types

| Field type | Operand | Operators |
|:-----------|:--------|:----------|
| Integer, Real number, Currency, Rating, Duration, Integer values | number, as `entry.field()` reads it | value, `$eq $ne $gt $gte $lt $lte $in $nin $exists $not` |
| Boolean (Checkbox) | `true` or `false` | value, `$eq $ne` |
| Date, Date/Time; `creationTime`, `lastModifiedTime` | a date, see [Dates](#dates) | as numbers |
| Single-choice list, Radio Buttons | item title | value, `$eq $ne $in $nin $exists $not` |
| Multiple-choice list, Checkboxes | item title | item, `$eq $ne $in $nin $all $exists $not` |
| Text, Hyperlink, Phone, Email | text | value, `$eq $ne $in $nin $contains $startsWith $exists $not` |

Fields of other types cannot be filtered by.

- **Numbers.** A number in quotes, `"10"`, is text, and an error on a number field.
- **Checkbox.** An unchecked box and a box never set are both `false`.
- **Choices** match item titles ignoring case, items inside folders included. On a multiple-choice field a value or `$eq` means "has this item", `$ne` "has not", `$in` "has any", `$nin` "has none", `$all` "has every"; an array as the value is an error.
- **Text** compares ignoring case, `$eq` included; `""` means empty. An encrypted field can be filtered by while its library is unlocked.
- **Empty.** `null` or `{ $exists: false }` matches an empty field, `{ $exists: true }` a filled one.

## Dates

A date operand is one of:

- a `Date` object, or milliseconds since 1970;
- `"2026-10-17"` - a whole day;
- `"2026-10-17T09:30"` or `"2026-10-17T09:30:15"` - a minute or a second, in the device's time zone;
- an instant with a zone, as `new Date().toISOString()` or JSON gives it: `"2026-10-17T09:30:00.000Z"`, `"2026-10-17T11:30:00+02:00"`.

An operand stands for the whole period it names: `{ $eq: "2026-10-17" }` matches any time of that day, `{ $lt: "2026-10-17" }` before it, `{ $lte: "2026-10-17" }` up to its end, `{ $gt: "2026-10-17" }` after it. A Date field compares by whole days of the device's time zone, so `{ $lt: new Date() }` on it means before today. The `lastModifiedTime` of an entry never edited is its creation time.

```javascript
// Entries created this month
let now = new Date();
let created = lib().count({ creationTime: { $gte: new Date(now.getFullYear(), now.getMonth(), 1) } });
```

## Errors

A filter that cannot be read stops the script with an error named `Filter Error`. Its message says what is wrong and what to write instead: an unknown field lists the library's fields, an unknown choice lists the items, an operator the field does not take lists those it does. `undefined` anywhere in a filter is an error, usually a variable that was not set; for an empty value write `null`. Regular expressions are not supported: use `$contains` or `$startsWith`.

```javascript
try {
    lib().count({ Staus: "Open" });
} catch (e) {
    log(e.name + ": " + e.message); // Filter Error: unknown field "Staus"; the library's fields: ...
}
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

### Search and Update Operations
{: .no_toc }
```javascript
// Mark the tasks in progress that are past their due date as overdue
let late = lib().entries({ where: { Status: "In Progress", DueDate: { $lt: new Date() } } });
late.forEach(task => task.set("Status", "Overdue"));
```

### Counts and Totals
{: .no_toc }
```javascript
// Tasks per status and the hours still planned, without loading the tasks
let report = "";
["Open", "In Progress", "Done"].forEach(status => {
    let count = lib().count({ Status: status });
    report += status + ": " + count + "\n";
});
let hours = lib().sum("Hours", { Status: { $ne: "Done" } });
message(report + "Hours left: " + hours);
```
