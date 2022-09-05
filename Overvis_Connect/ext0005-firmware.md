# EXT0005: Firmware Update

* *Extension dependencies:* `000F`
* *Document status:* `Approved on 2020-02-01`

This extension describes the process of updating device firmware.


# Commands (`CMD`)


## `0009` Get FW File Version

Reading the firmware file version on the device.

### Request

```
<TID> 3900 0002 0009
```

### Response

```
<TID> 3900 0006 0009 <FWVER>
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`FWVER`     | 4              | Version of the firmware file (senior 2 bytes - version type). This is the version of the file on the device that is ready for firmware update, and not currently running.

### Errors

Code   | Description
-------|------------
`0010` | Read error from ROM.
`0011` | The firmware file was not found (not written / has the wrong format).


## `000A` Delete FW File

Deleting the firmware file on the device.

This command should be executed before writing the file, otherwise the device may return the error `0012` during writing, even if a warning was returned that the file was not found (for example, if the data in the file location was written in the wrong format).

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
`0010` | ROM error.
`0011` | The ROM resource has expired.


## `000B` Send FW File

Writing the firmware file to the device.

### Request

```
<TID> 3900 <LEN> 000B <OFFSET> <TOTLEN> <BLOCKDATA>
```

### Response

```
<TID> 3900 0002 000B
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`OFFSET`    | 4              | Byte offset in the firmware file.
`TOTLEN`    | 4              | Full length of the firmware file in bytes.
`BLOCKDATA` | `LEN - 10`     | Data block from the firmware file.

### Errors

Code   | Description
-------|---------
`0010` | Recording out of bounds (wrong `TOTLEN`).
`0011` | Mismatch of `OFFSET` and `BLOCKDATA` length specified by` TOTLEN`.
`0012` | ROM write error, or there is already data in ROM.


## `000C` Start FW Update Process

Starting the firmware update process.

After receiving a response, the application terminates the connection.

### Request

```
<TID> 3900 0002 000C
```

### Response

```
<TID> 3900 0002 000C
```

### Errors

Code   | Description
-------|------------
`0010` | Read error from ROM.
`0011` | The firmware file was not found.
`0012` | Firmware file CRC error.
