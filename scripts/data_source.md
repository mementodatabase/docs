---
title: Data Source
parent: Scripts
nav_order: 3
layout: default
---

# Data Source
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

Memento Database offers several ways to automatically fill entry fields (Autofill feature). One of these ways is using Custom Data Sources - a powerful scripting mechanism that allows you to fetch data from virtually any source

Custom Data Sources give you the most flexibility by allowing you to:
- Connect to any web API or service
- Query other Memento libraries with complex logic
- Process and transform data before filling fields
- Implement custom search and data retrieval logic
- Combine multiple data sources

## Query
When a user enters a search term, Memento automatically makes it available in your script through the global variable `query`. This variable contains:
- The text entered by the user (for text searches)
- The scanned barcode (for barcode searches)

## How Custom Data Sources Work

### Basic Flow
{: .no_toc } 

1. User enters a search term in an entry field
2. The custom data source script runs using this search term (automatically available as the global variable `query`)
3. Script processes the search and must return results using the global `result()` method
4. The `result()` method expects an array of objects with this structure:
   ```javascript
   {
       title: "Display Name",     // Required - shown as main text
       desc: "Description",       // Optional - shown as subtitle
       thumb: "image_url",        // Optional - shown as thumbnail
       id: "identifier",          // Required for additional data fetch
       // ... any other properties for field mapping
   }
   ```
5. Memento displays these objects to the user in a selection list
6. User selects the desired result
7. Selected object's properties are mapped to entry fields according to defined rules

### Advanced Flow (with Additional Data)
{: .no_toc } 

Some data sources can fetch additional details after the initial search. In this case:
1. Initial search returns basic information (title, description, etc.)
2. User selects an item
3. Script fetches detailed information for the selected item
4. Memento fills entry fields with the complete data

## Setting Up a Custom Data Source

### 1. Create the Data Source
{: .no_toc } 
1. Open the Library Edit screen
2. Navigate to the Autofill tab
3. Click the "+" button
4. Choose the search type:
   - By Name (text search)
   - By Barcode (barcode scanner)
5. Select "Custom source"
6. Choose the field to use for searching

### 2. Write the Script
{: .no_toc } 
Scripts must return data in a specific format. Here's a basic template:

```javascript
// Basic search script
var results = [
    {
        title: "Item Name",        // Required
        desc: "Description",       // Optional
        thumb: "image_url",        // Optional
        id: "unique_identifier"    // Required for additional data fetch
    }
];
result(results);

// With additional data fetch
result(results, function(id) {
    // Fetch and return additional data
    return additionalData;
});
```

### 3. Map Data to Fields
{: .no_toc } 
After creating the script:
1. Close the script editor
2. Click "+" to add field mappings
3. Enter the property name from your result object
4. Select the corresponding library field
5. Repeat for each field you want to populate

## Script Requirements

### Result Object Properties
{: .no_toc } 
- `title` (required): Display name of the item
- `desc` (optional): Additional description
- `thumb` (optional): URL or reference to an image
- `id` (required for additional data): Unique identifier

### The `result()` Function
{: .no_toc } 
```javascript
result(sourceObjects, getMoreFunction)
```

- `sourceObjects`: Array of objects matching the search
- `getMoreFunction`: Optional function to fetch additional data

{: .note } 
Scripts run with the same security context as triggers

## Examples

### Fetch Data from Another Library
{: .no_toc } 
```javascript
var anotherLib = libByName("AnotherLib");         
var entries = anotherLib.find(query);
var resultArray = [];
for(var i in entries ) {
   var object = {};
   object["title"] = entries[i].title;
   object["desc"] = entries[i].description;
   object["number"] = entries[i].field("Number");
   resultArray.push(object);
   }
result(resultArray);
```

### Web API Integration
{: .no_toc } 
```javascript
// Example using a REST API
var response = http().get("https://api.example.com/search?q=" + 
    encodeURIComponent(query));
    
result(
    JSON.parse(response),
    function(id) {
        return http().get("https://api.example.com/items/" + id);
    }
);
```

