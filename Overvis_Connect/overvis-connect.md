# Overvis Connect Protocol

*Document status:* `approved on 2020-02-01`

Overvis Connect is a lightweight communication protocol between device and application. The underlying protocol describes connection support and sending meta information. Additional functionality, such as working with device parameters, reading the device journal, working with the device's internal clock, etc., is described in the protocol extensions. The list of extensions supported by the device is transmitted in meta-information (Handshake). Based on the list of supported extensions, the application determines the capabilities of the hardware.

The basic version of the protocol does not impose any requirements on the device's capabilities, except for the ability to exchange data with the application over any connection.

Protocol extensions describe additional commands, while command numbers cannot intersect between extensions supported by a specific device, extensions with matching command numbers conflict with each other.

Extensions can also define additional global error codes or change the behavior of the protocol (for example, impose a data encryption requirement). Thus, the exact communication protocol in a particular implementation is determined by the extensions that it supports.

### Data exchange procedure:

Overvis Connect is a client-server protocol. The device is always the server, the application is the client. The client (application) sends requests, the server (device) responds to them.

In this document and in the description of extensions, the terms "server" and "device" are used interchangeably. Likewise, the terms "client" and "application" are used interchangeably.

The connection initiator can be either the client or the server; this is not regulated by the protocol and may depend on the implementation. In any case, all further work procedure remains the same. When the server (device) initiates a connection, this connection is called "backward". Backward connectivity is required to bypass the firewalls in the scheme when multiple servers (devices) are served by one client (cloud application).

Usually, when connecting, the first request sent by the client is `0000` Handshake. This is necessary to get the type/version of the device and the set of supported extensions.

If there is no exchange for a certain period of time, the server (device) must terminate the connection. To prevent this from happening, the client must regularly send the command `0001` Keep Alive or other commands. The specific length of the waiting time for the next request is transmitted by the device in response to the command `0000` (field `KA`).

The server is obliged to respond to the request within 10 seconds (if the extension does not specify additional waiting time), otherwise the client can terminate the connection as inactive or repeat the request.

### Request and response format:

```
<TID> <PID> <LEN> <CMD> <DATA>
```

Fields are coded in order most significant first (Big Endian).

Field   | Length (bytes) | Description
--------|----------------|------------
`TID`   | 2              | Transaction number.
`PID`   | 2              | Protocol ID, always `0x3900`
`LEN`   | 2              | Package length without TID, PID and LEN.
`CMD`   | 2              | Command code.
`DATA`  | `LEN - 2`      | Data field.

The maximum request/response length supported by the protocol is 65535 bytes, but each specific server (device) implementation can set a smaller maximum length using the `FLIM` field in response to the` 0000` command. If the packet length is exceeded, the device should respond with error `0005`.

# Returning errors

In case of an error processing the request, the device returns a response:

```
<TID> 3900 0004 <ECMD> <ERROR>
```

Where `ECMD` is the command code of the request with the most significant bit set to 1 (`<CMD> + 0x8000`) and `ERROR` is the error code (2 bytes).

## Common error codes

Code    | Description
--------|------------
`0001`  | Unknown error.
`0002`  | Device I/O error.
`0003`  | The device is busy (e.g. by a background process, try again later).
`0004`  | Invalid LEN field value.
`0005`  | Unknown command.
`0006`  | Invalid value in the DATA field or the data in the DATA field could not be processed.
`0007`  | No access rights.

Also, for each additional command in each extension, additional specific errors are possible, described in the extension documentation. The most specific error is always preferred.


# Commands (`CMD`)


## `0000` Handshake

Request for meta-information about the server (device) and its capabilities.

### Request

```
<TID> 3900 0002 0000
```

### Response

```
<TID> 3900 <LEN> 0000 <IC> <HW> <FW> <FLIM> <KA> [<EXT> ...]
```

### Fields

Field    | Length (bytes) | Description
---------|----------------|---------
`IC`     | 2              | Device type category identifier (practically - manufacturer's identifier).
`HW`     | 2              | Hardware type of the device. The least significant digit in decimal is the AO version of this type.
`FW`     | 4              | Firmware version of the device. (the most significant 2 bytes are the version type).
`FLIM`   | 2              | Maximum packet length in bytes (including fields `TID`,` PID`, `LEN`,` CMD`).
`KA`     | 2              | Time in seconds after which the device will terminate the connection if there is no exchange.
`EXT`    | 2              | Supported protocol extension codes, 0 or more fields.

### Errors

This request returns no additional errors.


## `0001` Keep Alive

The client should regularly send commands if necessary to keep the connection. This short packet is used if the client has no requests for device.

Request:

```
<TID> 3900 0002 0001
```

Response:

```
<TID> 3900 0002 0001
```

### Errors

This request returns no additional errors.
