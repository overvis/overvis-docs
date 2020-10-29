# EXT0016: File System Manage

* *Extension dependencies:* `0015`
* *Document status:* `Draft`

This extension describes how to manage the file system of a device.

The commands use 32-bit file system element identifiers, which can be opened by other extensions for the file system, except for the extension `0015` (for example, but not necessarily,`00013`, `0014`).


# Commands (`CMD`)

## `0024` Rename/Move File System Element

Rename or move a file system item.

Executed only to change item data. For commands requiring time-consuming data movement (moving between volumes), error `0012` is returned.

### Request

```
<TID> 3900 <LEN> 0024 <HANDLE> <PATH>
```

### Response

```
<TID> 3900 0002 0024
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`HANDLE`    | 4              | Temporary item identifier.
`PATH`      | `LEN - 6`      | The path to the file system item.

### Errors

Code   | Description
-------|------------
`0010` | Volume write error.
`0011` | Invalid (deprecated) identifier.
`0012` | The operation cannot be performed synchronously.
`0013` | The item already exists at the specified path.



## `0025` Remove/Delete File System Element

Removing a file system item.

After executing the command, access by identifier is closed. Executed only to change item data. Time-consuming commands (deleting content directories) return error `0012`.

### Request

```
<TID> 3900 0006 0025 <HANDLE>
```

### Response

```
<TID> 3900 0002 0025
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`HANDLE`    | 4              | Temporary item identifier.

### Errors

Code   | Description
-------|------------
`0010` | Volume write error.
`0011` | Invalid (deprecated) identifier.
`0012` | The operation cannot be performed synchronously.



## `0026` Close File System Element Access

Closing access to a file system element by identifier.

Used to free up space in the table of access identifiers to the elements of the file system or to free an element for other operations with it. The command is not required to synchronize the file system and cache.

### Request

```
<TID> 3900 0006 0026 <HANDLE>
```

### Response

```
<TID> 3900 0002 0026
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`HANDLE`    | 4              | Temporary item identifier.

### Errors

Code   | Description
-------|------------
`0010` | Invalid (deprecated) identifier.
