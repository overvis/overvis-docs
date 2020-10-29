# EXT0004: Device Clock

* *Extension dependencies:* no
* *Document status:* `Testing`

This extension describes how to read and change the time on the device's internal clock.


# Commands (`CMD`)


## `0007` Get Time

Reading the current time value from the device in UTC standard.

### Request

```
<TID> 3900 0002 0007
```

### Response

```
<TID> 3900 0006 0007 <DTIME>
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`DTIME`     | 4              | The current time on the device's clock, in seconds, since 1970-01-01 00:00:00 UTC.

### Errors

Code   | Description
-------|------------
`0010` | Time reading error.


## `0008` Set Time

Setting a new time on the device.

### Request

```
<TID> 3900 0006 0008 <STIME>
```

### Response

```
<TID> 3900 0006 0008 <DTIME>
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`STIME`     | 4              | The time to be set on the device clock, in seconds, since 1970-01-01 00:00:00 UTC.
`DTIME`     | 4              | The current time on the device's clock, in seconds, since 1970-01-01 00:00:00 UTC. Must be equal to `STIME`.

### Errors

Code   | Description
-------|------------
`0010` | Time recording error.
