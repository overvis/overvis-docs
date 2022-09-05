# EXT0017: File System Service

* *Extension dependencies:* `000F`, `0015`
* *Document status:* `Draft`

This extension describes asynchronous service operations for the device file system.

The commands use 32-bit file system element identifiers, which can be opened by other extensions for the file system, except for the extension `0015` (for example, but not necessarily,`00013`, `0014`).


# Commands (`CMD`)

## `0024` Rename/Move File System Element

Rename or move a file system item.

## `0025` Remove/Delete File System Element

Removing a file system item.

## `0026` Close File System Element Access

Closing access to a file system element by identifier.

## `0027` Copy File System Element

Copying a file system item.

## `0028` Initialize / Format File System

Initialization and preparation (formatting) of a new file system.
