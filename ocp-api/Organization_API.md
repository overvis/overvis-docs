<a name="top"></a>
# Overvis API v0.1.0 :: Organization API

- [`POST /org/:orgId/dashboard/add-widgets/` - Add Widgets to Dashboard](#Add-Widgets-to-Dashboard)
- [`GET /org/:orgId/nets/device-param-tree/` - Get Organization Networks-Devices-Parameters Tree](#Get-Organization-Networks-Devices-Parameters-Tree)

## <a name='Add-Widgets-to-Dashboard'></a> `POST /org/:orgId/dashboard/add-widgets/` - Add Widgets to Dashboard
[Back to top](#top)

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
    -d '{"apiKey": "ca09571a-79d9-4e07-8c84-2bc5f60189e3" }' \
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
[Back to top](#top)

Returns full tree of networks, devices and all their parameters in organization.</p> <p>Tree is a multilevel dictionary with objects' unique system IDs as keys. First level is networks.</p> <p>Each object on each level contains user-specified name.</p> <p>Parameters contain two additional boolean fields:</p> <ul> <li>`is_tracked` - parameter is specified by user as 'tracked' (periodically reading on background).</li> <li>`is_programmable` - parameter value can be edited remotely on the device.</li> </ul>

```
GET /org/:orgId/nets/device-param-tree/
```
*Required account permissions:* `viewer`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "ca09571a-79d9-4e07-8c84-2bc5f60189e3" }' \
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
        "devices": {
            "4": {
                "name": "EM",
                "parameters": {
                    "4": {
                        "name": "Time, min",
                        "isTracked": true,
                        "isProgrammable": true
                    },
                    "5": {
                        "name": "Some parameter description",
                        "isTracked": false,
                        "isProgrammable": true
                    }
                }
            }
        }
    },
    "5": {
        "name": "Test EM Direct 2",
        "devices": {
            "5": {
                "name": "EM",
                "parameters": {
                    "6": {
                        "name": "Time, min",
                        "isTracked": true,
                        "isProgrammable": true
                    },
                    "7": {
                        "name": "Some parameter description",
                        "isTracked": false,
                        "isProgrammable": true
                    }
                }
            }
        }
    },
    "6": {
        "name": "Test readings",
        "devices": {
            "6": {
                "name": "EM",
                "parameters": {
                    "8": {
                        "name": "Time, min",
                        "isTracked": true,
                        "isProgrammable": true
                    },
                    "9": {
                        "name": "Some parameter description",
                        "isTracked": false,
                        "isProgrammable": true
                    },
                    "10": {
                        "name": "Version",
                        "isTracked": true,
                        "isProgrammable": true
                    }
                }
            }
        }
    }
}
```


