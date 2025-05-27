---
title: Actions
parent: Scripts
nav_order: 2
layout: default
---

# Actions
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

Actions are special scripts that users can trigger by pressing toolbar buttons in Memento Database. They can be executed from various locations and run in the background. Actions share the same security parameters and available functions as trigger scripts.

## Action Types

Actions can be created in four different contexts:
- **Library Actions**: Execute operations on the entire library
- **Entry Actions**: Perform tasks on a single entry (appears in Entry View card)
- **Entries in List**: Display action buttons for each entry row in the list view
- **Bulk Actions**: Process multiple selected entries simultaneously

## Creating an Action
1. Navigate to **Library > Menu > Scripts**
2. Click the **+** button
3. Select **Actions**
4. Choose the action context (Library, Entry, Entries in List, or Bulk actions)

## Action Visibility

- Library actions appear in the main toolbar
- Entry actions appear in the Entry View card toolbar
- Entries in List actions show as buttons in each entry row
- Bulk actions are available in the menu when multiple entries are selected

On the View tab, you can configure:
1. **User Access**
   - Specify which users can see and use the action

2. **Entry Filters** (for Entry View card and Entries in List actions)
   - Configure filters similar to entry list filters
   - The action button will only be visible for entries matching the filter conditions

## Arguments

Arguments allow users to input values when running an action.

### Setting Up Arguments
{: .no_toc } 
1. Open the Action Edit card
2. Navigate to the Arguments tab
3. Click the **+** button to add new arguments

### Using Arguments in Scripts
{: .no_toc } 
Access argument values using the `arg()` function:

```javascript
// Example: Getting a text argument value
let userInput = arg("inputName");

// Example: Getting a numeric argument
let quantity = arg("amount");
```

The return type of `arg()` matches the argument type, similar to the `field()` function.

## Available Functions and Objects

### Library Context
{: .no_toc } 
Use `lib()` to access the current library
```javascript
let currentLib = lib();
```

### Entry Context
{: .no_toc } 
Use `entry()` to access the current entry
```javascript
let currentEntry = entry();
```

### Bulk Context 
{: .no_toc } 
Use `selectedEntries()` to access selected entries
```javascript
let entries = selectedEntries();
for (let entry of entries) {
    // Process each selected entry
}
```

## Examples

### Library Action
{: .no_toc } 
```javascript
// Count total entries in library
let total = lib().entries().length;
message("Total entries: " + total);
```

### Entry Action
{: .no_toc } 
```javascript
// Update entry status
entry().set("status", "Processed");
```

### Entries in List
{: .no_toc } 
```javascript
// Quick status toggle
let status = entry().field("status");
entry().set("status", status === "Active" ? "Inactive" : "Active");
```

### Bulk Action
{: .no_toc } 
```javascript
// Mark multiple entries as reviewed
for (let entry of selectedEntries()) {
    entry.set("reviewed", true);
}
```
