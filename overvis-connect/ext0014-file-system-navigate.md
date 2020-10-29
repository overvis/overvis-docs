# EXT0014: File System Navigate

* *Extension dependencies:* no
* *Document status:* `Discussion`

This extension describes navigation in the device's file system.


# Commands (`CMD`)

## `0020` Access Directory

Gaining access to the directory.

### Request

```
<TID> 3900 <LEN> 0020 <PATH>
```

### Response

```
<TID> 3900 0006 0020 <HANDLE>
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`PATH`      | `LEN`          | Directory path.
`HANDLE`    | 4              | Temporary directory identifier.

### Errors

Code   | Description
-------|------------
`0010` | Volume read error.
`0011` | Wrong path.
`0012` | There are no free identifiers. You must wait until they close.



## `0021` Read Directory Contents

Getting data about catalog items.

### Request

```
<TID> 3900 0006 0021 <HANDLE>
```

### Response

```
<TID> 3900 <LEN> 0021 [(<FILESIZE> <FILETIME> <ATTR> <NAMELEN> <NAME>) ...]
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`HANDLE`    | 4              | Temporary directory identifier.
`FILESIZE`  | 4              | File size (0 for other directory entries).
`FILETIME`  | 4              | The time the item was last modified.
`ATTR`      | 2              | Item attributes.
`NAMELEN`   | 2              | The length of the item name.
`NAME`      | `NAMELEN`      | Item name.

### Errors

Code   | Description
-------|------------
`0010` | Volume read error.
`0011` | Invalid (deprecated) identifier.
`0012` | There are no free identifiers. It is necessary to close the old ones or wait for them to close.
