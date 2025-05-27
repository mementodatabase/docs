---
title: User Interface
parent: Memento JavaScript API
nav_order: 12
layout: default
---

# User Interface
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

# Overview
The `ui()` method is a JavaScript API provided by Memento Database that grants access to a framework of methods and objects for creating user interfaces (UIs) within Memento widget scripts or dialog boxes in other scripts. With this method, developers can create various UI elements such as text labels, buttons, images, layers, and checkboxes.

#### Example
{: .no_toc }
```javascript
ui().text('Hello, world!');
```
In this example, the `ui()` method is called to create a new UI object, which is then used to create a text label with the text "Hello, world!".

{: .note }
A script must return one UI object.

{: .note }
If you want to display multiple UI objects, you can use a layout object, which can hold multiple objects.

#### Example
{: .no_toc }
```javascript
ui().layout([
  ui().text('This is a text label'),
  ui().button('Click me!'),
  ui().image('https://example.com/myimage.png')
]);
```
In this example, the layout object is created by calling the `layout()` method on a UI object and passing in multiple UI objects as arguments. The layout object contains a text label, a button, and an image, which are grouped together within a single view.

# Base Methods

## Define size of an element
{: .no_toc }
- **`width(width: int)`**: Sets the width of the UI element in pixels.
- **`height(height: int)`**: Sets the height of the UI element in pixels.
- **`width_match_parent()`**: Sets the width of the UI element to match the parent container's width.
- **`height_match_parent()`**: Sets the height of the UI element to match the parent container's height.
- **`width_wrap_content()`**: Sets the width of the UI element to wrap the content.
- **`height_wrap_content()`**: Sets the height of the UI element to wrap the content.

## Weight
{: .no_toc }
- **`weight(weight: int)`**: Sets the weight of the UI element in a layout.

## Tag
{: .no_toc }
- **`tag(text: string)`**: Sets the tag for the UI element. This tag can be used to find and manipulate the element later during events such as button clicks.

#### Example
{: .no_toc }

```javascript
ui().button('Button').width(100).height(50).tag('button1');
```
In this example, a button UI element is created with a width of 100 pixels, a height of 50 pixels, and the tag "button1" is set for later reference during events such as button clicks.

# Text Element
The text object is used to create a text label.

#### Syntax
{: .no_toc }
```javascript
ui().text(text: string)
```
- **text** (required): The text to be displayed in the label.

#### Example
{: .no_toc }
```javascript
ui().text('Hello');
```
In this example, the `text()` method is called on a UI object to create a new text label with the text "Hello".

## Font Settings
{: .no_toc }
Font settings can be applied to text objects using the `font()` method.
```javascript
ui().text(text: string).font(fontOptions)
```
- **fontOptions** (required): An object containing font settings.
  - **size** (optional): The font size in pixels.
  - **color** (optional): The font color as a string or hexadecimal value.
  - **style** (optional): The font style, such as "bold" or "italic".

```javascript
ui().text('Hello').font({ size: 10, color: 'red', style: 'bold' });
```
In this example, a text object is created with the text "Hello" and the font settings are set to a size of 10 pixels, red color, and bold style.

# Layout Element
The layout object is used to group multiple UI objects together within a single view.

#### Syntax
{: .no_toc }
```javascript
ui().layout(children: Object[])
```
* children (required): An array of UI objects to be arranged either horizontally in a single row or vertically in a single column.

#### Example
{: .no_toc }
```javascript
ui().layout([
  ui().text('This is a text label'),
  ui().button('Click me!'), 
  ui().image('https://example.com/myimage.png')
]);
```

In this example, the layout object is created by calling the layout() method on a UI object and passing in an array of multiple UI objects as an argument. The layout object contains a text label, a button, and an image, which are grouped together within a single view.

{: .note }
By default, the layout object arranges its children vertically in a single column. However, it is possible to display the children horizontally by calling the horizontal() method.

# Button Element
The button object is used to create a button within the app.

#### Syntax
{: .no_toc }
```javascript
ui().button(title: string)
```
* title (required): The text to be displayed on the button.

#### Example
{: .no_toc }
```javascript
ui().button('Click me!');
```

In this example, the button() method is called on a UI object to create a new button with the title "Click me!".

## Action
{: .no_toc }

The action() method can be used to set a callback function for the button. This function will be called when the button is clicked.
```javascript
ui().button('Click me!').action(function() { message('Hello'); });
```
In this example, a button object is created with the title "Click me!" and a callback function is set to display a message when the button is clicked.

## Icon
{: .no_toc }

The icon() method can be used to set an icon for the button. If no title is set for the button, a small button with an icon will be displayed.
```javascript
ui().button().icon('nova:add-circle-1.png');
```

In this example, a small button with the "add" icon will be displayed.

## Refreshing
{: .no_toc }
If you want to refresh the libraries list or widgets UI after the button's action, you can simply return true in the action function.
```javascript
ui().button('Add Entry').action(function() { lib().create({ 'Title': 'New record' }); return true; });
```

In this example, a new entry will be created in the library when the button is clicked, and the libraries list or widgets UI will be refreshed.

# Editor Element
The editor object is used to create a text editor.

#### Syntax
{: .no_toc }
```javascript
ui().edit(text: string)
```
* text (optional): The initial text to be displayed in the editor.

#### Example
{: .no_toc }
```javascript
ui().edit('Type your text here');
```
In this example, the edit() method is called on a UI object to create a new text editor with the initial text "Type your text here".

## Properties
{: .no_toc }

The editor object has two properties for retrieving and modifying the text in the editor, respectively.
```javascript
var myEditor = ui().edit('Type your text here'); 
var currentText = myEditor.text; 
myEditor.text = 'New text';
```
In this example, a new editor object is created with the initial text "Type your text here". The current text in the editor is then retrieved and stored in the currentText variable using the text property. Finally, the text in the editor is changed to "New text" using the text property.

## Tag
{: .no_toc }
The tag() method can be used to assign a tag to the editor object. This tag can be used to identify and manipulate the editor object later on.
```javascript
ui().layout([
    ui().edit('').tag('name'), 
    ui().button('Create').action(function() { 
        lib().create({ 'Name': ui().findByTag('name').text }); 
        return true; 
    })
]);
```
In this example, a layout object is created with an editor object assigned the tag name "name" and a button object with an action function that retrieves the text from the editor object using the findByTag() method and creates a new entry in the library.

# Checkbox Element

The checkbox object is used to create a checkbox UI element.

#### Syntax
{: .no_toc }
```javascript
ui().checkbox(title: string, value: boolean)
```
- title (required): The title or label to be displayed next to the checkbox.
- value (required): The initial value of the checkbox (true for checked, false for unchecked).

#### Example
{: .no_toc }
```javascript
ui().checkbox('Enable notifications', true);
```
In this example, the checkbox() method is called on a UI object to create a new checkbox with the title "Enable notifications" and an initial value of true (checked).

## Properties
{: .no_toc }
The checkbox object has one property for retrieving and modifying the value of the checkbox.
```javascript
var myCheckbox = ui().checkbox('Enable notifications', true); 
var currentValue = myCheckbox.checked; 
myCheckbox.checked = false;
```
In this example, a new checkbox object is created with the title "Enable notifications" and an initial value of true (checked). The current value of the checkbox is then retrieved and stored in the currentValue variable using the checked property. Finally, the value of the checkbox is changed to false (unchecked) using the checked property.

## onChange()
{: .no_toc }

The onChange() method can be used to add a change listener to the checkbox object. This listener will be called whenever the value of the checkbox is changed by the user.
```javascript
ui().layout([
    ui().checkbox('Enable notifications', true).onChange(function(value) { 
        if (value) { 
            message('Notifications enabled'); 
        } else { 
            message('Notifications disabled'); 
        } 
    })
]);
```
In this example, a layout object is created with a checkbox object that has the title "Enable notifications" and an initial value of true (checked). The onChange() method is then called to add a change listener to the checkbox object that displays a message depending on the new value of the checkbox.

# Choice Box Element

The choiceBox object is used to create a choice box UI element within the app.

#### Syntax
{: .no_toc }
```javascript
ui().choiceBox(selected: number, items: string[])
```
- selected (required): The index of the item that should be selected by default.
- items (required): An array of string values representing the items in the choice box.

#### Example
{: .no_toc }
```javascript
ui().choiceBox(0, ['Option 1', 'Option 2', 'Option 3']);
```
In this example, the choiceBox() method is called on a UI object to create a new choice box with three options and the first option selected by default.

## Properties
{: .no_toc }
The choiceBox object has one property for retrieving and modifying the selected item.

```javascript
var myChoiceBox = ui().choiceBox(0, ['Option 1', 'Option 2', 'Option 3']); 
var currentSelection = myChoiceBox.selected; 
myChoiceBox.selected = 1;
```
In this example, a new choiceBox object is created with three options and the first option selected by default. The current selection of the choice box is then retrieved and stored in the currentSelection variable using the selected property. Finally, the selected item is changed to the second option using the selected property.

## onChange()
{: .no_toc }
The onChange() method can be used to add a change listener to the choiceBox object. This listener will be called whenever the selected item is changed by the user.
```javascript
ui().layout([
    ui().choiceBox(0, ['Option 1', 'Option 2', 'Option 3']).onChange(function(position) { 
        message('Selected option: ' + position); 
    })
]);
```
In this example, a layout object is created with a choiceBox object that has three options and the first option selected by default. The onChange() method is then called to add a change listener to the choiceBox object that displays a message showing the index of the new selected item.

# Image Element

The image object is used to create an image UI element.

#### Syntax
{: .no_toc }
```javascript
ui().image(url: string)
```
* url (required): The URL or file path of the image to be displayed.

#### Example
{: .no_toc }
```javascript
ui().image(lib().entries()[0].field('Photo')[0]).height(200)
```
In this example, the image() method is called on a UI object to create a new image element. The URL of the image is retrieved from the 'Photo' field of the first entry in the library, and the height of the image is set to 200 pixels.

# Pages Element

The pages object is used to create a series of pages within the app that display UI elements.

#### Syntax
{: .no_toc }
```javascript
ui().pages(children: Object[])
```
* children (required): An array of UI objects to be displayed on separate pages.

#### Example
{: .no_toc }
```javascript
let images = []
let entries = lib().entries()
for (var i in entries) {
    images.push(ui().image(entries[i].field('Photo')[0]).height(200))
}
ui().pages(images)
```
In this example, a new pages object is created using the ui().pages() method, and an array of UI objects (images) is passed as the children parameter. The images are retrieved from the 'Photo' field of all entries in the database and displayed on separate pages, each with a height of 200 pixels.

{: .note }
The ui().pages() method returns a pages object that can be used to display multiple UI elements on separate pages.

# Save State
Widgets can be rebuilt after updating libraries, syncing with the cloud, rotating the device, and other events. If you want your view to not reset to its default state (e.g. a text editor), you can set a unique tag for the view.

#### Example
{: .no_toc }
```javascript
ui().edit('default text').tag('editor_tag')
```
In this example, a new editor object is created with the default text 'default text', and a unique tag 'editor_tag' is assigned to the editor view. When a unique tag is assigned to a view, its state will be saved and restored when the widget is rebuilt after an event.

# Init Script Execution

Memento can run widget scripts to display their results after various events such as displaying, refreshing libraries, or syncing libraries with the cloud. However, if you want to run a part of the script only once (e.g. to initialize variables), you can use the _initWidget variable to run code only one time.

#### Syntax
{: .no_toc }
```javascript
if (_initWidget) {
    // code to run only once
}
```

#### Example
{: .no_toc }
```javascript
var counter;
if (_initWidget) {
    counter = 0;
}
counter++;
```
In this example, a variable counter is initialized only when the widget is first loaded. Subsequent loads of the widget will skip the initialization step and increment the existing counter value.

{: .note }
The _initWidget variable is automatically set to true when the widget is first loaded, and false for all subsequent loads.

{: .note }
Use this variable to initialize variables or perform other one-time actions when the widget is first loaded, without triggering them during subsequent loads.
