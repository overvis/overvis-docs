# EXT0002: Device State

* *Extension dependencies:* no
* *Document status:* `Approved on 2020-02-01`

This extension describes reading the current values of the device status parameters.


# Commands (`CMD`)


## `0004` Read State

Reading a block of device state parameter values.

### Request

```
<TID> 3900 <LEN> 0004 [( <OFFSET> <BLOCKLEN> ) ...]
```

If `OFFSET` and `BLOCKLEN` are not specified, the device outputs the maximum amount of data without offset in one block. That is, if all the values of the device state parameters fit into one response, this is a command to read all values at once.

Several `<OFFSET> <BLOCKLEN>` pairs in a row can be specified in the request, respectively, the resulting response will consist of the same number of `<BLOCKDATA>` blocks.

### Response

```
<TID> 3900 <LEN> 0004 (<BLOCKDATA>) ...
```

### Fields

Field       | Length (bytes) | Description
------------|----------------|------------
`OFFSET`    | 2              | Offset in the parameter structure.
`BLOCKLEN`  | 2              | The length of the block with parameters.
`BLOCKDATA` | `BLOCKLEN`     | Byte representation of the parameter block.

### Errors

Code   | Description
-------|------------
`0010` | Reading out of bounds (wrong `OFFSET`/`BLOCKLEN`).
`0011` | The answer does not fit in the packet length (overflow).
