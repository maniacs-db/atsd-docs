# Metric: List

## Description 

Retrieve a list of metrics matching the specified filter conditions.

## Request

| **Method** | **Path** | 
|:---|:---|---:|
| GET | `/api/v1/metrics` |

### Query Parameters 

|**Name**|**Type**|**Description**|
|:---|:---|:---|
| expression |string|Include metrics that match an [expression](../expression.md) filter. Use the `name` variable for metric name. The wildcard `*` is supported.|
| minInsertDate |string|Include metrics with `lastInsertDate` equal or greater than `minInsertDate`.<br>`minInsertDate` can be specified in ISO format or using [endtime](/end-time-syntax.md) syntax.|
| maxInsertDate |string|Include metrics with `lastInsertDate` less than `maxInsertDate`.<br>`maxInsertDate` can be specified in ISO format or using [endtime](/end-time-syntax.md) syntax.|
| limit |integer|Maximum number of metrics to retrieve, ordered by name.|
| tags |string|Comma-separated list of metric tag names to be displayed in the response.<br>For example, `tags=OS,location`<br>Specify `tags=*` to print all metric tags.|

## Response 

### Fields

| **Name** | **Type** | **Description** |
|:---|:---|:---|
|name| string | Metric name.|
|label| string | Metric label.|
|description | string | Metric description.|
|tags| object | An object containing tags as names and values.<br>For example, `"tags": {"table": "axibase-collector"}`|
|dataType| string | [Data Type](#data-types).|
|interpolate| string | Interpolation mode: `LINEAR` or `PREVIOUS`. <br>Used in SQL `WITH INTERPOLATE` clause when interpolation mode is set to `AUTO`, for example, `WITH INTERPOLATE(1 MINUTE, AUTO)`. |
|timeZone| string | Time Zone ID, for example `America/New_York` or `EST`.<br>Refer to [Java Time Zone](../../network/timezone-list.md) table for a list of supported Time Zone IDs.<br>The timezone is applied by date-formatting functions to return local time in metric-specific timezone.|
|timePrecision| string | Time precision: SECONDS or MILLISECONDS.|
|enabled| boolean | Enabled status. Incoming data is discarded for disabled metrics.|
|persistent | boolean | Persistence status. Non-persistent metrics are not stored in the database and are only processed by the rule engine.|
|filter | string | Persistence filter [expression](../expression.md). Discards series that do not match this filter.|
|lastInsertDate| string | Last time a value was received for this metric by any series. ISO date.|
|retentionDays| integer | Number of days to retain values for this metric in the database.|
|versioned| boolean | If set to true, enables versioning for the specified metric. <br>When metrics are versioned, the database retains the history of series value changes for the same timestamp along with `version_source` and `version_status`.|
|minValue| double | Minimum value for [Invalid Action](#invalid-actions) trigger.|
|maxValue| double | Maximum value for [Invalid Action](#invalid-actions) trigger.|
|invalidAction | string | [Invalid Action](#invalid-actions) type.|

### Data Types

|**Type**|**Storage Size, bytes**|
|:---|:---|
|SHORT|2|
|INTEGER|4|
|LONG|8|
|FLOAT|4|
|DOUBLE|8|
|DECIMAL|variable|

Default data type for new metrics, when auto-created, is **float**. 

### Invalid Actions

 Invalid Action is triggered if the received series value is less than the Minimum value, or if it's greater than the Maximum value.

|**Action**|**Description**|
|:---|:---|
|NONE|Retain value as is.|
|DISCARD|Don't process the received value, discard it.|
|TRANSFORM|Set value to `min_value` or `max_value`, if value is outside of range.|
|RAISE_ERROR|Log ERROR event in the database log.|

### Interpolate

|**Type**|
|:---|
|LINEAR|
|PREVIOUS|

### Time Precision

|**Precision**|
|:---|
|MILLISECONDS|
|SECONDS|

### Errors

|  Status Code  |  Description  |
|:---------------|:---------------|
| 500 |TypeMismatchException: <br>Failed to convert value of type 'java.lang.String' to required type 'com.axibase.tsd.model.TimeFormat';|
| 500 |TypeMismatchException: <br>Failed to convert value of type 'java.lang.String' to required type 'int'|

## Example 1 

### Request

#### URI

```elm
https://atsd_host:8443/api/v1/metrics?limit=2
```

#### Payload

None.

#### curl

```elm
curl https://atsd_host:8443/api/v1/metrics?limit=2 \
  --insecure --verbose --user {username}:{password} \
  --request GET
```

### Response

```json
[
  {
    "name": "m-vers",
    "enabled": true,
    "dataType": "FLOAT",
    "persistent": true,
    "timePrecision": "MILLISECONDS",
    "retentionDays": 0,
    "invalidAction": "NONE",
    "lastInsertDate": "2016-05-19T00:15:02.000Z",
    "versioned": true,
	"interpolate":"LINEAR"
  },
  {
    "name": "temperature",
    "enabled": true,
    "dataType": "FLOAT",
    "persistent": true,
    "timePrecision": "MILLISECONDS",
    "retentionDays": 0,
    "invalidAction": "NONE",
    "lastInsertDate": "2016-05-18T00:35:12.000Z",
    "versioned": false,
	"interpolate":"LINEAR",
	"timeZone":"America/New_York"
  }
]
```

## Example 2 

Expression text:

```text
name!="" or tags.keyName!="" or label!="" or description!="" or enabled=true or persistent=true or persistenceFilter!="" or retentionDays=0 or dataType="FLOAT" or timePrecision="MILLISECONDS" or versioning=false and invalidAction="NONE" or timeZone="" or interpolate="LINEAR" or counter=false
```

### Request

#### URI

```elm
https://atsd_host:8443/api/v1/metrics?tags=*&expression=versioning=true%20and%20retentionDays%3E0%20and%20dataType=%22FLOAT%22
```

#### Payload

None.

#### curl

```elm
curl https://atsd_host:8443/api/v1/metrics?expression=versioning=true%20and%20retentionDays%3E0%20and%20dataType=%22FLOAT%22 \
  --insecure --verbose --user {username}:{password} \
  --request GET
```

### Response

```json
[
  {
    "name": "metric",
    "enabled": true,
    "dataType": "FLOAT",
    "counter": false,
    "persistent": true,
    "timePrecision": "MILLISECONDS",
    "retentionDays": 3,
    "invalidAction": "NONE",
    "lastInsertDate": "2016-10-28T08:18:17.218Z",
    "versioned": true,
    "interpolate": "LINEAR"
  }
]
```

## Additional Examples

* [List metrics by name](examples/list-metrics-by-name.md)
* [List metrics by name and tag](examples/list-metrics-by-name-and-tag.md)
* [List metrics with tag `table`](examples/list-metrics-with-tag-table.md)
* [List metrics by maxInsertDate](examples/list-metrics-by-maxinsertdate.md)
* [List metrics by minInsertDate](examples/list-metrics-by-mininsertdate.md)
