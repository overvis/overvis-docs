# EXT0011: EdDSA Authentication

* *Extension dependencies:* no
* *Document status:* `Discussion`

This extension describes a two-way process for authenticating a device and a server using the EdDSA algorithm and two public/private key pairs.

Specific algorithm used: Ed25519, public key length - 32 bytes, signature length - 64 bytes.

EdDSA algorithm:
* https://en.wikipedia.org/wiki/EdDSA
* https://tools.ietf.org/html/rfc8032
* https://ed25519.cr.yp.to/ed25519-20110926.pdf

Both public keys are known to both parties (device and server) and are not secret information. Secret keys are known only to their owners and are never transferred during data exchange. The public key of the device also serves to uniquely identify the device, that is, each device must have its own, set during the production of a pair of public and private keys.

Both key pairs can be changed at the request of the server (after passing the authentication) in case the old keys have been compromised.

## Description of the authentication process

For each connection of the device to the server, the server is considered unauthenticated and receives the minimum access rights (minimum - requests `0000`,` 001A`, `001B` and` Keep Alive`, any additional requests - at the discretion of the device). For its part, the server does not have the ability to identify the device and determine its owner until the new connection is authenticated.

The server will initiate authentication at any time upon request `001A`. In this request, the server sends its public key and a random sequence of `DCHALLENGE` bytes. The device checks that the public key of the server matches the expected one (otherwise - an authentication error) and sends back its public key and a new random sequence of bytes `SCHALLENGE`. After that, the device is taken for signing the message `DCHALLENGE` and waits for the request` 001B` (not less than 1 minute).

In the `001A` request, the server sends the signature of the` SCHALLENGE` sequence with its secret key and expects to receive the signature of the `DCHALLENGE` sequence with the device secret key in the response. A correct response to this message confirms that the device has verified the validity of the server signature (i.e., the response may take some time). According to the received public key of the device, the server (after confirming the signature of the device) uniquely identifies it (determines the owner, linked account, etc.).

If the server detects an authentication error (unknown public key of the device or incorrect signature), authentication and identification of the device are considered unsuccessful and further actions are left at the discretion of the server (for example, immediately disconnect the connection or continue to poll the device, marking it as unknown).

If the device detects an authentication error (incorrect public key of the server or incorrect signature), it should return an error to the server, report the error to the user when working with the server, and leave the minimum access rights to the unauthenticated server. It is impossible to disconnect the connection in case of an authentication error - this can be important if the server has several public keys and must choose the one needed for a specific device.

At the discretion of the hardware developers - on the device side, a limit on the number of authentication requests in a certain period of time can be added (to protect against brute force).


# Commands (`CMD`)


## `001A` Start Device-Server EdDSA Authentication

Request to initialize the authentication.

### Request

```
<TID> 3900 0042 001A <SPUBKEY> <DCHALLENGE>
```

### Response

```
<TID> 3900 0042 001A <DPUBKEY> <SCHALLENGE>
```

### Fields

Field        | Length (bytes) | Description
-------------|----------------|------------
`SPUBKEY`    | 32             | Server public key.
`DPUBKEY`    | 32             | Public key of the device.
`SCHALLENGE` | 32             | A random sequence of bytes generated by the device for signing by the server.
`DCHALLENGE` | 32             | A random sequence of bytes generated by the server for signing by the device.

### Errors

Code   | Description
-------|------------
`000A` | Mismatch of the public key of the server (`SPUBKEY`) with the known device (server authentication error).
`000B` | Too many authentication requests, try again later.


## `001B` Confirm Device-Server EdDSA Authentication

Request for confirmation of authentication. It is assumed that the request `001A` was sent before this request was sent.

### Request

```
<TID> 3900 0042 001B <SSIGN>
```

### Response

```
<TID> 3900 0042 001B <DSIGN>
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`SSIGN`     | 64             | Signature of the message `<CHALLENGE>` with the server's secret key.
`DSIGN`     | 64             | Signature of the message `<DCHALLENGE>` with the secret key of the device.

### Errors

Code   | Description
-------|------------
`000A` | Server signature verification failed (`SSIGN`) (server authentication error).
`000B` | Authentication not initialized or timed out after initialization. Repeat initialization.
`000C` | The device is busy signing a message. Retry the request later.


## `001C` Create New EdDSA Secret

A request to change the public key of the server known to the device and to regenerate a pair of public and private keys belonging to the device.

This request can be accepted by the device only after successful server authentication (after request `001B`).

A successful response from the device confirms the generation and saving of a new pair of device keys, i.e., the response may take a long time. After answering, the authentication session remains in effect and the connection is not terminated. If there is an error in sending a response to the server (for example, if the connection is broken), the device must return its old key pair to the permanent memory.

### Request

```
<TID> 3900 0022 001C <SPUBKEY>
```

### Response

```
<TID> 3900 0022 001C <DPUBKEY>
```

### Fields

Field        | Length (bytes) | Description
-------------|----------------|------------
`SPUBKEY`    | 32             | New public key of the server.
`DPUBKEY`    | 32             | New public key of the device.

### Errors

Code   | Description
-------|------------
`000A` | Key generation error.