---
title: JavaScript Field
parent: Scripts
nav_order: 4
layout: default
---

# JavaScript Field
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

The JavaScript field is similar to a calculated field, but uses JavaScript expressions for calculations. Unlike other scripts in Memento Database, this field executes only in the context of an entry (has access to entry fields), not to any data. You can only read field values - modification of field values is not supported.

A script may consist of multiple operations, but only the result of the last operation will be entered into the field. For example:

```javascript
var i = 1 + 1  // Result would be 2, if this were the final statement,
i + 1          //   but instead, this is the final statement, so the result will be 3
```

## Accessing Entry Fields

Use the `field("fieldName")` function to access values of entry fields. The type of JavaScript object returned depends on the field type.

### Field Type Compatibility
{: .no_toc } 

| Memento Field Type | JavaScript Object Type | Notes |
|-------------------|------------------------|-------|
| Text | String | Direct text value |
| Number | Number | Numeric value |
| Currency | Number | Numeric value without currency symbol |
| Date/Time | Date | JavaScript Date object |
| Checkbox | Boolean | true/false value |
| Rating | Number | Numeric rating value |
| Link to Entry | Array | Array of linked entry objects |
| File/Image | Object | File metadata object |
| Location | Object | Geographic coordinates object |

## Script Execution Timing

A script can be executed in two scenarios:

1. **On Edit (Default)**: Script runs only when an entry is created or modified
   - Better performance for large libraries
   - Suitable for calculations that don't depend on current time

2. **Real-time**: Script runs both on edit and when displayed
   - Enable this option if your script contains functions that depend on current time
   - May significantly increase loading time for libraries with many entries


## Basic Examples

### Example: Product Total Price
{: .no_toc } 
Library contains fields: `Price` and `Count`
JavaScript field `Total Price`:

```javascript
field('Price') * field('Count');
```

### Example: Percentage Calculation
{: .no_toc } 
Library contains fields: `Count` and `Total`
JavaScript field `Percentage`:

```javascript
var total = field('Total');
if (total > 0)
   field('Count') * 100 / total + ' %';
else
   'None';
```

### Example: Days Between Dates
{: .no_toc } 
Library contains DateTime fields: `Start` and `End`
JavaScript field `Days`:

```javascript
moment(field('End')).diff(field('Start'), 'days');
```

### Example: Age-Based Classification
{: .no_toc } 
Library contains a `Birthday` field. Display "Youth" for people under 18, otherwise "Adult":

```javascript
var years = moment().diff(field('Birthday'), 'years');
if (years < 18)
   'Youth';
else
   'Adult';
```

## Working with Linked Entries

When you have a many-to-many Link To Entry field linking the current entry to entries in another library, you can perform calculations across those linked entries.

### Example: Sum of Linked Amounts
{: .no_toc } 
```javascript
var amountField = "Amount";        // CHANGE Amount to your amount field name
var linkedEntries = field("Them"); // CHANGE Them to your link field name
var numLE = linkedEntries.length;  // The number of linked entries

// Loop through linked entries, creating the sum
for (var lex = 0, result = 0; lex < numLE; lex++)
    result += linkedEntries[lex].field(amountField);

result;                            // The result is the SUM
```

### Example: Average of Linked Amounts
{: .no_toc } 
```javascript
var amountField = "Amount";        // CHANGE Amount to your amount field name
var linkedEntries = field("Them"); // CHANGE Them to your link field name
var numLE = linkedEntries.length;  // The number of linked entries

// Loop through linked entries, creating the sum
for (var lex = 0, result = 0; lex < numLE; lex++)
    result += linkedEntries[lex].field(amountField);

if (numLE != 0)
    result / numLE;                // The result is the AVERAGE
```

### Example: Minimum of Linked Amounts
{: .no_toc } 
```javascript
var amountField = "Amount";        // CHANGE Amount to your amount field name
var linkedEntries = field("Them"); // CHANGE Them to your link field name
var numLE = linkedEntries.length;  // The number of linked entries
var temp;

// Loop through linked entries, finding the MINIMUM
for (var lex = 0, result = 0; lex < numLE; lex++) {
    temp = linkedEntries[lex].field(amountField);
    if (temp < result)
        result = temp;
}

result;                            // The result is the MINIMUM
```

### Example: Maximum of Linked Amounts
{: .no_toc } 
```javascript
var amountField = "Amount";        // CHANGE Amount to your amount field name
var linkedEntries = field("Them"); // CHANGE Them to your link field name
var numLE = linkedEntries.length;  // The number of linked entries
var temp;

// Loop through linked entries, finding the MAXIMUM
for (var lex = 0, result = 0; lex < numLE; lex++) {
    temp = linkedEntries[lex].field(amountField);
    if (temp > result)
        result = temp;
}

result;                            // The result is the MAXIMUM
```

### Example: Order Cost Calculation
{: .no_toc } 
Library `Orders` contains orders, library `Items` contains items. Orders has a link field `Items` to the Items library. Items contains fields `Quantity` and `Price`.

To calculate order cost, create a JavaScript field `Cost`:

```javascript
var items = field('Items');        // CHANGE Items to your link field name
var cost = 0;
for (var i in items)
    cost += items[i].field('Quantity') * items[i].field('Price');

cost;                              // The result is the COMPUTED COST
```

## Working with Dates - moment.js

Enable the integrated [momentjs.com](https://momentjs.com) library for advanced date and time operations. In the field configuration, click **JavaScript Libraries** and select **moment.min.js**.

### Date Format Examples
{: .no_toc } 
If an entry contains a Date field with value "18 March 2016 14:55":

```javascript
moment(field('Date')).format('MMMMM Do YYYYY')      // March 18th 2016
moment(field('Date')).format('dddd')                // Friday
moment(field('Date')).format('dddd [Escaped] YYYY') // Friday Escaped 2016
moment(field('Date')).format('HH:mm')               // 14:55
```

### Relative Time Examples
{: .no_toc } 
```javascript
moment(field('Date')).fromNow()                     // 11 minutes ago
moment(field('Date')).startOf('day').fromNow()      // 15 hours ago
```

### Calendar Time Examples
{: .no_toc } 
```javascript
moment(field('Date')).calendar()                    // Today at 2:55 PM
moment(field('Date')).add(1, 'days').calendar()     // Tomorrow at 2:55 PM
```

## Best Practices

1. **Performance**: Use "Execute script in real time: No" unless you need current time-dependent calculations
2. **Error Handling**: Include checks for undefined or null values when accessing fields
3. **Naming**: Use descriptive variable names and add comments for complex calculations
4. **Testing**: Test your scripts with various data scenarios to ensure reliability
5. **Optimization**: Keep calculations simple and efficient for better performance



