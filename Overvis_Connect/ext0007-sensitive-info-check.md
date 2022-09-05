# EXT0007: Sensitive Information Check

* *Extension dependencies:* no
* *Document status:* `Approved on 2020-02-01`

This extension adds the ability to validate a value known to the server (device) for the client (application). For example, a password.

In response, the server returns a boolean value - either the value transmitted by the client is valid or not. In this case, no state in the device memory should change as a result of this check. That is, using this extension, it is impossible to implement, for example, the issuance of additional access rights on the device for the client when the password verification is successful.

Information validation functionality is needed in a schema when the application and the device completely trust each other, but the application does not trust the user. For example, in such a scheme, the user needs to prove the fact of physical possession of the device. To implement this, the device generates a code for the user, which must be entered into the application. The application can validate this code (using the command described below) and grant the user additional rights.

Based on the described scheme, and due to the requirement of the immutability of the state of the device memory, in this extension on the device side there is no need to protect against enumeration of values (by the time of the codes, by the number of attempts, etc.).

# Commands (`CMD`)


## `0011` Confirm Value Correctness

Checking the value for plausibility.

### Request

```
<TID> 3900 <LEN> 0011 <KIND> <VALUE>
```

### Response

```
<TID> 3900 0003 0011 <RESULT>
```

### Fields

Field       | Length (bytes)   | Description
------------|------------------|------------
`KIND`      | 2                | The type of value to check. The list of all possible types is implementation-regulated. The type of value determines the constraints of the `VALUE` field.
`VALUE`     | `LEN - 4`        | The value to check. The result is the same for both invalid values and values in the wrong format (wrong length).
`RESULT`    | 1                | 1 - value is correct, 0 - value is incorrect.

### Errors

Code   | Description
-------|------------
`0010` | Invalid field `KIND`
