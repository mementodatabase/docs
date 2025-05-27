---
title: Messages
parent: Memento JavaScript API
nav_order: 9
layout: default
---

# Messages
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

# Global Functions

## message(text)
Displays a brief notification message to the user.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| text | string | The text content of the notification to display |

#### Example
{: .no_toc } 
```javascript
message("Operation completed successfully");
```

## log(text)
Writes a line to the script's log file. 

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| text | string | The text to write to the log file |

#### Example
{: .no_toc } 
```javascript
log("Starting data processing...");
```

## dialog()
Creates a dialog builder for constructing and displaying modal dialogs.

#### Returns
{: .no_toc } 
Returns a Dialog object that can be used to configure and display the dialog.

#### Example
{: .no_toc } 
```javascript
const myDialog = dialog()
    .title("Confirm Action")
    .text("Are you sure you want to proceed?")
    .positiveButton("Yes", () => {
        log("User confirmed action");
        return true;
    })
    .negativeButton("No", () => {
        log("User cancelled action");
        return true;
    })
    .show();
```

## notification()
Creates a notification builder for constructing and displaying system notifications.

#### Returns
{: .no_toc } 
Returns a Notification object that can be used to configure and display the notification.

#### Example
{: .no_toc } 
```javascript
notification()
    .id(1)
    .title("New Message")
    .text("You have received a new message")
    .bigText("Long message content here...")
    .show();
log("Notification displayed");
```

# Dialog Object
A builder object for creating and customizing dialog windows.

## title(text)
Sets the title of the dialog.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| text | string | The title text to display |

## text(text)
Sets the main content text of the dialog.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| text | string | The main message text |

## view(uiObject)
Sets a custom view for the dialog.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| uiObject | UI Object | A custom UI object to display in the dialog |

## positiveButton(text, callback)
Adds a positive action button to the dialog.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| text | string | The button text |
| callback | function | Function to execute when button is clicked |

## negativeButton(text, callback)
Adds a negative action button to the dialog.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| text | string | The button text |
| callback | function | Function to execute when button is clicked |

## neutralButton(text, callback)
Adds a neutral action button to the dialog.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| text | string | The button text |
| callback | function | Function to execute when button is clicked |

## autoDismiss(boolean)
Controls whether the dialog automatically closes after button click.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| boolean | boolean | If false, dialog only closes when callback returns true |

## show()
Displays the configured dialog.

# Notification Object
A builder object for creating and customizing system notifications.

## id(value)
Sets the unique identifier for the notification.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| value | integer | Unique ID for the notification |

## title(text)
Sets the notification title.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| text | string | The title text |

## text(content)
Sets the notification content text.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| content | string | The main notification text |

## bigText(content)
Sets the expanded notification text.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| content | string | The expanded text content |

## smallIcon(icon)
Sets the small notification icon.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| icon | iconCode | The icon code to use |

## largeIcon(icon)
Sets the large notification icon.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| icon | iconCode/URL | Icon code or image URL |

## alertOnce()
Configures the notification to alert only once.

## show()
Displays the configured notification.
