# EXT000A: Device Reboot On Disconnect

* *Extension dependencies:* no
* *Document status:* `Approved on 2020-02-01`

This extension contains a command to reboot the device.


# Commands (`CMD`)


## `0014` Reboot Device on Disconnect

Reboot request at the end of the communication session with the application. Does not cause an immediate reboot, but only changes the connection state. If disconnected (or connection is lost), the device should restart.

### Request

```
<TID> 3900 0002 0014
```

### Response

```
<TID> 3900 0002 0014
```

### Errors

This request returns no additional errors.
