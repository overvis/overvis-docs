# EXT000B: Next Reconnect

* *Extension dependencies:* no
* *Document status:* `Testing`

This extension is only used for backward connection. It contains a command for setting the delay before the next connection from the device and specifying the address to which the next connection should be established.


# Commands (`CMD`)


## `0015` Set Next Reconnect

Request to set the parameters of the next backward connection. The device stores these parameters in RAM, and returns the values from the settings (or default values) when one of the following conditions is met:
* on device restart,
* on successful connection,
* by exceeding the number of connection attempts (at the discretion of the device).

### Request

```
<TID> 3900 <LEN> 0015 <DELAY> [ <PORT> [ <HOST> ]]
```

### Response

```
<TID> 3900 0002 0015
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`DELAY`     | 4              | Delay until next connection in ms.
`PORT`      | 2              | Server port for connecting a device to it.
`HOST`      | `LEN-8`        | Domain name or IP address as ASCII string.

### Errors

Code   | Description
-------|------------
`0010` | Invalid value `DELAY`.
