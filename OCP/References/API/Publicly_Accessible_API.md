# Overvis API v1.0 :: Publicly Accessible API

## <a name='Get-API-Version-Information'></a> `GET /version/` - Get API Version Information

Provides version of the server-side Overvis software and API. This request doesn't require authorization header or any additional data.

```
GET /version/
```
*Required account permissions:* `none`

### Examples

```bash
curl -s -S -H "Content-Type: application/json" \
        "https://ocp.overvis.com/api/v1/version/" | \
    jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `apiVersion` | `String` | Version of the API protocol. |
| `apiServerVersion` | `String` | API server software version. |

### Success response example

```json
{
    "apiVersion": "0.1.0",
    "apiServerVersion": "1.0.0"
}
```


