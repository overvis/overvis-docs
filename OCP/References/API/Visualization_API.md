# Overvis API v1.0 :: Visualization API

## <a name='Get-Visualization-Data'></a> `GET /v1/vis/:visId/data/` - Get Visualization Data

Returns the data that the user entered in the visualization. The answer is a "key: value" pair, where key is a string and value is a string.

```
GET /v1/vis/:visId/data/
```
*Required account permissions:* `demo`

### Parameters - `Param`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `visId` | `Number` | System ID of the visualization. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/vis/7/data/" | \
    jq -C
```

### Success response example

```json
{
    "roomName": "Room 1",
    "ownerName": "John"
}
```


## <a name='Save-Visualization-Data'></a> `GET /v1/vis/:visId/data/save/` - Save Visualization Data

Saves the data entered by the user in the visualization.

```
GET /v1/vis/:visId/data/save/
```
*Required account permissions:* `manager`

### Parameters - `Param`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `visId` | `Number` | System ID of the visualization. |

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `code` | `String` | Key for the value. |
| `value` | `String` | Value to be saved. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '{ "code": "roomName", "value": "Room 1" }' \
    "https://ocp.overvis.com/api/v1/vis/7/data/save/" | \
        jq -C
```

### Success response example

```json
{}
```


