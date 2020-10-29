# EXT0015: File System Write

* *Extension dependencies:* no
* *Document status:* `Draft`

This extension describes how to write data to the device's file system.


# Commands (`CMD`)

## `0022` Access File

Gaining access to the file for writing.

Returns a temporary file identifier. Access is automatically closed 30 seconds after using the identifier in requests or responses.

If the path does not exist in the file system with directories, the necessary directories are created. If the file does not exist, an empty file is created and opened.


### Request

```
<TID> 3900 <LEN> 0022 <FILENAME>
```

### Response

```
<TID> 3900 <LEN> 0022 <HANDLE> <FILESIZE> [<FILETIME> <ATTR>]
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`FILENAME`  | `LEN`          | File name (path to file in the file system with directories).
`HANDLE`    | 4              | Temporary file identifier.
`FILESIZE`  | 4              | File size.
`FILETIME`  | 4              | The time the file was last modified.
`ATTR`      | 2              | File attributes.

### Errors

Code   | Description
-------|------------
`0010` | Volume read error.
`0011` | Volume write error.
`0012` | There are no free identifiers. You must wait until they close.
`0013` | The file has already been accessed with a different ID. You must wait until it closes.



## `0023` Write File

Writing data to a file.

### Request

```
<TID> 3900 <LEN> 0023 <HANDLE> <OFFSET> <DATA>
```

### Response

```
<TID> 3900 0006 0023 <FILESIZE>
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`HANDLE`    | 4              | Temporary identifier of the open file.
`OFFSET`    | 4              | The position from the beginning of the file from which to write data.
`DATA`      | `LEN` - 10     | Data to write to file.
`FILESIZE`  | 4              | The current file size.

### Errors

Code   | Description
-------|------------
`0010` | Volume write error.
`0011` | Invalid (deprecated) identifier.
`0012` | Invalid value in the field `OFFSET`.
`0013` | There is not enough space on the volume.
