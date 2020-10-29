# EXT0013: File System Read

* *Extension dependencies:* no
* *Document status:* `Draft`

This extension describes how to read data from the device's file system.


# Commands (`CMD`)

## `001E` Access File

Gaining read access to the file.

Returns a temporary file identifier. Access is automatically closed 30 seconds after using the identifier in requests or responses.

### Request

```
<TID> 3900 <LEN> 001E <FILENAME>
```

### Response

```
<TID> 3900 <LEN> 001E <HANDLE> <FILESIZE> [<FILETIME> <ATTR>]
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`FILENAME`  | `LEN`          | File name (path to file in the file system with directories).
`HANDLE`    | 4              | Temporary identifier of the open file.
`FILESIZE`  | 4              | File size.
`FILETIME`  | 4              | The time the file was last modified.
`ATTR`      | 2              | File attributes.

### Errors

Code   | Description
-------|---------
`0010` | Volume read error.
`0011` | The file was not found (at the specified path - in the file system with directories).
`0012` | There are no free identifiers. You must wait until they close.
`0013` | The file has already been accessed with a different ID. You must wait until it closes.



## `001F` Read File

Reading data from a file.

### Request

```
<TID> 3900 000C 001F <HANDLE> <OFFSET> <BLOCKLEN>
```

### Response

```
<TID> 3900 <LEN> 001F <BLOCKDATA>
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|---------
`HANDLE`    | 4              | Temporary file identifier.
`OFFSET`    | 4              | The position of the requested data block from the beginning of the file.
`BLOCKLEN`  | 2              | The length of the requested data block.
`BLOCKDATA` | `BLOCKLEN`     | File data.

### Errors

Code   | Description
-------|------------
`0010` | Volume read error.
`0011` | Invalid (deprecated) identifier.
`0012` | Reading out of bounds (wrong `OFFSET` / `BLOCKLEN`).
