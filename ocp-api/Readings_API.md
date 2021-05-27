<a name="top"></a>
# Overvis API v0.1.0 :: Readings API

- [`POST /readings/dispersed/` - Get Dispersed Readings](#Get-Dispersed-Readings)
- [`GET /readings/get/` - Get Readings](#Get-Readings)
- [`GET /readings/date-range/` - Range of Readings History](#Range-of-Readings-History)
- [`POST /readings/min-max-range/` - Range of Readings Values for a Given Period](#Range-of-Readings-Values-for-a-Given-Period)

## <a name='Get-Dispersed-Readings'></a> `POST /readings/dispersed/` - Get Dispersed Readings
[Back to top](#top)

Returns specified number of data points for a requested parameters on a given time range.</p> <p>Result is an array of "segment" records. Each segment corresponds to a distance between data points calculated from `from`, `till` and `points` request parameters.</p> <p>Each record is an array, first item is a datetime of the data point. Other items are parameters values during that segment in the order of parameter IDs provided in the request. Consequently, there should be `number of parameters + 1` items in each record.</p> <p>If there was any reading during given segment, parameter will have a value, otherwise it will be `null`. If all parameters have no value on a given segment, record for this segment will be omitted from the response.</p> <p>If there are several records for a parameter during this segment, only the first one is provided.

```
POST /readings/dispersed/
```
*Required account permissions:* `viewer`

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `paramIds` | `String[]` | List of parameter IDs. |
| `from` | `String` | Start date of the check range. |
| `till` | `String` | End date of the check range. |
| `points` | `Number` | Number of points to take in this date range (for each parameter). Points will be linearly distributed on a given date range. Maximum: 1000.<br> For example, if `from` and `till` specify one hour range, and `points` is 10, then response will contain 10 points for each parameter ID, one reading every 6 minutes. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '{
        "paramIds": [271, 272],
        "from": "2020-05-26T16:28:00Z",
        "till": "2020-05-28T17:25:00Z",
        "points": 1000
    }' \
    "https://ocp.overvis.com/api/v1/readings/dispersed/" | \
        jq -C
```

### Success response example

```json
[
    ["2019-07-26T16:28:00Z", [7.0, 2018.0]],
    ["2019-07-26T16:33:42Z", [7.0, 2018.0]],
    ["2019-07-26T16:39:24Z", [7.0, 2018.0]],
    ["2019-07-26T16:45:06Z", [7.0, 2018.0]],
    ["2019-07-26T16:50:48Z", [7.0, null]],
    ["2019-07-26T16:56:30Z", [7.0, 2018.0]],
    ["2019-07-26T17:02:12Z", [7.0, 2018.0]],
    ["2019-07-26T17:07:54Z", [null, 2018.0]],
    ["2019-07-26T17:13:36Z", [7.0, 2018.0]],
    ["2019-07-26T17:19:18Z", [7.0, 2018.0]]
]
```


## <a name='Get-Readings'></a> `GET /readings/get/` - Get Readings
[Back to top](#top)

Returns stored data points for a requested time range for a given parameter.</p> <p>Result is an array of data points. Each data point contains a date (ISO string) and a value (number). Data points are sorted by dates in a descending order. Multiplier is applied to parameter value, if specified in parameter settings.

```
GET /readings/get/
```
*Required account permissions:* `viewer`

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `paramId` | `String` | ID of the parameter. |
| `from` | `String` | Start date of the range. If omitted, response will contain last pointsLimit data points up to till date. |
| `till` | `String` | End date of the range. If omitted, end date will be the current time. |
| `pointsLimit` | `Number` | Limit on the number of the returned data points. If specified date range includes more points, only the most recent ones will be returned. Maximum and default: 1000. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/readings/get/?paramId=284&from=2020-01-01T00:00:00Z&till=2020-08-17T00:00:00Z&pointsLimit=100" | \
        jq -C
```

### Success response example

```json
[
    ["2019-07-26T16:28:00Z", 7.0],
    ["2019-07-26T16:33:42Z", 7.0],
    ["2019-07-26T16:39:24Z", 7.0],
    ["2019-07-26T16:45:06Z", 7.0],
    ["2019-07-26T16:50:48Z", 7.0],
    ["2019-07-26T16:56:30Z", 8.0],
    ["2019-07-26T17:02:12Z", 7.0],
    ["2019-07-26T17:13:36Z", 7.0],
    ["2019-07-26T17:19:18Z", 7.0]
]
```


## <a name='Range-of-Readings-History'></a> `GET /readings/date-range/` - Range of Readings History
[Back to top](#top)

Returns minimum and maximum dates where given parameters readings history contains any values.</p> <p>If several parameter IDs are provided, response will contain date range that includes all of their readings combined.</p> <p>If there are no readings for any of the parameters requested, empty object will be returned.

```
GET /readings/date-range/
```
*Required account permissions:* `viewer`

### Parameters - `Querystring`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `paramIds` | `String[]` | Comma-separated list of parameter IDs. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    "https://ocp.overvis.com/api/v1/readings/date-range/?paramIds=271,272" | \
        jq -C
```

### Success response example

```json
{
    "min": "2018-05-14T16:28:59.948845Z",
    "max": "2018-07-26T17:24:03.000383Z"
}
```


## <a name='Range-of-Readings-Values-for-a-Given-Period'></a> `POST /readings/min-max-range/` - Range of Readings Values for a Given Period
[Back to top](#top)

Returns minimum and maximum values of the requested parameters on a given time range.</p> <p>Returns a dictionary where key is a parameter ID and value contains object with min/max value and time.</p> <p>If the parameter has no readings during requested period, its ID won't be specified in the response at all.

```
POST /readings/min-max-range/
```
*Required account permissions:* `viewer`

### Parameters - `Body`
| Name     | Type       | Description                           |
|:---------|:-----------|:--------------------------------------|
| `paramIds` | `String[]` | List of parameter IDs. |
| `from` | `String` | Start date of the check range. |
| `till` | `String` | End date of the check range. |

### Examples

```bash
TOKEN=`curl -s -H "Content-Type: application/json" \
    -d '{"apiKey": "513cf747-eb5c-4d5f-931f-d22c9872c73c", "password": "DCdcSLmkoZkU5zGI9gpInDbo" }' \
    "https://ocp.overvis.com/api/v1/authenticate/" | \
        jq -r ".token"` && \
curl -s -S -H "Content-Type: application/json" -H "Authorization: token $TOKEN" \
    -d '{
        "paramIds": [271, 272],
        "from": "2019-04-01T21:52:13Z",
        "till": "2019-09-01T21:52:13Z"
    }' \
    "https://ocp.overvis.com/api/v1/readings/min-max-range/" | \
        jq -C
```

### Success response example

```json
{
    "271": {
        "minValueOn": "2019-07-26T17:24:02.923224Z",
        "minValue": 2018.0,
        "maxValueOn": "2019-07-20T12:45:39.449118Z",
        "maxValue": 65535.0
    },
    "272": {
        "minValueOn": "2019-05-14T21:39:33.890526Z",
        "minValue": 5.0,
        "maxValueOn": "2019-07-20T12:45:39.452660Z",
        "maxValue": 255.0
    }
}
```


