# EXT000E: Encrypted Packet AES 256 CBC

* *Extension dependencies:* no
* *Document status:* `Testing`

This extension describes the process of transmitting encoded data, with full encryption of requests, responses and errors. The exchange of encryption keys is not considered and should be done through other extensions.

Algorithm: AES 256 CBC with a single key buffer for requests and responses.


# Commands (`CMD`)


## `0018` Send Encrypted Request

This command wraps encrypted data exchange. A device that supports this extension returns the error `0006` (no rights) when accepting any command that is not wrapped by the` 0018` command, except for a limited set of commands - Handshake, KeepAlive and commands of supported extensions to run the required encryption algorithm, for example, `EXT0009 `to get the shared secret key.

### Request

```
<TID> 3900 <LEN> 0018 <PAYLOAD>
```

### Response

```
<TID> 3900 <LEN> 0018 <PAYLOAD>
```

### Fields

Field      | Length (bytes) | Description
-----------|----------------|---------
`PAYLOAD`  | `LEN - 2`      | Encrypted content (see below).

### `PAYLOAD`

Encrypted request/response content in the following format:

```
<LEN> <CMD> <DATA> <CHKSUM> <PADDING>
```

Field     | Length (bytes)           | Description
----------|--------------------------|------------
`LEN`     | 2                        | Length `CMD + DATA`.
`CMD`     | 2                        | Command code (from the extensions supported by the device, except for `0001` Keep Alive and the` 0018` command itself).
`DATA`    | `LEN - 2`                | Data field.
`CHKSUM`  | 2                        | Sum of bytes of fields `CMD + DATA`.
`PADDING` | variable, multiple of 16 | A set of random bytes for finishing up to the minimum `PAYLOAD` length equal to 128 (or for finishing up to a multiple of 16). Add-ons can also be used to a randomly selected length multiple of 16 and less than the maximum packet length for the device.

### Errors

The device should respond to any error in decrypting the content of the request (in unencrypted form):

```
<TID> 3900 0004 8018 0010
```

All other errors must be encrypted, that is, they must be transmitted inside the `PAYLOAD` field in the following form:

```
<LEN> <ECMD> <ERROR> <CHKSUM> <PADDING>
```

Where `ECMD` is the command code of the request with the most significant bit set to 1 (`<CMD> + 0x8000`) and `ERROR` is the error code (2 bytes).

If the client cannot decrypt the response of the server (device), it must terminate the connection. This extension does not provide the ability to stop an encrypted session.
