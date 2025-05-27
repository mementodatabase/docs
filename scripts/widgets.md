---
title: Widgets
parent: Scripts
nav_order: 3
layout: default
---

# Widgets
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

Script widgets are a powerful feature in Memento Database that allow you to execute custom JavaScript code and display the result in a widget on your dashboard. These widgets contain a script that is executed before the widget is displayed and also after all refreshes of a dashboard. The result of this script as a string will be displayed in the widget. A script may consist of multiple operations, but only the result of the last operaion will be displayed. Also, you can use a limited amount of HTML tags to format the text that is displayed.

## Adding a Script Widget
To add a script widget to your dashboard, simply click on the Add Widget menu item and then select Script from the available options. Once you have added the widget, you can then start writing your JavaScript code.

## Available Methods and Objects
In script widgets, you can use all the methods and objects that are available for triggers and actions in Memento Database. Scripts run in the context of the library, just like actions for the library. 

## User Interface
Memento Database provides a [JavaScript UI API]({{site.baseurl}}/script_api/ui) that allows you to create user interfaces for your scripts in widgets. This library provides a simple way to build custom user interfaces that can be used to display data and interact with users. 

## Script Initialization
The `_initWidget` global variable is a boolean variable that is available for use in script widgets. When the script runs for the first time, the value of `_initWidget` is true. On subsequent runs of the script, `_initWidget` will be false.

```javascript
if (_initWidget) {
    // Code to be run only once goes here
}
```

{: .note }
By using the `_initWidget` variable in your script, you can ensure that your script is only executed when it needs to be, and avoid unnecessary processing and delays in loading your dashboard.

## Examples

### Count Library Entries
{: .no_toc } 
```javascript
var library = lib();
// get the number of entries in the library
var entryCount = library.entries().length;
// return the entry count as a string
"Total Entries: " + entryCount;
```

### Display Recent Entries
{: .no_toc } 
```javascript
var entries = lib().entries();
var result = '';
for (var i = 0; i < Math.min(entries.length, 5); i++) {
    var entry = entries[i];
    result += entry.title + '\n';
}
result;
```

### Group Entries by Category
{: .no_toc } 
```javascript
var entries = lib().entries();
var categories = {};
for (var i = 0; i < entries.length; i++) {
    var category = entries[i].field("Category");
    if (category in categories) {
        categories[category]++;
    } else {
        categories[category] = 1;
    }
}
var result = "<ul>";
for (var category in categories) {
    result += "<li>" + category + " - " + categories[category] + "</li>";
}
result += "</ul>";
result;
```

### UI Example
{: .no_toc } 
```javascript
ui().layout([
    ui().edit('').tag('name'), 
    ui().button('Create').action(function() { 
        lib().create({ 'Name': ui().findByTag('name').text }); 
        return true; 
    })
]);
```


