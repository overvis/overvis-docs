# Overvis API v1.0 :: Device Parameter API

## <a name='Get-parameter-identifier-by-reference'></a> `POST /org/:orgId/params/get-id-by-reference/` - Get parameter identifier by reference

This request take parameter reference string
(https://docs.overvis.com/#/OCP/References/Visualizations?id=device-parameters-can-be-referenced-in-one-of-the-following-ways)
as argument ans returns ID of this parameter inside system.

```
POST /org/:orgId/params/get-id-by-reference/
```

_Required account permissions:_ `operator`

### Parameters - `Params`

| Name    | Type     | Description                 |
| :------ | :------- | :-------------------------- |
| `orgId` | `Number` | System IDs of organization. |

### Parameters - `Body`

| Name        | Type     | Description                 |
| :---------- | :------- | :-------------------------- |
| `emptyName` | `String` | Parameter reference string. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '"11-22-33-44-55-66>1>5000"' \
    "https://ocp.overvis.com/api/v1/org/1/params/get-id-by-reference/" | \
        jq -C
```

### Success response example

```json
551
```

### Error response example

```json
{
  "errorCode": "ObjectNotFound",
  "errorMessage": "Parameter not found."
}
```

## <a name='Read-current-value-of-the-parameter-from-the-device'></a> `GET /params/read-realtime/` - Read current value of the parameter from the device

Performs reading from the device and returns values of the requested parameters.</p> <p>More than
one value can be requested at a time. Results will be returned as a dictionary where key is a
parameter ID. Returned values are automatically converted from Modbus registers (16 bit groups) to
the actual parameter value number depending on the parameter type.</p> <p>If some parameter ID is
not found, it will be ommited from the result.

```
GET /params/read-realtime/
```

_Required account permissions:_ `demo`

### Parameters - `Querystring`

| Name           | Type      | Description                                                                                                                     |
| :------------- | :-------- | :------------------------------------------------------------------------------------------------------------------------------ |
| `paramIds`     | `String`  | System IDs of parameters that should be read.                                                                                   |
| `lang`         | `String`  | The language of the returned message, one of: `en`, `ru`, `pl`. Needed for representations of bit values (yes/no, on/off, etc.) |
| `failbackToDb` | `Boolean` | Get value from the database if no real readings.                                                                                |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/params/read-realtime/?paramIds=547,545,537,546&lang=en&failbackToDb=true" | \
    jq -C
```

### Success response

| Name             | Type     | Description                                                                                                                                                                                                                                                                 |
| :--------------- | :------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `value`          | `String` | The current value of the parameter in numeric form.                                                                                                                                                                                                                         |
| `formattedValue` | `String` | The current value of the parameter as a string ready for display to a user. For example, bit values may be formatted as "True" or "False" depending on the parameter setting. Numeric formatted values are automatically rounded depending on value type and multiplicator. |

### Success response example

```json
{
  "537": { "value": "1", "formattedValue": "Yes" },
  "545": { "value": "-1", "formattedValue": "-1" },
  "546": { "value": "12.3400001", "formattedValue": "12.34" }
}
```

## <a name='Write-parameter-value-to-the-device'></a> `POST /param/:paramId/write/` - Write parameter value to the device

This requests takes a numeric value, converts it to the device registers values (one or more 16 bit
packets) and sends it to the device. It takes network and device information by the parameter
system ID.</p> <p>Before writing, system tries to read the current value of the parameter and
returns it along with the new one.</p> <p>Value is truncated to a specific precision different for
each parameter type, for example if you send "22.22" to a u16 parameter, only "22" will be written.

```
POST /param/:paramId/write/
```

_Required account permissions:_ `operator`

### Parameters - `Params`

| Name      | Type     | Description              |
| :-------- | :------- | :----------------------- |
| `paramId` | `Number` | System IDs of parameter. |

### Parameters - `Body`

| Name       | Type     | Description                                                                                                                                                                                                         |
| :--------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `newValue` | `String` | Value to be written as a string. Depends on the parameter type. For signed/unsigned integer types, bit groups and float types provide numeric value as a string, e.g. "-1234.56". For bit type, provide "0" or "1". |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '{
            "newValue": "1"
        }' \
    "https://ocp.overvis.com/api/v1/param/551/write/" | \
        jq -C
```

### Success response

| Name       | Type     | Description                                                                                                                                                                                                                                                                    |
| :--------- | :------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ok`       | `Object` | Object containing `oldValue` and `newValue` on success.                                                                                                                                                                                                                        |
| `err`      | `String` | Error message returned by the device communication backend. It is not request failure, so it is returned with status 200 in a response body. All request errors are returned as usual with 4xx/5xx statuses and body as described in the <a href="README.md">main section.</a> |
| `oldValue` | `String` | Old parameter value before writing.                                                                                                                                                                                                                                            |
| `newValue` | `String` | Value that was sent by this request.                                                                                                                                                                                                                                           |

### Success response example

```json
{
  "ok": {
    "oldValue": "22",
    "newValue": "1"
  }
}
```

### Error response example

```json
{
  "err": "can't compose new value"
}
```
