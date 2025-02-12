---
title: SQL
parent: Memento JavaScript API
nav_order: 5
layout: default
---

# SQL
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

The SQL Query Interface in Memento Database allows you to execute SELECT SQL queries using SQLite syntax. This interface enables complex calculations, data aggregation, and result transformation directly within the app.

# Global Functions

## sql(query)
Executes a SELECT SQL query and returns an SQL result object.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| query | String | The SELECT SQL query to execute using SQLite syntax |

#### Returns
{: .no_toc } 
Returns an SQL object that provides access to the query results.

#### Example
{: .no_toc } 
```javascript
let result = sql("SELECT COUNT(*) as total FROM MyLibrary");
log("Total records: " + result.asInt());
```

# SQL Object

The SQL object provides methods to access and manipulate query results in different formats.

## asObjects()
Converts the query results into an array of JavaScript objects where each object represents a row with column names as properties.

#### Returns
{: .no_toc } 
Array of objects where each object represents a row from the query results.

#### Example
{: .no_toc } 
```javascript
let books = sql("SELECT id, Title, Author FROM MyLibrary").asObjects();
books.forEach(book => {
    log(`Book: ${book.Title} by ${book.Author}`);
});
```

## asEntries()
{: .no_toc } 
Returns query results as a list of Memento Database entry objects.

#### Returns
{: .no_toc } 
Array of Entry objects representing the matching database records.

{: .note }
The SQL query must include the `id` column

{: .note }
Only fields specified in the SELECT statement will be included in the entries

{: .note }
For better performance, specify needed fields instead of using SELECT *

#### Example
{: .no_toc } 
```javascript
let highRatedBooks = sql("SELECT id, Title, Rating FROM MyLibrary WHERE Rating > 4").asEntries();
highRatedBooks.forEach(entry => {
    log(`High rated book: ${entry.field("Title")}`);
});
```

## asInt()
Returns the first column of the first row as an integer value.

#### Returns
{: .no_toc } 
Integer value from the first column of the first row.

#### Example
{: .no_toc } 
```javascript
let bookCount = sql("SELECT COUNT(*) FROM MyLibrary").asInt();
log(`Total number of books: ${bookCount}`);
```

## asDouble()
Returns the first column of the first row as a floating-point number.

#### Returns
{: .no_toc } 
Double (floating-point) value from the first column of the first row.

#### Example
{: .no_toc } 
```javascript
let avgRating = sql("SELECT AVG(Rating) FROM MyLibrary").asDouble();
log(`Average book rating: ${avgRating.toFixed(2)}`);
```

## asString()
Returns the first column of the first row as a string.

#### Returns
{: .no_toc } 
String value from the first column of the first row.

#### Example
{: .no_toc } 
```javascript
let firstAuthor = sql("SELECT Author FROM MyLibrary ORDER BY Author LIMIT 1").asString();
log(`First author alphabetically: ${firstAuthor}`);
```

# Table and Column Naming Conventions
For detailed information about table and column naming conventions and working with linked records, refer to the [SQL Explorer documentation](https://help.mementodatabase.com/?ht_kb=sql-explorer).

For additional SQLite syntax reference, see the [SQLite documentation](https://www.sqlite.org/lang.html).
