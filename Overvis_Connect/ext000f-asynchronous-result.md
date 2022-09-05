# EXT000F: Asynchronous Process Result

* *Extension dependencies:* no
* *Document status:* `Approved on 2020-02-01`

This extension describes how the client receives the result of an asynchronous process. Such processes can be started by the command during its execution, before the command response returns. In this case, the process identifier must be specified in the command response (the value of the process identifier may not depend on the type of process or the command that called it, and is used in the command to obtain the result).

In the description of each command that starts asynchronous processes, for each process, the result of its execution (if it successfully completes) and a list of process errors (returned if its execution fails) are described. The execution mode (background, blocking, optional, blocking time) is also described for the command creating the process.

The result of the process is independent of the presence of a connection and remains available when reconnecting.


# Commands (`CMD`)


## `0019` Get Asynchronous Process Result

Used to get the result of an asynchronous process.

### Request

```
<TID> 3900 0004 0024 <PROCID>
```

### Response

```
<TID> 3900 LEN 0024 <PERROR> [<EXTRA>]
```

### Fields

Field    | Length (bytes) | Description
---------|----------------|------------
`PROCID` | 2              | Process identifier.
`PERROR` | 2              | Process execution error code, or 0 if the process completed successfully. Similar to the command error code, basic errors or errors described for the process in the description of the creating command are used.
`EXTRA`  | `LEN - 4`      | Optional additional information about the result, described for the process in the description of the creating command.

### Errors

Code   | Description
-------|------------
`0010` | Invalid `PROCID` (the process is not running, there was no such process, or the result was removed from the cache).
`0011` | The result is not yet available (the process is running).
