<a name="top"></a>
# Overvis API v0.1.0 :: Authentication API

- [`POST /authenticate/` - Login](#Login)
- [`POST /logout/` - Logout](#Logout)

## <a name='Login'></a> `POST /authenticate/` - Login
[Back to top](#top)

Performs authentication for a given user API key. Returns session token that should be provided to other API endpoints. This request doesn't require authorization header.

```
POST /authenticate/
```
*Required account permissions:* `none`

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `apiKey` | `String` | API key (UUID). |
| `password` | `String` | API key password. |

### Examples

```bash
curl -s -S -H "Content-Type: application/json" \
        -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
        "https://ocp.overvis.com/api/v1/authenticate/" | \
    jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `token` | `String` | Session token that should be provided to other API endpoints in the `Authorization` header. |
| `expiresOn` | `Date` | Expiration time of the provided token. |

### Success response example

```json
{
    "token": "cdf2923f-c89f-41e5-91af-f139f0c2e7d0",
    "expiresOn": "2020-05-07T19:49:34.213Z"
}
```

### Error response

#### Error response - `401`
| Name     | Description                           |
|:---------|:--------------------------------------|
| `InvalidKey` | API key doesn't exist. |
| `NotAuthorized` | Incorrect API key password. |


## <a name='Logout'></a> `POST /logout/` - Logout
[Back to top](#top)

Deletes session token immediately.

```
POST /logout/
```
*Required account permissions:* `none`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d 'null' \
    "https://ocp.overvis.com/api/v1/logout/" | \
        jq -C
```

### Success response example

```json
{}
```


