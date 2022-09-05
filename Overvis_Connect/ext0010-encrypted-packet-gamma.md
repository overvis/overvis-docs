# EXT0010: Encrypted Packet Gamma

* *Extension dependencies:* no
* *Document status:* `Approved on 1.02.2020`

This extension describes the process of transmitting encoded data, with full encryption of requests, responses and errors. The exchange of encryption keys is not considered and should be done through other extensions.

Algorithm: Gamma with a single key buffer for requests and responses.

A 40-byte key is used to encrypt data. Data is encrypted in blocks of 16 bytes. Before the block is encrypted, the hash of the key is calculated using the SHA-1 algorithm (the first 16 bytes of the hash are used to encrypt the block). After processing each block, a new key is calculated: 20 bytes of the hash of the previous key, 16 bytes of the data block before it is encrypted, and 4 bytes of the next block number.

Encryption of a block of 16 bytes takes place in 2 stages:
1) Permutation. In a cycle from the zero to the 15th byte of the key hash, each byte of the hash is divided into 2 tetrads - byte indices with rearranged tetrads. The senior tetrad of the data block (with the index taken from the senior tetrad of the hash byte) is swapped with the senior tetrad of the next data byte (with the index equal to the hash byte index). Likewise for the younger tetrad. Total: 16 cycles of 2 permutations of tetrads.
2) Coding. Bitwise exclusive "OR" of the key hash with the result of the previous step.

The key for block 0 is initialized using two shared secret keys: key #1 of arbitrary length and key #2 of 16 bytes: 20 bytes of the hash of key #1, 16 bytes of key #2, 4 bytes of the block number (zeros).

The data is decrypted in reverse order:
1) Exclusive "OR" operation of a key hash with a data block.
2) Permutation (in a cycle from the 15th byte of the key hash to zero, similar to the first stage of encryption).

# Commands (`CMD`)


## `0018` Send Encrypted Request

This command wraps encrypted data exchange. A device that supports this extension returns the error `0006` (no rights) when accepting any command that is not wrapped with the command` 0018`, except for a limited set of commands - Handshake, KeepAlive and commands of supported extensions to start encryption, for example, `EXT0009` for obtaining a shared secret key (s).

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
-----------|----------------|------------
`PAYLOAD`  | `LEN - 2`      | Encrypted content (see below).

### `PAYLOAD`

Encrypted request/response content in the following format:

```
<LEN> <CMD> <DATA> <CHKSUM> <PADDING>
```

Field     | Length (bytes)           | Description
----------|--------------------------|------------
`WRAPLEN` | 2                        | Length `CMD + DATA`.
`CMD`     | 2                        | Command code (from the extensions supported by the device, except for command `0018`).
`DATA`    | `WRAPLEN - 2`            | Data field.
`CHKSUM`  | 2                        | Sum of bytes of fields `CMD + DATA`.
`PADDING` | variable, multiple of 16 | A set of random bytes for finishing up to the minimum `PAYLOAD` length equal to 128 (or for finishing up to a multiple of 16 with a length over 128). Add-ons can also be used to a randomly selected length multiple of 16 and less than the maximum packet length for the device.

### Errors

The device must respond to any error in decrypting the content of the request (unencrypted):

```
<TID> 3900 0004 8018 0010
```

All other errors related to the processing of encrypted content must be encrypted, that is, they must be transmitted inside the `PAYLOAD` field in the following form:

```
<WRAPLEN> <ECMD> <ERROR> <CHKSUM> <PADDING>
```

Where `ECMD` is the command code of the request with the most significant bit set to 1 (`<CMD> + 0x8000`) and `ERROR` is the error code (2 bytes).

If the client cannot decrypt the response of the server (device), it must terminate the connection. This extension does not provide the ability to stop an encrypted session.
