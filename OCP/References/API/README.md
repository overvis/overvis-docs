# Overvis API v1.0 Reference

## Basics

Overvis provides REST-like HTTP API with request payload and response encoded in JSON format.

Base API URL is `https://ocp.overvis.com/api/v1/`. Further documentation will list the requests as entrypoints with paths that should be concatenated to the base API URL. E.g. API version retrieval endpoint is `/version/`, that means, the full URL of the version request should be `https://ocp.overvis.com/api/v1/version/`.

All API requests require `Content-Type: application/json` HTTP header set.

All POST requests require HTTP payload to be a UTF-8 encoded plain-text with semantically-correct JSON content. Empty payload is not allowed for `POST` requests, send `null` if endpoint requires no data.

## Authentication

All requests except `/version/` and `/authenticate/` require authorization HTTP header.

Authorization HTTP header has the following format:

```
Authorization: token <token>
```

Here `<token>` is a randomly generated session token (UUID) returned by `/authenticate/` request.

Session token expires after 1 hour and is not automatically prolonged when being used.

## Errors

All requests return HTTP status code `200` if executed correctly.

`5xx` status codes represent server-side unexpected errors and are automatically reported to the developers.

`4xx` status codes represent errors that are client's concern and should be dealt with on the client side. These errors have JSON payload which look like this:

```json
{
    "errorCode": "ErrCode",
    "errorMessage": "Human-readable error message in english."
}
```

### Common errors

These errors can be returned by any endpoint:

| Status | Code                    | Description                                                                                                         |
| :----- | :---------------------- | :------------------------------------------------------------------------------------------------------------------ |
| `400`  | `CantDecodePayloadJSON` | HTTP POST payload can't be decoded.                                                                                 |
| `400`  | `IncorrectRequest`      | Invalid request format (POST payload, querystring, or URL properties are provided incorrectly).                     |
| `400`  | `PayloadIsTooLarge`     | HTTP POST payload is exceeding the limit of 1Mb.                                                                    |
| `400`  | `CantParseAuthToken`    | Incorrect authentication token format (should be lower-case UUID)                                                   |
| `400`  | `IncorrectContentType`  | HTTP request content type is not `application/json`                                                                 |
| `401`  | `NoAuthTokenProvided`   | No authentication token provided.                                                                                   |
| `401`  | `IncorrectAuthToken`    | Provided authentication token is incorrect. That may also mean that session is expired.                             |
| `401`  | `NotAuthorized`         | User is not authorized to make this specific action (no access).                                                    |
| `403`  | `TooManyRequests`       | API key quota of requests per minute is exceeded. Please repeat request later.                                      |
| `404`  | `ObjectNotFound`        | Requested object (network, device, parameter, template, etc.) doesn't exist in the database, or user has no access. |
| `404`  | `IncorrectEndpoint`     | Requested API endpoint doesn't exist (or method is wrong).                                                          |

## Dates

All dates in all API requests and responses use [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format with or without microseconds.

All dates returned in responses are in UTC. Example: `2018-05-14T16:28:59.948845Z`. Requests can provide dates in any timezone.

## Endpoints
- [Publicly Accessible API](./Publicly_Accessible_API.md)
  - [`GET  /version/` - Get API Version Information](./Publicly_Accessible_API.md#GetVersion)
- [Authentication API](./Authentication_API.md)
  - [`POST /authenticate/` - Login](./Authentication_API.md#Authenticate)
  - [`POST /logout/` - Logout](./Authentication_API.md#Logout)
- [User API](./User_API.md)
  - [`GET  /user/orgs/` - List User Organizations Access](./User_API.md#GetUserOrgList)
- [Organization API](./Organization_API.md)
  - [`GET  /org/:orgId/nets/device-param-tree/` - Get Organization Networks-Devices-Parameters Tree](./Organization_API.md#OrgGetDeviceParamTree)
  - [`POST /org/:orgId/dashboard/add-widgets/` - Add Widgets to Dashboard](./Organization_API.md#DashboardAddWidgets)
- [Networks API](./Networks_API.md)
  - [`GET  /org/:orgId/nets/` - Get Organization Networks List](./Networks_API.md#OrgGetNets)
  - [`GET  /net/:netId/` - Get Network Info](./Networks_API.md#NetGetInfo)
- [Readings API](./Readings_API.md)
  - [`GET  /readings/get/` - Get Readings](./Readings_API.md#GetReadings)
  - [`POST /readings/dispersed/` - Get Dispersed Readings](./Readings_API.md#GetDispersedReadings)
  - [`GET  /readings/date-range/` - Range of Readings History](./Readings_API.md#GetReadingsDateRange)
  - [`POST /readings/min-max-range/` - Range of Readings Values for a Given Period](./Readings_API.md#GetReadingsMinMaxRange)
- [Alert API](./Alert_API.md)
  - [`GET  /org/:orgId/alerts/` - Get List of Alerts](./Alert_API.md#GetAlertsList)
  - [`GET  /alert/:alertId/` - Get Alert Data](./Alert_API.md#GetAlertData)
  - [`GET  /alert/:alertId/status/` - Get Alert Status Information](./Alert_API.md#GetAlertStatus)
- [Device Parameter API](./Device_Parameter_API.md)
  - [`POST /org/:orgId/params/get-id-by-reference/` - Get parameter identifier by reference](./Device_Parameter_API.md#GetParamIdByReference)
  - [`GET  /params/read-realtime/` - Read current value of the parameter from the device](./Device_Parameter_API.md#RealtimeReadParams)
  - [`POST /param/:paramId/write/` - Write parameter value to the device](./Device_Parameter_API.md#WriteParam)
- [Raw Readings API](./Raw_Readings_API.md)
  - [`POST /raw/read-multiple-register-group/` - Raw read multiple register group](./Raw_Readings_API.md#RawReadMultipleRegisterGroup)
  - [`POST /raw/read-register-group/` - Raw read register group](./Raw_Readings_API.md#RawReadRegisterGroup)
- [Visualization API](./Visualization_API.md)
  - [`GET  /v1/vis/:visId/data/` - Get Visualization Data](./Visualization_API.md#GetVisualizationData)
  - [`GET  /v1/vis/:visId/data/save/` - Save Visualization Data](./Visualization_API.md#SaveVisualizationData)
