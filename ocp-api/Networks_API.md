<a name="top"></a>
# Overvis API v0.1.0 :: Networks API

- [`GET /net/:netId/` - Get Network Info](#Get-Network-Info)
- [`GET /org/:orgId/nets/` - Get Organization Networks List](#Get-Organization-Networks-List)

## <a name='Get-Network-Info'></a> `GET /net/:netId/` - Get Network Info
[Back to top](#top)

Retrieves information about one network.

```
GET /net/:netId/
```
*Required account permissions:* `viewer`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "ca09571a-79d9-4e07-8c84-2bc5f60189e3" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/net/1/" | \
        jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `id` | `Number` | Unique non-changing ID of the network in the system. |
| `orgId` | `Number` | Unique non-changing ID of the organization in the system. |
| `name` | `String` | User-provided name of the network. |
| `slug` | `String` | URL-conformant unique code of the network. |
| `createdOn` | `String` | Date and time when the network was created. |
| `disabledOn` | `String` | **optional** Date and time when the network was disabled. If not set - network is active. |
| `disabledBy` | `String` | **optional** Actor who has disabled the network. One of: "admin", "billing", "user". |
| `disabledNote` | `String` | **optional** Note with description of the reason why the network was disabled. |
| `ip` | `String` | Network IP address (used to establish connection to direct-connection networks). |
| `port` | `Number` | Network TCP/IP port (used to establish connection to direct-connection networks). |
| `mac` | `String` | **optional** MAC adrress of the network. Used only for backward-connection networks. |
| `protocol` | `String` | Server communication protocol. Currently always "modbustcp". |
| `controllerKind` | `String` | Controller used for this network. E.g., "et485", "em486", "em481", "em480", "em482", "rpm416", "other", etc. |
| `controllerModbusUnitid` | `Number` | Modbus address of the controller in the RS-485 network. |
| `devicePasswords` | `String[]` | Passwords that Overvis sends to this controller after the connection is established. Used only for devices that support password protection. |

### Success response example

```json
{
    "id": 1,
    "orgId": 1,
    "name": "Some Network",
    "slug": "some-network",
    "createdOn": "2020-05-06T22:28:54.331Z",
    "ip": "127.0.0.1",
    "port": 502,
    "mac": "11:22:33:44:55:66",
    "protocol": "modbustcp",
    "controllerKind": "em481",
    "controllerModbusUnitId": 111,
    "devicePasswords": ["mypass"]
}
```


## <a name='Get-Organization-Networks-List'></a> `GET /org/:orgId/nets/` - Get Organization Networks List
[Back to top](#top)

Returns basic information about organization networks.

```
GET /org/:orgId/nets/
```
*Required account permissions:* `viewer`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "ca09571a-79d9-4e07-8c84-2bc5f60189e3" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/org/1/nets/" | \
        jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `id` | `Number` | Unique non-changing ID of the network in the system. |
| `name` | `String` | User-provided name of the network. |
| `slug` | `String` | URL-conformant unique code of the network. |
| `lid` | `String` | Connection identifier (Logical ID) of the network. Is one of the following:<br> - `{ "backward": "xx-xx-xx-xx-xx-xx" }` where `xx-xx-xx-xx-xx-xx` is backward connection MAC address.<br> - `{ "direct": "<ip>:<port>" }` where `<ip>:<port>` is IP-address and port for the direct connection. |

### Success response example

```json
[
    {
        "id": 17,
        "name": "Network 1",
        "slug": "network-1",
        "lid": { "backward": "d8:80:39:4e:8d:e9" }
    },
    {
        "id": 18,
        "name": "Some other network",
        "slug": "some-other-network",
        "lid": { "direct": "1.2.3.4:502" }
    }
]
```


