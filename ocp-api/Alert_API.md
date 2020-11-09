<a name="top"></a>
# Overvis API v0.1.0 :: Alert API

- [`GET /alert/:alertId/` - Get Alert Data](#Get-Alert-Data)
- [`GET /alert/:alertId/status/` - Get Alert Status Information](#Get-Alert-Status-Information)
- [`GET /org/:orgId/alerts/` - Get List of Alerts](#Get-List-of-Alerts)

## <a name='Get-Alert-Data'></a> `GET /alert/:alertId/` - Get Alert Data
[Back to top](#top)

Returns full information about alert structure. Includes list of actions, followUps and conditions.

```
GET /alert/:alertId/
```
*Required account permissions:* `demo`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "ca09571a-79d9-4e07-8c84-2bc5f60189e3" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/alert/4/" | \
    jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `id` | `Number` | System ID of the alert. |
| `name` | `String` | Alert name. |
| `slug` | `String` | URL-compatible code for the alert. |
| `description` | `String` | **optional** Alert description added by user. |
| `conditionLogic` | `String` | Logic of the alert triggering:<br> - `all` - all conditions should trigger to start the event,<br> - `any` - any of the conditions should trigger to start the event. |
| `logAs` | `String` | Alert level: `none`, `info`, `warning`, `failure`, `critical`. |
| `conditions` | `Object[]` | List of alert conditions:<br> - `paramId`: Number, System ID of the parameter.<br> - `kind`: String, Kind of operation. `g` - greater than, `ge` - greater or equal to, `l` - less then, `le` - less or equal to, `eq` - equal to, `neq` - not equal to, `reading-error` - reading error.<br> - `param`: String, parameter "system path" consisting of the parameter id, parameter slug, device slug, and network slug, @ - separator character.<br> - `value`: Number, optional, value for comparison operation. |
| `actions` | `Object[]` | Alert actions list. Each record contains:<br> - `timeoutMinutes`: Number, the delay for this action.<br> - `type`: String, optional, notification method: `sms`, `email`, `voice-call`.<br> - `recepient`: String, optional, notification recipient. Email or phone number, depending on `type`. |
| `followUps` | `Object[]` | Alert actions that are performed upon completion of the alert. Each record contains:<br> - `stage`: String, stage of the alert when this followup is performed: `confirmed`, `not-confirmed`, `resolved`.<br> - `timeoutMinutes`: Number, the delay in minutes for this action.<br> - `type`: String, optional, notification method: `sms`, `email`, `voice-call`.<br> - `recepient`: String, optional, notification recipient. Email or phone number, depending on `type`. |

### Success response example

```json
{
    "id": 4,
    "name": "AND Logic Alert",
    "logAs": "warning",
    "slug": "and-logic-alert",
    "description": "alert description",
    "conditionLogic": "all",
    "conditions": [
        {
            "paramId": 542,
            "kind": "eq",
            "param": "542#coil@example-device-bc@backward-connection-to-emu",
            "value": 1,
        },
        {
             "paramId": 543,
            "kind": "eq",
            "param": "543#discrete-input@example-device-bc@backward-connection-to-emu",
            "value": 1,
        },
    ],
    "actions": [
        {
            "timeoutMinutes": 5,
            "type": "email",
            "recepient": "asdf@overvis.com",
        },
        {
            "timeoutMinutes": 0,
            "type": "sms",
            "recepient": "+380330000000",
        }
    ],
    "followUps": [
        {
            "stage": "confirmed",
            "timeoutMinutes": 2,
            "type": "email",
            "recepient": "asdf@overvis.com",
        },
        {
            "stage": "not-confirmed",
            "timeoutMinutes": 0,
            "type": "sms",
            "recepient": "+380330000000",
        }
    ],
}
```


## <a name='Get-Alert-Status-Information'></a> `GET /alert/:alertId/status/` - Get Alert Status Information
[Back to top](#top)

Returns alert status information, including current state (active or not), system ID of the last record, when it was started, confirmed, and resolved. Also includes information about all condition statuses.

```
GET /alert/:alertId/status/
```
*Required account permissions:* `demo`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "ca09571a-79d9-4e07-8c84-2bc5f60189e3" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/alert/4/status/" | \
    jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `id` | `Number` | System ID of the alert. |
| `lastRecordId` | `Number` | **optional** System ID of the last record. Alert record is created each time when the alert is activated. |
| `isActive` | `Boolean` | Is alert currently active. |
| `startedOn` | `Date` | **optional** Date when the alert was activated. |
| `closedOn` | `Date` | **optional** Date when the alert was resolved. |
| `confirmed` | `Object` | **optional** An object containing confirmation details:<br> - `on`: Date, date and time when alert was confirmed.<br> - `from`: String, email or phone number of the confirmer. |
| `conditions` | `Object` | List of alert conditions and their statuses. Object key is system  ID of the condition. Sub-object contains:<br> - `lastValue`: Number, last reading value for this condition.<br> - `isTriggered`: Boolean, condition is met or not. |

### Success response example

```json
{
    "id": 4,
    "lastRecordId": 22,
    "isActive": true,
    "startedOn": "2020-08-17T14:30:12.569Z",
    "confirmed": {
        "on" "2020-08-18T14:30:12.569Z",
        "from": "asd@overvis.com",
    },
    "conditions": {
        "122": { "lastValue": 1, "isTriggered": true },
        "123": { "lastValue": 1, "isTriggered": true },
    },
}
```


## <a name='Get-List-of-Alerts'></a> `GET /org/:orgId/alerts/` - Get List of Alerts
[Back to top](#top)

Returns an array with basic information about all alerts that belong to the organization. The list contains names and system IDs.

```
GET /org/:orgId/alerts/
```
*Required account permissions:* `demo`

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "ca09571a-79d9-4e07-8c84-2bc5f60189e3" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/org/1/alerts/" | \
    jq -C
```

### Success response

| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `id` | `Number` | System ID of the alert. |
| `slug` | `String` | URL-compatible code for the alert. |
| `name` | `String` | Alert name. |

### Success response example

```json
[
    { "id": 4, "name": "AND Logic Alert", "slug": "and-logic-alert" },
    { "id": 3, "name": "OR Logic Alert", "slug": "asdf" }
]
```


