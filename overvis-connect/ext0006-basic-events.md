# EXT0006: Basic Events

* *Extension dependencies:* `000F`
* *Document status:* `Testing`

This extension describes the reading and management of a set of simple events (scheduled in time, or reactions to changes in the measured parameters of the device), and also describes the data format for describing these events.

# Events

Events consist of records of the form:

```
<DATE> (<PARAM> ...)
```

Field       | Length (bytes) | Description
------------|----------------|------------
`DATE`      | 4              | The date representation of the event in seconds since 1970-01-01 00:00:00 UTC.
`PARAM`     | regulated      | A set of parameter sequences. The specific sequence is regulated in the device documentation.

Despite the fact that the values of `PARAM` can be of different sizes, their set and order never change, therefore all records in the table are of the same length.


# Commands (`CMD`)


## `000D` Read Events

Reading an event block.

The request does not indicate the amount of data read from the device, the maximum number of events that fits in the response is always returned. Events are never partially returned.

### Request

```
<TID> 3900 0006 000D <AFTEREV>
```

### Response

```
<TID> 3900 <LEN> 000D <LASTEV> [<EVENT> ...]
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`AFTEREV`   | 4              | The `LASTEV` from the previous packet. For the first request, `AFTEREV `must be 0.
`LASTEV`    | 4              | The device memory address of the last table entry in the current packet.
`EVENT`     | regulated      | The byte representation of the event.

Events can be returned in any order - it is necessary to provide sorting after all events are received.

If there are no records left in the device, it returns `LASTEV` equal to `AFTEREV`, without `EVENT` fields.

### Errors

Code   | Description
-------|------------
`0010` | Read error from ROM (data integrity is violated).
`0011` | The `AFTEREV` value failure.


## `000E` Write Events

Recording one or more events to the device.

### Request

```
<TID> 3900 <LEN> 000E (<EVENT> ...)
```

### Response

```
<TID> 3900 0002 000E
```

### Fields

Field    | Legth (bytes) | Description
---------|---------------|------------
`EVENT`  | regulated     | The byte representation of the event.

Events that are identical to those that already exist in the ROM are ignored when written.

### Errors

Code   | Description
-------|------------
`0010` | Error writing to ROM.
`0011` | The ROM resource has expired.
`0012` | No ROM space.
`0013` | Error in field `EVENT`.


## `000F` Delete Events

Remove multiple specified events from the device.

### Request

```
<TID> 3900 <LEN> 000F (<EVENT> ...)
```

### Response

```
<TID> 3900 0002 000F
```

### Fields

Field       | Length (байт) | Description
------------|---------------|------------
`EVENT`     | regulated     | The byte representation of the event.

Events missing in the device are ignored.

### Errors

Code   | Description
-------|------------
`0010` | Error writing to ROM.
`0011` | The ROM resource has expired.
`0012` | Error in field `EVENT`.


## `0010` Clear Events

Delete all events from the device.

The device starts an asynchronous deletion process because it can take a long time. If the process is not in the background, then the response indicates the time the device is busy from the moment the response was sent, during which the device may not respond to application requests. At the discretion of the application, if this time is above the allowed limit, it may terminate the connection. The result of the deletion process is returned by command `0019`.

### Request

```
<TID> 3900 0002 000A
```

### Response

```
<TID> 3900 <LEN> 000A <PROCID> [<BUSYTIME>]
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`PROCID`    | 2              | The identifier of the asynchronous process, used to get the result with the command `0019`.
`BUSYTIME`  | 4              | The maximum time, in seconds, that a process can take if it is not running in the background.

### Command errors

This command returns no additional errors.

### Result of a successful removal process

If successful, the process returns no data.

### Removal process errors

Code   | Description
-------|------------
`0010` | Error writing to ROM.
`0011` | The ROM resource has expired.
