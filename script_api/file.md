---
title: Files
parent: Memento JavaScript API
nav_order: 4
layout: default
---

# Files
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

The File API enables reading and writing files on the device's internal memory or SD card. All file operations are performed through the `File` object, which is obtained using the global `file()` function or the `file` property of a [Http.download()]({% link script_api/http.md %}#downloadurl-headers) result.

{: .note }
Scripts require read/write file permissions. In the Android app, you must select a folder that your script will have access to through the script permission dialog.


# Global functions

## file(name)
Creates or opens a file for read/write operations.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|-----------|------|-------------|
| name | String | File name in the chosen accessible folder. For Desktop app, specify full file path. |

#### Returns
{: .no_toc }
Returns a `File` object for the specified file.

#### Example
{: .no_toc }
```javascript
let f = file("data.txt");
f.writeLine("Hello World");
f.close();
log("File created successfully");
```

# File Object

Represents a file on the device's storage system and provides methods for reading and writing operations.

## Properties
{: .no_toc }

| Property | Type | Description |
|----------|------|-------------|
| name | String | Read-only file name or path used to open the file. For downloaded files, this is the full temporary path. Updated after `rename()`. |
| exists | Boolean | `true` if the file exists, `false` otherwise |
| length | Number | Size of the file in bytes. Returns 0 if file doesn't exist |

## rename(newName)
Physically renames the file within its current directory and updates its `name` property. This method also supports files in the folder selected through the Android script permission dialog.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|-----------|------|-------------|
| newName | String | The new filename, including its extension, such as `report.pdf`. Must not be empty, `.` or `..`, or contain directory separators or a null character. |

#### Returns
{: .no_toc }

The same File object, allowing method chaining or direct use in [Entry.set(name, value)]({% link script_api/entry.md %}#setname-value).

Renaming requires file write permission. Open text streams are closed and pending writes are saved before the rename. An existing destination file is never overwritten. If renaming fails, an error is thrown and `name` retains its previous value. Renaming to the current name leaves the file unchanged.

For a file returned by [Http.download(url, headers)]({% link script_api/http.md %}#downloadurl-headers), `rename` changes the temporary file itself. A later call to `entry.set` creates a separate copy in the library's file storage.

#### Example
{: .no_toc }

```javascript
var result = http().download("https://example.com/report.pdf");
if (result.code >= 200 && result.code < 300) {
    var document = result.file.rename("report.pdf");
    log("Renamed file: " + document.name);
    entry().set("Document", document);
}
```

## close()
Closes the file and saves any written content. The file must be closed after writing operations.

#### Example
{: .no_toc }
```javascript
let f = file("test.txt");
f.writeLine("Some content");
f.close();
log("File closed successfully");
```

## getLineNumber()
Returns the current line position in the file.

#### Returns
{: .no_toc }
Returns a number representing the current line position.

#### Example
{: .no_toc }
```javascript
let f = file("test.txt");
f.readLine();
log("Current line: " + f.getLineNumber());
f.close();
```

## readAll()
Reads all lines from the file and automatically closes it.

#### Returns
{: .no_toc }
Returns an array containing all lines from the file.

#### Example
{: .no_toc }
```javascript
let f = file("test.txt");
let lines = f.readAll();
log("File contents: " + lines.join(", "));
```

## readChar()
Reads the next character from the file stream.

#### Returns
{: .no_toc }
Returns a single character as a string.

#### Example
{: .no_toc }
```javascript
let f = file("test.txt");
let char = f.readChar();
log("First character: " + char);
f.close();
```

## readLine()
Reads the next line from the file stream.

#### Returns
{: .no_toc }
Returns a string containing the next line.

#### Example
{: .no_toc }
```javascript
let f = file("test.txt");
let line = f.readLine();
log("First line: " + line);
f.close();
```

## readLines()

Reads all remaining lines from the current position.

#### Returns
{: .no_toc }
Returns an array containing the remaining lines.

#### Example
{: .no_toc }
```javascript
let f = file("test.txt");
let lines = f.readLines();
log("Remaining lines: " + lines.join(", "));
f.close();
```

## write(text)
Writes text to the file stream without adding a newline.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|-----------|------|-------------|
| text | String | Content to write to the file |

#### Example
{: .no_toc }
```javascript
let f = file("test.txt");
f.write("Hello ");
f.write("World");
f.close();
log("Content written successfully");
```

## writeLine(text)
Writes text followed by a newline character.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|-----------|------|-------------|
| text | String | Content to write to the file |

#### Example
{: .no_toc }
```javascript
let f = file("test.txt");
f.writeLine("First line");
f.writeLine("Second line");
f.close();
log("Lines written successfully");
```

## appendMode()
Switches the file to append mode, allowing new content to be added to the end.

#### Example
{: .no_toc }
```javascript
let f = file("log.txt");
f.appendMode();
f.writeLine("New log entry");
f.close();
log("Content appended successfully");
```

# Examples

## Writing and Reading a File
{: .no_toc }
```javascript
let f = file("myfile.txt");
f.writeLine("one");
f.writeLine("two");
f.writeLine("three");
f.close();

f = file("myfile.txt");
let lines = f.readLines();
log("File contents: " + lines.join(", "));
f.close();
```

## Saving Entry Data as XML
{: .no_toc }
```javascript
let xml = '<record id="' + entry().field("id") + '">' +
          '<title>' + entry().field("title") + '</title>' +
          '<date>' + entry().field("date") + '</date>' +
          '</record>';

let f = file(entry().field("title") + ".xml");
f.write(xml);
f.close();
log("Entry saved as XML");
```
