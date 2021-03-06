# `series` Command

## Description

Insert timestamped numeric or text values for the specified entity, series tags, and one or multiple metrics. 

New entities, metrics, and tags will be created automatically.

New metrics will be initialized with `float` data type by default.

To insert metric samples with another data type, create or update metric properties using [Meta API](/api/meta/metric/update.md) or the user interface.

## Syntax

```css
series e:{entity} m:{metric-1}={number} m:{metric-2}={number} x:{metric-3}={text} t:{tag-1}={val-1} s:{seconds} 
```

### Fields

| **Field** | **Type** | **Description** |
|:---|:---|:---|
| e         | string          | **[Required]** Entity name. |
| m         | string:number   | **[Required]** Metric name and numeric value. _Multiple._ |
| x         | string:string   | **[Required]** Metric name and text value. _Multiple._ |
| d         | string          | Time in ISO format. | 
| ms        | integer         | Time in UNIX milliseconds. | 
| s         | integer         | Time in UNIX seconds. | 
| t         | string:string   | Tag name and text value. _Multiple._ |

> At least one numeric metric `m:` or text metric `x:` is required.

> If the numeric observation was not specified for the text value with the same metric name, it is set to `NaN` (not a number).

> If time fields `d, s, and ms` are omitted, the values are inserted with the current server time.

### ABNF Syntax

Rules inherited from [Base ABNF](base-abnf.md).

```properties
  ; entity and at least one metric is required
command = "series" MSP entity 1*(MSP [metric-numeric / metric-text]) *(MSP tag) [MSP time]
entity = "e:" NAME
metric-numeric = "m:" NAME "=" NUMBER
metric-text    = "x:" NAME "=" VALUE
tag = "t:" NAME "=" VALUE
time = time-millisecond / time-second / time-iso
time-millisecond = "ms:" POSITIVE_INTEGER
time-second = "s:" POSITIVE_INTEGER
time-iso = "d:" ISO_DATE
```

## Examples

```ls
series e:server001 m:cpu_used=72.0 m:memory_used=94.5 s:1425482080
```

```ls
series e:server001 m:cpu_used=72.0 m:memory_used=94.5 ms:1425482080000
```

```ls
series e:server001 m:cpu_used=72.0 m:memory_used=94.5 d:2016-09-04T12:40:00Z
```

```ls
series e:server001 m:disk_used_percent=20.5 m:disk_size_mb=10240 t:mount_point=/ t:disk_name=/sda1
```

```ls
series d:2016-10-13T08:15:00Z e:sensor-1 m:temperature=24.4 x:temperature="Provisional"
```

```ls
series d:2016-10-13T10:30:00Z e:sensor-1 x:status="Shutdown by adm-user, RFC-5434"
```

```ls
series d:2016-10-13T08:45:00Z e:sensor-1 m:temperature=NaN
```


## Number Representation

* The numeric value can be a real number or a `NaN` (Not a Number).
* The string representation of an inserted number consists of an optional sign, '+' ('\u002B') or '-' ('\u002D'), followed by a sequence of zero or more decimal digits ("the integer"), optionally followed by a fraction, optionally followed by an exponent.
* The exponent consists of the character 'e' ('\u0065') or 'E' ('\u0045') followed by an optional sign, '+' ('\u002B') or '-' ('\u002D'), followed by one or more decimal digits.
* The fraction consists of a decimal point followed by zero or more decimal digits. The string must contain at least one digit in either the integer or the fraction. 
* The number formed by the sign, the integer, and the fraction is referred to as the [**significand**](https://en.wikipedia.org/wiki/Significand).
* The **significand** value stripped from trailing zeros should be within Long.MAX_VALUE `9223372036854775807` and Long.MIN_VALUE  `-9223372036854775808` (19 digits). Otherwise the database will throw an **llegalArgumentException: BigDecimal significand overflows the long type** for decimal metrics or round the value for non-decimal metrics. For example, significand for `1.1212121212121212121212121212121212121212121` contains 44 digits and will be rounded to `1.121212121212121212` if inserted for a non-decimal metric.

## Series Tags, Text Value, Messages

The difference between series tags and text values inserted with the `x:` field is that tag names/values are converted to unique identifiers when stored.

Series tags are part of each series composite primary key, whereas the text value is not.

Since the total number of unique tag value identifiers is [limited](README.md#schema) to `16,777,215`, series tag values are not well suited for values with high cardinality such as random values or continuously incrementing values (time, counters).

The text value, on the other hand, is stored `as is`, without converting it to an identifier. It can be used as an annotation, or order to describe a numeric observation without changing it's primary key. 



```ls
series d:2016-10-13T08:00:00Z e:sensor-1 m:temperature=20.3
series d:2016-10-13T08:15:00Z e:sensor-1 m:temperature=24.4 x:temperature="Provisional"
```

In this example, temperature reading at `2016-10-13T08:15:00Z` is characterized as `Provisional`.

The text value can also be used to record observations for series that contain only text values in which case their numeric values are set to `NaN` (not a number).

```ls
series d:2016-10-13T10:30:00Z e:sensor-1 x:status="Shutdown by adm-user, RFC-5434"
```

Unlike [message](message.md) commands, series text values are available for SQL querying using the `text` column.

```sql
SELECT entity, datetime, value, text 
  FROM atsd_series 
WHERE metric IN ('temperature', 'status') AND datetime >= '2016-10-13T08:00:00Z'
```

```ls
| entity   | datetime             | value | text                           | 
|----------|----------------------|-------|--------------------------------| 
| sensor-1 | 2016-10-13T08:00:00Z | 20.3  | null                           | 
| sensor-1 | 2016-10-13T08:15:00Z | 24.4  | Provisional                    | 
| sensor-1 | 2016-10-13T10:30:00Z | NaN   | Shutdown by adm-user, RFC-5434 | 
```

## Versioning


[Versioning](http://axibase.com/products/axibase-time-series-database/data-model/versioning/) enables tracking of time-series value changes for the purpose of establishing an audit trail and traceable data reconciliation.

Versioning is disabled by default. It can be enabled for particular metrics by applying the Versioning check box on the Metric Editor page.

To insert versioning fields use reserved tags:

* `$version_source`
* `$version_status`

```ls
series s:1425482080 e:e-vers m:m-vers=13 t:$version_status=OK t:$version_source=collector-1
```

If the metrics are versioned,  `$version_source` and `$version_status` tags will be ignored as regular series tags and instead will be converted to corresponding versioning tags.
