# Overvis API v1.0 :: Device Parameter API

## <a name='Get-parameter-identifier-by-reference'></a> `POST /org/:orgId/params/get-id-by-reference/` - Get parameter identifier by reference

This request take parameter reference string (https://docs.overvis.com/#/OCP/References/Visualizations?id=device-parameters-can-be-referenced-in-one-of-the-following-ways) as argument ans returns ID of this parameter inside system.

```
POST /org/:orgId/params/get-id-by-reference/
```
*Required account permissions:* `operator`

### Parameters - `Params`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `orgId` | `Number` | System IDs of organization. |

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
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

Performs reading from the device and returns values of the requested parameters.</p> <p>More than one value can be requested at a time. Results will be returned as a dictionary where key is a parameter ID. Returned values are automatically converted from Modbus registers (16 bit groups) to the actual parameter value number depending on the parameter type.</p> <p>If some parameter ID is not found, it will be ommited from the result.

```
GET /params/read-realtime/
```
*Required account permissions:* `demo`

### Parameters - `Querystring`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `paramIds` | `String` | System IDs of parameters that should be read. |
| `lang` | `String` | The language of the returned message, one of: `en`, `ru`, `pl`, `ua`. Needed for representations of bit values (yes/no, on/off, etc.) |
| `failbackToDb` | `Boolean` | Get value from the database if no real readings. |

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

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `value` | `String` | The current value of the parameter in numeric form. |
| `formattedValue` | `String` | The current value of the parameter as a string ready for display to a user. For example, bit values may be formatted as "True" or "False" depending on the parameter setting. Numeric formatted values are automatically rounded depending on value type and multiplicator. |

### Success response example

```json
{
    "537": { "value": "1", "formattedValue": "Yes" },
    "545": { "value": "-1", "formattedValue": "-1" },
    "546": { "value": "12.3400001", "formattedValue": "12.34" },
}
```


## <a name='Returns-last-read-values-of-the-parameters'></a> `GET /params/last-readings/` - Returns last read values of the parameters

Returns last read values of the parameters.</p> <p>More than one value can be requested at a time. Results will be returned as a dictionary where key is a parameter ID. Returned values are automatically converted from Modbus registers (16 bit groups) to the actual parameter value number depending on the parameter type.</p> <p>If some parameter ID is not found, it will be ommited from the result.

```
GET /params/last-readings/
```
*Required account permissions:* `demo`

### Parameters - `Querystring`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `paramIds` | `String` | System IDs of parameters that should be read. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/params/last-readings/?paramIds=547,545,537,546" | \
    jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `value` | `String` | The value of the parameter in numeric form. |
| `formattedValue` | `String` | The value of the parameter as a string ready for display to a user. For example, bit values may be formatted as "True" or "False" depending on the parameter setting. Numeric formatted values are automatically rounded depending on value type and multiplicator. |

### Success response example

```json
{
    "537": { "value": "1", "readOn": "2023-12-14T01:35:44Z", "formattedValue": "Yes" },
    "545": { "value": "-1", "readOn": "2023-12-14T01:35:44Z", "formattedValue": "-1" },
    "546": { "value": "12.3400001", "readOn": "2023-12-14T01:35:44Z", "formattedValue": "12.34" },
    "555": { "err": "no-value" },
}
```


## <a name='Returns-last-read-values-of-the-parameters-up-to-specified-dates'></a> `POST /params/readings-on-time/` - Returns last read values of the parameters up to specified dates

Returns the last read values of the specified parameters up to the given date.</p> <p>You can request more than one value at a time. The request body should be an object containing the `orgId`, `tillDate`, and an array of parameter IDs. The response will be an object where each key is a parameter ID and the value is either the last reading or null if no reading was found.

```
POST /params/readings-on-time/
```
*Required account permissions:* `demo`

### Parameters - `Parameter`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `orgId` | `Number` | Organization ID to which the parameters belong. |
| `tillDate` | `String` | The date up to which the last reading should be fetched (in ISO 8601 format). |
| `paramIds` | `Number[]` | Array of parameter IDs for which the last readings are requested. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '{"orgId":1,"tillDate":"2025-01-01T00:00:00.000Z","paramIds":[556,557]}' \
    "https://ocp.overvis.com/api/v1/params/readings-on-time/" | \
    jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `response` | `Object` | An object where each key is a parameter ID and the value is either the last reading or null if no reading was found. |
| `response.` | `String` | <paramId>.readOn The date and time when the last reading was taken (in ISO 8601 format). |

### Success response example

```json
{
    "556": {
        "readOn": "2024-12-11T09:50:00.000Z",
        "value": "1499"
    },
    "557": {
        "readOn": "2024-12-11T09:50:00.000Z",
        "value": "1599"
    },
    "9999": null
}
```

### Error response example

```json
{
    "errorCode": "ObjectNotFound",
    "errorMessage": "Params not found"
}
```


## <a name='Write-parameter-value-to-the-device'></a> `POST /param/:paramId/write/` - Write parameter value to the device

This requests takes a numeric value, converts it to the device registers values (one or more 16 bit packets) and sends it to the device. It takes network and device information by the parameter system ID.</p> <p>Before writing, system tries to read the current value of the parameter and returns it along with the new one.</p> <p>Value is truncated to a specific precision different for each parameter type, for example if you send "22.22" to a u16 parameter, only "22" will be written.

```
POST /param/:paramId/write/
```
*Required account permissions:* `operator`

### Parameters - `Params`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `paramId` | `Number` | System IDs of parameter. |

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
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

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `ok` | `Object` | Object containing `oldValue` and `newValue` on success. |
| `err` | `String` | Error message returned by the device communication backend. It is not request failure, so it is returned with status 200 in a response body. All request errors are returned as usual with 4xx/5xx statuses and body as described in the <a href="README.md">main section.</a> |
| `oldValue` | `String` | Old parameter value before writing. |
| `newValue` | `String` | Value that was sent by this request. |

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


