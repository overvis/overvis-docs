# Overvis API v1.0 :: Raw Readings API

## <a name='Raw-read-multiple-register-group'></a> `POST /raw/read-multiple-register-group/` - Raw read multiple register group

Read multiple register group using provided controllers/devices data and starting address.

```
POST /raw/read-multiple-register-group/
```
*Required account permissions:* `viewer`

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `emptyName` | `Array` | Request body should contain an array of objects with proeprties provided below. |
| `deviceModbusId` | `number` | Identifier of device inside modbus network. |
| `address` | `number` | Starting address. |
| `kind` | `String` | Kind of register group:<br> - `holding` - 16-bit word. Read-Write.<br> - `coil` - Single bit. Read-Write.<br> - `discrete-input` - Single bit. Read-Only.<br> - `input` - 16-bit word. Read-Only. |
| `registersNum` | `number` | Count of registers that should be read. |
| `connection` | `Object` | Object that describe connection type.<br> - `kind` - Connection type, can be one of these values: "connect-code", "activation-code", "direct", "backward", "net-id".<br> - `code` - That property should be defined when connection kind is "connect-code" or "activation-code". When connection kind is "connect-code" that property should be string type. When connection kind is "activation-code" that property should be number type.<br> - `ip` - That property should be defined when connection kind is "direct".<br> - `port` - That property should be defined when connection kind is "direct".<br> - `mac` - That property should be defiend when connection kind is "backward".<br> - `connectCode` - That property can be optionally defined when connection kind is "backward".<br> - `networkId` - That property should be defined when connection kind is "net-id". |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '[{"deviceModbusId": 1, "address": 3, "registersNum": 5, "kind": "holding", "connection": {"kind": "backward", "mac": "11:22:33:44:55:66"}}, {"deviceModbusId": 111, "address": 459, "registersNum": 1, "kind": "holding", "connection": {"kind": "backward", "mac": "11:22:33:44:55:66"}}]' \
    "https://ocp.overvis.com/api/v1/raw/read-multiple-register-group/" | \
        jq -C
```

### Success response example

```json
[
    {
        "deviceModbusId": 1,
        "address": 3,
        "registersNum": 5,
        "result": {"ok":[65535,65535,65535,65535,65535]}
    },
    {
        "deviceModbusId": 111,
        "address": 459,
        "registersNum": 1,
        "result": {"ok":[9600]}
    }
]
```

### Error response example

```json
[
    {
        "deviceModbusId": 22,
        "address": 3,
        "registersNum": 5,
        "result": {"err":"MODBUS exception during reading register 3h: GatewayTargetDeviceFailedToRespond"}
    },
    {
        "deviceModbusId": 45,
        "address": 459,
        "registersNum": 1,
        "result": {"err":"MODBUS exception during reading register 459h: GatewayTargetDeviceFailedToRespond"}
    }
]
```


## <a name='Raw-read-register-group'></a> `POST /raw/read-register-group/` - Raw read register group

Read register group using provided controller/device data and starting address.

```
POST /raw/read-register-group/
```
*Required account permissions:* `viewer`

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `deviceModbusId` | `number` | Identifier of device inside modbus network. |
| `address` | `number` | Starting address. |
| `kind` | `String` | Kind of register group:<br> - `holding` - 16-bit word. Read-Write.<br> - `coil` - Single bit. Read-Write.<br> - `discrete-input` - Single bit. Read-Only.<br> - `input` - 16-bit word. Read-Only. |
| `registersNum` | `number` | Count of registers that should be read. |
| `connection` | `Object` | Object that describe connection type.<br> - `kind` - Connection type, can be one of these values: "connect-code", "activation-code", "direct", "backward", "net-id".<br> - `code` - That property should be defined when connection kind is "connect-code" or "activation-code". When connection kind is "connect-code" that property should be string type. When connection kind is "activation-code" that property should be number type.<br> - `ip` - That property should be defined when connection kind is "direct".<br> - `port` - That property should be defined when connection kind is "direct".<br> - `mac` - That property should be defiend when connection kind is "backward".<br> - `connectCode` - That property can be optionally defined when connection kind is "backward".<br> - `networkId` - That property should be defined when connection kind is "net-id". |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '{"deviceModbusId": 1, "address": 3, "registersNum": 5, "kind": "holding", "connection": {"kind": "backward", "mac": "11:22:33:44:55:66"}}' \
    "https://ocp.overvis.com/api/v1/raw/read-register-group/" | \
        jq -C
```

### Success response example

```json
{
    "ok": [65535,65535,65535,65535,65535]
}
```

### Error response example

```json
{
    "errorCode": "IncorrectRequest",
    "errorMessage": "body.connection.kind should be equal to one of the allowed values, body.connection.kind should be equal to one of the allowed values, body.connection.kind should be equal to one of the allowed values, body.connection.kind should be equal to one of the allowed values, body.connection.kind should be equal to one of the allowed values, body.connection should match some schema in anyOf"
}
```


