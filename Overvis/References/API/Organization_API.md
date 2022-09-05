# Overvis API v1.0 :: Organization API

## <a name='Add-Widgets-to-Dashboard'></a> `POST /org/:orgId/dashboard/add-widgets/` - Add Widgets to Dashboard

Adds selected parameters to the organization dashboard.

```
POST /org/:orgId/dashboard/add-widgets/
```
*Required account permissions:* `manager`

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `paramIds` | `Number[]` | System IDs of parameters that should be added to dashboard. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '{ "paramIds": [1, 2, 3] }' \
    "https://ocp.overvis.com/api/v1/org/1/dashboard/add-widgets/" | \
        jq -C
```

### Success response example

```json
{}
```


## <a name='Get-Organization-Networks-Devices-Parameters-Tree'></a> `GET /org/:orgId/nets/device-param-tree/` - Get Organization Networks-Devices-Parameters Tree

Returns full tree of networks, devices and all their parameters in organization.</p> <p>Tree is a multilevel dictionary with objects' unique system IDs as keys. First level is networks.</p> <p>Each object on each level contains user-specified name.</p> <p>Parameters contain three additional boolean fields:</p> <ul> <li>`address` - modbus parameter address.</li> <li>`is_tracked` - parameter is specified by user as 'tracked' (periodically reading on background).</li> <li>`is_programmable` - parameter value can be edited remotely on the device.</li> </ul>

```
GET /org/:orgId/nets/device-param-tree/
```
*Required account permissions:* `viewer`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/org/1/nets/device-param-tree/" | \
        jq -C
```

### Success response example

```json
{
    "4": {
        "name": "Test EM Direct",
        "solution": "standalone",
        "devices": {
            "4": {
                "name": "EM",
                "modbusUnitId": 111,
                "parameters": {
                    "4": {
                        "name": "Time, min",
                        "address": 100,
                        "isTracked": true,
                        "isProgrammable": true
                    },
                    "5": {
                        "name": "Some parameter description",
                        "address": 101,
                        "isTracked": false,
                        "isProgrammable": true
                    }
                }
            }
        }
    },
    "5": {
        "name": "Test EM Direct 2",
        "solution": "standalone",
        "devices": {
            "5": {
                "name": "EM",
                "modbusUnitId": 111,
                "parameters": {
                    "6": {
                        "name": "Time, min",
                        "address": 200,
                        "isTracked": true,
                        "isProgrammable": true
                    },
                    "7": {
                        "name": "Some parameter description",
                        "address": 201,
                        "isTracked": false,
                        "isProgrammable": true
                    }
                }
            }
        }
    },
    "6": {
        "name": "Test readings",
        "solution": "standalone",
        "devices": {
            "6": {
                "name": "EM",
                "modbusUnitId": 111,
                "parameters": {
                    "8": {
                        "name": "Time, min",
                        "address": 300,
                        "isTracked": true,
                        "isProgrammable": true
                    },
                    "9": {
                        "name": "Some parameter description",
                        "address": 301,
                        "isTracked": false,
                        "isProgrammable": true
                    },
                    "10": {
                        "name": "Version",
                        "address": 302,
                        "isTracked": true,
                        "isProgrammable": true
                    }
                }
            }
        }
    }
}
```


