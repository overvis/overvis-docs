# EXT0008: Get Device ID

* *Extension dependencies:* no
* *Document status:* `Approved on 2020-02-01`

This extension describes the receipt by the application of the device's self-identification information, a unique identifier by which this device instance is identified. The device must have a unique 64-bit number (UID).


# Commands (`CMD`)


## `0012` Get Device ID

Request to read identification information, a unique 64-bit number, programmed into the device. This can be a MAC address (with pre-added zeros at the beginning) or a random number generated during the manufacture of the device. Requirements: This number must be unique among all manufactured devices that support the protocol and must not change. The unpredictability of the value is optional.

### Request

```
<TID> 3900 0002 0012
```

### Response

```
<TID> 3900 000A 0012 <UID>
```

### Fields

Field   | Length (bytes) | Description
--------|----------------|---------
`UID`   | 8              | An immutable public unique identifier for the device.

### Errors

Code   | Description
-------|------------
`0010` | Error reading unique ID.
