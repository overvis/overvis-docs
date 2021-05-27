<a name="top"></a>
# Overvis API v0.1.0 :: User API

- [`GET /user/orgs/` - List User Organizations Access](#List-User-Organizations-Access)

## <a name='List-User-Organizations-Access'></a> `GET /user/orgs/` - List User Organizations Access
[Back to top](#top)

Returns a list of all organizations current account (API key) has access to. Includes access level (roles) information.

```
GET /user/orgs/
```
*Required account permissions:* `demo`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/user/orgs/" | \
    jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `orgId` | `Number` | System ID of the organization. |
| `orgName` | `String` | Name of the organization. |
| `userRole` | `String` | Role of the user in the organization:<br> - `owner` - Full access.<br> - `manager` - Full access, except user management.<br> - `operator` - Can't edit organization objects, but can send write requests to organization devices.<br> - `viewer` - View-only access.<br> - `monitor-operator` - Monitoring UI access with ability to send write requests to organization devices.<br> - `monitor-viewer` - View-only monitoring UI access. |

### Success response example

```json
[
    {
        "orgId": 1,
        "orgName": "Деворг",
        "userRole": "owner"
    }
]
```


