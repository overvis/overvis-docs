# EXT0003: Device Journal

* *Extension dependencies:* `000F`
* *Document status:* `Testing`

This extension describes reading and clearing the journal of accumulated data (usually the history of measured values) of the device. The format of the journal file is also described.


# Journal

The journal consists of entries of the form:

```
<DATE> (<VAL> ...)
```

Field       | Length (bytes) | Description
------------|----------------|------------
`DATE`      | 4              | The representation of the creation date of the entry in seconds since 1970-01-01 00:00:00 UTC.
`VAL`       | variable       | A set of values of the tracked parameters (which, as a rule, are taken from the measured ones). The specific sequence is regulated in the device documentation.

Despite the fact that the `VAL` values, depending on the device and the firmware version, can be of different sizes in different connection sessions, their set and order never change, therefore all journal entries are the same length.


# Commands (`CMD`)


## `0005` Read Journal

Reading a block of values of the tracked parameters. The request does not indicate the amount of data to be read from the journal, the maximum number of records that fits in the response is always returned. Records are never partially returned.

### Request

```
<TID> 3900 0006 0005 <AFTERREC>
```

### Response

```
<TID> 3900 <LEN> 0005 <LASTREC> [<RECORDS>]
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`AFTERREC`  | 4              | The `LASTREC` value obtained from the previous packet. For the first request, `AFTERREC` must be 0.
`LASTREC`   | 4              | The device memory address of the last journal entry in the current packet. Cannot be 0 if the response contains the `RECORDS` field.
`RECORDS`   | `LEN - 6`      | The byte representation of the recordset.

Entries must be returned in descending order of date.

If there are no records left in the journal, it returns `LASTREC` equal to the address of the last journal entry, without the` RECORDS` field.

### Errors

Code   | Description
-------|------------
`0010` | ROM read error (data integrity is violated).
`0011` | The `AFTERREC` value failure.


## `0006` Clear Journal

Request to completely clear the contents of the journal.

The device starts an asynchronous cleaning process as it can take a long time. If the process is not in the background, then the response indicates the time the device is busy from the moment the response was sent, during which the device may not respond to application requests. At the discretion of the application, if this time is above the allowed limit, it may terminate the connection. The result of the cleaning process is returned by command `0019`.

### Request

```
<TID> 3900 0002 0006
```

### Response

```
<TID> 3900 <LEN> 0006 <PROCID> [<BUSYTIME>]
```

### Fields

Field       | Length (bytes) | Description
------------|--------------|---------
`PROCID`    | 2            | The identifier of the asynchronous process, used to get the result with the command `0019`.
`BUSYTIME`  | 4            | The maximum time, in seconds, that a process can take if it is not running in the background.

### Command errors

This command returns no additional errors.

### Result of a successful cleaning process

If successful, the process returns no data.

### Cleaning process errors

Code   | Description
-------|------------
`0010` | Error writing to ROM.
`0011` | The ROM resource has expired.
