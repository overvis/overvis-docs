# EXT000D: Wrapped Packet

* *Extension dependencies:* no
* *Document status:* `Approved on 2020-02-01`

This extension allows you to wrap a packet with any data in the Overvis Connect protocol packet.

Wrapped packets in length can exceed the Overvis Connect packet length limit set for this session, and they are split and transmitted by several commands in a row (see below). In fact, it allows you to tunnel any protocol (HTTP, FTP, etc.).


# Commands (`CMD`)


## `0017` Exchange Wrapped Packets

This command serves as a wrapper for exchanging binary data in any format.

If the request does not fit into one packet, then it is sent by several commands in a row, while the device responds only with the `OFFSET` field, without data, to all commands except the last one. If the device cannot process a request of the specified length, it will return an error in response to any of the commands that send a long request.

If the response does not fit into one packet, then it is required to send several commands with the `OFFSET` field, without data, to the device to receive all parts of the response.

### Request

```
<TID> 3900 <LEN> 0017 <OFFSET> [ <REMLEN> <BLOCKDATA> ]
```

#### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`OFFSET`    | 4              | Block offset in the request, if there are `REMLEN` and` BLOCKDATA` fields. Otherwise, match the value of `OFFSET` + `BLOCKDATA` of the previous received response to get the next part.
`REMLEN`    | 4              | Remaining request length in bytes. The total length of the request is equal to the sum of the length of `BLOCKDATA` and the fields `OFFSET` and `REMLEN`. If `REMLEN` is 0, then this is the last part of the request.
`BLOCKDATA` | `LEN - 10`     | Request data block.

### Response

```
<TID> 3900 <LEN> 0017 <OFFSET> [ <ESTREM> <BLOCKDATA> ]
```

#### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`OFFSET`    | 4              | Block offset in response, if there is a `BLOCKDATA` field. Otherwise, it is the same as the value `OFFSET` + `BLOCKDATA` of the request, which confirms the receipt of a part of the request and expectation of the next part.
`ESTREM`    | 4              | An estimate of the remaining length of the response in bytes. The estimate of the total length of the response is equal to the sum of the length of `BLOCKDATA` plus the fields `OFFSET` and `ESTREM`. If `ESTREM` is 0, then this is the last part of the answer.
`BLOCKDATA` | `LEN - 10`     | Response data block.

### Errors

Code      | Description
----------|-------------
`0010`    | The total length of the request is too long.
`0011`    | Invalid `OFFSET` value.
