# Fetch Metrics by Limit Parameter 
Fetch first three metrics, ordered by name.
## Request
### URI
```
https://atsd_host:8443/api/v1/metrics?limit=3
```
## Response
```json
[
   {
      "name":"api_command_malformed_per_second",
      "enabled":true,
      "dataType":"FLOAT",
      "counter":false,
      "persistent":true,
      "timePrecision":"MILLISECONDS",
      "retentionInterval":0,
      "invalidAction":"NONE",
      "lastInsertTime":1463129522969,
      "versioned":false
   },
   {
      "name":"asd",
      "enabled":true,
      "dataType":"FLOAT",
      "counter":false,
      "description":"it is description",
      "persistent":true,
      "timePrecision":"MILLISECONDS",
      "retentionInterval":2,
      "minValue":21.0,
      "maxValue":125.0,
      "invalidAction":"RAISE_ERROR",
      "lastInsertTime":1462427358127,
      "filter":"filter",
      "versioned":false
   },
   {
      "name":"df.disk_size",
      "enabled":true,
      "dataType":"FLOAT",
      "counter":false,
      "persistent":true,
      "timePrecision":"MILLISECONDS",
      "retentionInterval":0,
      "invalidAction":"NONE",
      "lastInsertTime":1463129525000,
      "versioned":false
   }
]
```
