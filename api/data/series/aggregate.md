# Aggregate Processor

## Overview

Splits the interval into periods and calculates statistics for each periods.

Aggregation process:

1. Load detailed data within the specified `startDate` and `endDate` into each series separately. <br>`startDate` is inclusive and `endDate` is exclusive.
2. Split each series' time:value array into periods based on [alignment](period.md#alignment) parameter.
3. Discard periods if its start time is earlier than `startDate`.
4. Apply [statistical function](/api/data/aggregation.md) to values in each period and return a modified time:value array for each series where time is the period start time and value is the result of the statistical function.

## Fields

| **Name** | **Type**  | **Description**   |
|:---|:---|:---|
| type  | string        | [**Required**] [Statistical function](/api/data/aggregation.md) applied to detailed values in the period, such as `AVG`, `SUM`, or `COUNT`. |
| types | array          | Array of [statistical functions](/api/data/aggregation.md). Either `type` or `types` fields are required in each query. |
| period  | object     | [**Required**] [Period](#period). |
| interpolate  | object  | Generates aggregation periods in case of missing detailed samples using an [interpolation function](#interpolation), for example, `PREVIOUS` or `LINEAR`   |
| threshold    | object  | Object containing minimum and and maximum range for a `THRESHOLD_*` aggregator.  |
| calendar     | object  | Calendar settings for a `THRESHOLD_*` aggregator. |
| workingMinutes | object | Working minutes settings for a `THRESHOLD_*` aggregator.  |
| order         | number           | Change the order in which `aggregate` and `group` are executed, the higher the value of `order` the later in the sequence will it be executed.             |

### period

[Period](period.md) is a repeating time interval used to group detailed values within the period in order to apply a statistical function.

| **Name**  | **Type** | **Description** |
|:---|:---|:---|
| unit  | string | [Time unit](time-unit.md) such as `MINUTE`, `HOUR`, `DAY`. |
| count  | number | Number of time units contained in the period. |
| align | string | Alignment of the period's start/end time. Default: `CALENDAR`.|

Example: `{ "count": 1, "unit": "HOUR" }` or `{ "count": 15, "unit": "MINUTE", "align": "END_TIME" }`.

### calendar

| **Name** | **Type**| **Description** |
|:---|:---|:---|
| name | string | Custom calendar name |

Example: `{ "name": "au-nsw-calendar" }`.

### threshold

* Either `min` or `max` is **required**. 

| **Name** | **Type**| **Description** |
|:---|:---|:---|
| min  | number | Minimum threshold. |
| max  | number | Maximum threshold. |

Example: `{ "max": 80 }` or `{ "min": 100, "max": 150 }`.

### workingMinutes

| **Name** | **Type**| **Description** |
|:---|:---|:---|
| start | number | [**Required**] Working date start time, in minutes. If working day starts at 9:30 then `start` can be specified as `570` (9 * 60 + 30). |
| end   | number | [**Required**] Working date end time, in minutes.  |

## Examples

* 1-hour Average

```json
{
    "aggregate" : {
        "type": "AVG",
        "period": {"count": 1, "unit": "HOUR"}
    }
}
```

* 15-minute Average and Maximum

```json
{
    "aggregate": {
        "types": [ "AVG", "MAX" ],
        "period": { "count": 15, "unit": "MINUTE" }
    }
}
```

### Interpolation

By the default, if the period doesn't contain any detailed values, it will be excluded from the results.

The behaviour can be changed by specifying an interpolation function.
The interpolation function will add a missing period and calculate its value based on previous and next period values.

> Note that missing period values are interpolated from aggregate values of neighbouring periods and not from raw values.

#### Interpolation Fields

| **Name** | **Type**  | **Description**   |
|:---|:---|:---|
| type  | string | [**Required**] Interpolation [function](#interpolation-functions). |
| value | number | [**Required by `VALUE` function**] Constant number used to set value for the missing periods. |
| extend  | boolean | Add missing periods at the beginning and the end of the selection interval. Default: `false`. |

Values added by the `extend` setting are determined as follows:

* If the `VALUE {n}` interpolation function is specified, the `extend` option sets empty leading/trailing period values to equal `{n}`.
* Without the `VALUE {n}` function, the `extend` option adds missing periods at the beginning and end of the selection interval using `NEXT` and `PREVIOUS` interpolation functions.

#### Interpolation Functions

| **Type** | **Description** |
|:---|:---|
| NONE | No interpolation. Periods without any raw values are excluded from results. |
| PREVIOUS | Set value for the period based on the previous period's value. |
| NEXT | Set value for the period based on the next period's value. |
| LINEAR | Calculate period value using linear interpolation between previous and next period values. |
| VALUE| Set value for the period to a specific number. |

#### Examples

**PREVIOUS**:

```json
"aggregate" : {
   "type": "AVG",
   "period": {"count": 1, "unit": "HOUR"},
   "interpolate" : {
	  "type": "PREVIOUS"
   }
}
```

**LINEAR**:

```json
"aggregate" : {
   "type": "AVG",
   "period": {"count": 1, "unit": "HOUR"},
   "interpolate" : {
	  "type": "LINEAR"
   }
}
```

**VALUE WITH EXTEND**:

```json
"aggregate" : {
   "type": "SUM",
   "period": {"count": 1, "unit": "HOUR"},
   "interpolate" : {
	  "type": "VALUE",
	  "value": 0,
	  "extend": true
   }
}
```

[Additional Interpolation Examples](examples/query-aggr-interpolation.md)

[Chartlab Examples](https://apps.axibase.com/chartlab/d8c03f11/3/)

![Interpolation Example](aggregate_interpolate.png)
