# EXT0001: Device Parameters

* *Extension dependencies:* no
* *Document status:* `Approved on 2020-02-01`

This extension describes the reading and setting of parameters (setpoints/settings) of the device. Parameters are stored in non-volatile memory. To optimize work with a large number of records and eliminate intermediate states, the extension supports transactional work - changes are first saved in RAM, then, when the transaction is completed, all accumulated changes are written to permanent memory.


# Commands (`CMD`)


## `0002` Read Parameters

Reading a block of parameters (settings) from non-volatile memory.

### Request

```
<TID> 3900 <LEN> 0002 <SOURCE> [( <OFFSET> <BLOCKLEN> ) ...]
```

If `OFFSET` and `BLOCKLEN` are not specified, the device outputs the maximum amount of data without offset in one block. That is, if all the parameters of the device fit in one response, this is a command to read all parameters at once.

Several `<OFFSET> <BLOCKLEN>` pairs in a row can be specified in the request, respectively, the resulting response will consist of the same number of `<BLOCKDATA>` blocks.

### Response

```
<TID> 3900 <LEN> 0002 (<BLOCKDATA>) ...
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`SOURCE`    | 1              | Data source.
`OFFSET`    | 2              | Offset in the parameter structure.
`BLOCKLEN`  | 2              | The length of the block with parameters.
`BLOCKDATA` | `BLOCKLEN`     | Byte representation of the parameter block.

### Errors

Code   | Description
-------|------------
`0010` | Incorrect `SOURCE`.
`0011` | Reading out of bounds (wrong `OFFSET`/`BLOCKLEN`).
`0012` | The answer does not fit in the packet length (overflow).
`0013` | ROM read error (data integrity is violated).


## `0003` Write Parameters

Writing a block of parameters (settings) and managing the transaction of moving changes to non-volatile memory.

Correct device response (without error) confirms acceptance of changes and/or writing to ROM (if `TRCON = 2`).

When a transaction is initialized, the device allocates space in the RAM for the entire structure of parameters, then copies it from permanent memory. In the future, the device adds changes to the structure in the RAM, sent by the command `0003`. The device must keep this structure in memory for at least 10 seconds from the moment of the previous command. After receiving the command to complete the transaction (`TRCON = 2`), the device writes the changes to the permanent memory and frees up the space occupied by the structure in RAM.

### Request

```
<TID> 3900 <LEN> 0003 <SOURCE> <TRCON> [( <OFFSET> <BLOCKLEN> <BLOCKDATA> ) ...]
```

### Response

```
<TID> 3900 0002 0003
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|---------
`SOURCE`    | 1              | Data source.
`TRCON`     | 1              | Transaction control byte. 0 - start of a new transaction (clearing accumulated changes), 1 - continuation of the previous transaction, 2 - end of the transaction (recording accumulated changes in ROM).
`OFFSET`    | 2              | Offset in the parameter structure.
`BLOCKLEN`  | 2              | The length of the block with parameters.
`BLOCKDATA` | `BLOCKLEN`     | Byte representation of the parameter block.

### Errors

Code   | Description
-------|------------
`0010` | Incorrect `SOURCE`.
`0011` | Recording out of bounds (incorrect `OFFSET` / `BLOCKLEN`).
`0012` | Error writing to ROM.
`0013` | The ROM resource has expired.
`0014` | RAM error (unable to allocate memory for a transaction).
`0015` | Transaction aborted by initializing a transaction from another connection.
`0016` | Transaction support timed out.
`0017` | An attempt to continue a completed transaction.
