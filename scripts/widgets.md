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

Script widgets are a powerful feature in Memento Database that allow you to execute custom JavaScript code and display the result in a widget on your dashboard. These widgets contain a script that is executed before the widget is displayed and also after all refreshes of a dashboard. The result of this script as a string will be displayed in the widget. A script may consist of multiple operations, but only the result of the last operaion will be displayed. The text is shown as HTML, but only a limited set of tags is supported: see Formatting Text with HTML below.

## Adding a Script Widget
To add a script widget to your dashboard, simply click on the Add Widget menu item and then select Script from the available options. Once you have added the widget, you can then start writing your JavaScript code.

## Available Methods and Objects
In script widgets, you can use all the methods and objects that are available for triggers and actions in Memento Database. Scripts run in the context of the library, just like actions for the library. 

## User Interface
Memento Database provides a [JavaScript UI API]({{site.baseurl}}/script_api/ui) that allows you to create user interfaces for your scripts in widgets. This library provides a simple way to build custom user interfaces that can be used to display data and interact with users. 

## Formatting Text with HTML
The string the script returns is shown as HTML: Android converts it to styled text. Only the tags listed below are supported. Any other tag is ignored and only its text is shown, so a `<table>` runs all its cells together. For tables, columns, images and anything else beyond styled text, build the widget with the [JavaScript UI API]({{site.baseurl}}/script_api/ui).

{: .important }
As in a web page, line breaks (`\n`) and repeated spaces in the text are shown as a single space. Use `<br>` to start a new line, or put each line in its own `<p>` or `<div>`.

| Tags | Result |
|:--|:--|
| `<br>` | A line break |
| `<p>`, `<div>` | A block on its own line |
| `<h1>` … `<h6>` | A bold heading on its own line, from 1.5 (`h1`) to 1 (`h6`) times the text size |
| `<b>`, `<strong>` | Bold |
| `<i>`, `<em>`, `<cite>`, `<dfn>` | Italic |
| `<u>` | Underlined |
| `<s>`, `<strike>`, `<del>` | Struck through |
| `<big>`, `<small>` | Text 1.25 or 0.8 times the size |
| `<sup>`, `<sub>` | Superscript, subscript |
| `<tt>` | Monospace |
| `<font color="…" face="…">` | Text color; font family, such as `monospace`, `serif` or `sans-serif` |
| `<span style="…">` | Only the styles below |
| `<ul>`, `<li>` | A bulleted list |
| `<blockquote>` | An indented quote with a bar |
| `<a href="…">` | Shown as a link, but tapping it does not open it |

The `style` attribute supports only these properties:

- `color`, `background-color` (or `background`) and `text-decoration: line-through` on `<span>`, `<p>` and `<li>`;
- `text-align: start`, `center` or `end` on `<p>`, `<div>`, `<h1>` … `<h6>`, `<ul>`, `<li>` and `<blockquote>`.

Colors are written as `#RRGGBB` or by name: `black`, `white`, `gray` (`grey`), `darkgray`, `lightgray`, `silver`, `red`, `maroon`, `green`, `lime`, `olive`, `blue`, `navy`, `teal`, `cyan` (`aqua`), `magenta` (`fuchsia`), `purple`, `yellow`. Short `#RGB`, `rgb()` and other color names do not work.

Not supported: `<table>`, `<tr>`, `<td>` and `<th>`; `<ol>` (its items get bullets, not numbers); `<img>` (a placeholder icon is shown instead of the picture); `<hr>`, `<pre>`, `<style>`, `<script>` and `class`; any other CSS property, such as `font-size`, `font-weight`, `margin`, `padding`, `width` or `border`.

{: .note }
Text from entries may contain `<` or `&`. Replace them with `&lt;` and `&amp;` before adding it to the HTML, or it may be taken for a tag.

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
    result += entry.title + '<br>';
}
result;
```

### Group Entries by Category
{: .no_toc } 
```javascript
var entries = lib().entries();
var categories = {};
for (var i = 0; i < entries.length; i++) {
    var category = entries[i].field("Role");
    if (category in categories) {
        categories[category]++;
    } else {
        categories[category] = 1;
    }
}
var result = "";
for (var category in categories) {
    result += "• " + category + " - " + categories[category] + "<br>";
}
result;
```

### Formatted Summary
{: .no_toc } 
```javascript
function escape(text) {
    return String(text).replace(/&/g, '&amp;').replace(/</g, '&lt;');
}
var entries = lib().entries();
var html = '<h3>Recent entries</h3><ul>';
for (var i = 0; i < Math.min(entries.length, 5); i++) {
    var entry = entries[i];
    html += '<li><b>' + escape(entry.title) + '</b>';
    if (entry.description) html += ' <font color="#808080">' + escape(entry.description) + '</font>';
    html += '</li>';
}
html += '</ul><small>Total: ' + entries.length + '</small>';
html;
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


