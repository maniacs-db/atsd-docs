# Freemarker Expressions

Freemarker expressions are supported in portal creation.

#### Freemarker Functions

##### getTags

```javascript
getTags('metric', 'entity', 'tagKey'[, hours])
```

Returns a string collection.

Tag values for metric, entity and tagKey.
[, hours] is an optional parameter, which specifies the time interval (in hours) for searching unique tag values.
Default interval is 24 hours.

```
<#assign cpus = getTags("nmon.cpu.idle%", "${entity}", "id") >      
<#list cpus as id>
    [series]
        label = ${id}
        entity = ${entity}
        metric = nmon.cpu.busy%
        #type = avg
        #interval = 5 minute
        [tag]
            name = id
            value = ${id}
</#list>
```

##### tag

```javascript
tag('entity', 'tagKey')
```

Returns a string.

Entity tag value.

```javascript
tag('nurswgvml007', 'location')
```

##### groupTag

```javascript
groupTag('entity', 'tagKey')
```

Returns a string collection.

Returns collection of tag values for tagKey of all entity groups to which the entity belongs to.

```javascript
groupTag('nurswgvml007', 'cpu_busy_avg_15_min')
```

##### getMetrics

```javascript
getMetrics('entity')
```

String collection

Returns collected metrics for a particular entity.

```
<#assign metrics = getMetrics("${entity}") >      
<#list metrics as metric>
    [series]
        label = ${metric}
        entity = ${entity}
        metric = ${metric}
        statistic = avg
        period = 10 minute
</#list
```

<script src="http://gist.github.com/de95557d743b0129f519.js"></script><noscript>View the code on <a href="https://gist.github.com/de95557d743b0129f519">Gist</a>.</noscript>

##### isMetric

```javascript
isMetric('metric')
```

Boolean

Returns true if a metric exists.

<script src="http://gist.github.com/aec1288aeeffed9048bc.js"></script><noscript>View the code on <a href="https://gist.github.com/aec1288aeeffed9048bc">Gist</a>.</noscript>

##### isMetricCollected

```javascript
isMetricCollected('metric', 'entity')
```

Boolean

Returns true if there is some data for metric and entity inserted in last 24 hours.

<script src="http://gist.github.com/112f84798db0eded1bea.js"></script><noscript>View the code on <a href="https://gist.github.com/112f84798db0eded1bea">Gist</a>.</noscript>

##### getProperty

```javascript
getProperty('entity', 'property_type', 'tagKey')
```

Returns a string collection.

Retrieve a collection of property objects for specified entity, property type and tag.

<script src="http://gist.github.com/51ef46f7fde74b004957.js"></script><noscript>View the code on <a href="https://gist.github.com/51ef46f7fde74b004957">Gist</a>.</noscript>

##### getSeriesProperties

```javascript
getSeriesProperties("{entity}", "{property_type}")
```

Returns property objects for specified entity and property type.

Retrieve a collection of property objects for specified entity and property type.
If no entity is specified, then retrieves a collection of property objects for all entities with the specified property type.

<script src="http://gist.github.com/ab7af1d8d030bab23cb8.js"></script><noscript>View the code on <a href="https://gist.github.com/ab7af1d8d030bab23cb8">Gist</a>.</noscript>

##### getTagMaps

```javascript
getTagMaps('metric', 'entity'[, hours])
```

Returns collection of maps(string, string).

Retrieve a collection of unique tag maps for metric and entity.
[, hours] is an optional parameter, which specifies the time interval (in hours) for searching unique tag values.
Default interval is 24 hours.

<script src="http://gist.github.com/6b0777fa7ab3ea8796de.js"></script><noscript>View the code on <a href="https://gist.github.com/6b0777fa7ab3ea8796de">Gist</a>.</noscript>

##### atsd_last

```javascript
gatsd_last('entity', 'metric', 'tag1=v1,tag2=v2')
```

Returns double.

Retrieves the last value for time series or null.

<script src="http://gist.github.com/27479e80aefdda287a3f.js"></script><noscript>View the code on <a href="https://gist.github.com/27479e80aefdda287a3f">Gist</a>.</noscript>

##### memberOf

```javascript
memberOf('entity', 'group1', …, 'groupN')
```

Boolean

Returns true if an entity belongs to any of specified entity groups.

<script src="http://gist.github.com/afe3b5f93af4fdf49835.js"></script><noscript>View the code on <a href="https://gist.github.com/afe3b5f93af4fdf49835">Gist</a>.</noscript>

##### memberOfAll

```javascript
memberOfAll('entity', 'group1', …, 'groupN')
```

Boolean

Returns true if an entity belongs to all of the entity groups.

<script src="http://gist.github.com/87936cc6e009b2620308.js"></script><noscript>View the code on <a href="https://gist.github.com/87936cc6e009b2620308">Gist</a>.</noscript>

##### lastInsertTime & lastInsertDate

```javascript
lastInsertTime('entity'[, ‘metric’])
```

```javascript
lastInsertDate('entity'[, ‘metric’])
```

Double

Returns the last insert time for the entity or entity/metric combination in milliseconds (Time) or ISO format (Date). Metric is an optional parameter.

<script src="http://gist.github.com/heinrichvk/88c6545dbb88041bf6d3.js"></script><noscript>View the code on <a href="https://gist.github.com/heinrichvk/88c6545dbb88041bf6d3">Gist</a>.</noscript>

<script src="http://gist.github.com/heinrichvk/9fc870336a42d1ecea3d.js"></script><noscript>View the code on <a href="https://gist.github.com/heinrichvk/9fc870336a42d1ecea3d">Gist</a>.</noscript>

##### getEntitiesForGroup:

```javascript
getEntitiesForGroup('group')
```

```javascript
getEntitiesForGroup('group', 'hours')
```

Returns a string collection.

Find all entities in a particular entity group, useful when building portals that compare entities from the same entity group.
The method returns group member that have inserted data over the last N hours.
If hours is not specified or non-positive, all group members are returned.

<script src="http://gist.github.com/982c0cb9c0ea6c20f478.js"></script><noscript>View the code on <a href="https://gist.github.com/982c0cb9c0ea6c20f478">Gist</a>.</noscript>

##### getEntitiesForTags:

```javascript
getEntitiesForTags(expression)
```

Returns a string collection.

Find find entities by expression, based on tags.

<script src="http://gist.github.com/4cd99c834ad9e63258bc.js"></script><noscript>View the code on <a href="https://gist.github.com/4cd99c834ad9e63258bc">Gist</a>.</noscript>

In the first example we are searching for entities with two tags, the required value can be specified directly in the browser:

http://atsd.com/portal/1.xhtml?app=> value1&dc=> value2

All entities where app tag is equal to > value1 and dc tag is equal to > value2 will be loaded into the portal.

In the second example we are searching for entities with a specific application tag, the required value can be specified directly in the browser:

http://atsd.com/portal/1.xhtml?application=> value

All entities where the application tag is equal to > value will be loaded into the portal.

A single line of freemarker code can be used to easily customize the results of the portal by searching for entity tags rather that specific entities giving extensive possibilities to create flexible portals.

The freemarker search can be for any combination of tags, for example: > application, > data center and > function, only entities that have all three specified tags will be loaded into the portal.

In the response, freemarker [series] are substituted with the matching entities, creating [series] for each of them.

##### Example output of a freemarker [series]:

```
[configuration]
title = CPU Used Portal
height-units = 1
width-units = 1
 
[group]
 
[widget]
type = chart
title = CPU Used
time-span = 1 hour
max-range = 100
 
[series]
label = host0987
entity = host0987
metric = cpu_used
 
[series]
label = host1040
entity = host1040
metric = cpu_used
 
[series]
label = host1299
entity = host1299
metric = cpu_used
 
[series]
label = host1786
entity = host1786
metric = cpu_used
```

Advanced functions and aggregations can be added to freemarker portals to enhance the resulting data prior to loading it into the portal. Below are two examples.

##### The freemarker [series] are given an alias, that can then be used to sum the loaded data as in the example below:

<script src="http://gist.github.com/e6dfcaa018b1bfc5d837.js"></script><noscript>View the code on <a href="https://gist.github.com/e6dfcaa018b1bfc5d837">Gist</a>.</noscript>

##### The freemarker [series] data can be aggregated by ATSD prior to loading into the portal:

<script src="http://gist.github.com/31f5e1115907cd15faec.js"></script><noscript>View the code on <a href="https://gist.github.com/31f5e1115907cd15faec">Gist</a>.</noscript>

#### Freemarker Expressions Summary Table

| Name | Returns | Description | 
| --- | --- | --- | 
|  <p>`atsd_last('entity', 'metric', 'tag1=v1,tag2=v2')`</p>  |  <p>Double</p>  |  <p>Last value for time series or null</p>  | 
|  <p>`groupTag('entity', 'tagKey')`</p>  |  <p>string collection</p>  |  <p>collection of tag values for tagKey of all entity groups an entity belongs to</p>  | 
|  <p>`tag('entity', 'tagKey')`</p>  |  <p>string</p>  |  <p>Entity tag value</p>  | 
|  <p>`memberOf('entity', 'group1', ..., 'groupN')`</p>  |  <p>boolean</p>  |  <p>returns true if an entity belongs to any of specified entity groups</p>  | 
|  <p>`memberOfAll('entity', 'group1', ..., 'groupN')`</p>  |  <p>boolean</p>  |  <p>returns true if an entity belongs to all of the entity groups</p>  | 
|  <p>`list('value' [, delimiter])`</p>  |  <p>string collection</p>  |  <p>splits a string by a delimeter. Default delimiter is comma character</p>  | 
|  <p>`getTags('metric', 'entity', 'tagKey'[, hours])`</p>  |  <p>string collection</p>  |  <p>tag values for metric, entity and tagKey.</p>  <p>[, hours] is an optional parameter, which specifies the time interval (in hours) for searching unique tag values.</p>  <p>Default interval is 24 hours.</p>  | 
|  <p>`getEntitiesForTags(expression)`</p>  |  <p>string collection</p>  |  <p>find entities by expression</p>  | 
|  <p>`getEntitiesForGroup("group")`</p>  |  <p>string collection</p>  |  <p>find all entities in a particular entity group, useful when building portals that compare entities from the same entity group</p>  | 
|  <p>`getEntitiesForGroup(groupName, hours)`</p>  |  <p>string collection</p>  |  <p>find all entities in a particular entity group, useful when building portals that compare entities from the same entity group.</p>  <p>The method returns group member that have inserted data over the last N hours.</p>  <p>If hours is not specified or non-positive, all group members are returned.</p>  | 
|  <p>`getMetrics('entity')`</p>  |  <p>string collection</p>  |  <p>Retrieve all collected metrics for a particular entity.</p>  | 
|  <p>`isMetric('metric')`</p>  |  <p>boolean</p>  |  <p>returns true if a metric exists</p>  | 
|  <p>`isMetricCollected('metric', 'entity')`</p>  |  <p>boolean</p>  |  <p>returns true if there is some data for metric and entity inserted in last 24 hours</p>  | 
|  <p>`hasMetric('entity', 'metric' [,hours])`</p>  |  <p>boolean</p>  |  <p>Executes query for Last Insert Cache table and returns true if the entity collects specified metric, regardless of tags.</p>  <p>If optional hours argument is specified, only rows inserted for the last N hours are evaluated.</p>  | 
|  <p>`getTagMaps('metric', 'entity'[, hours])`</p>  |  <p>collection of maps(string, string)</p>  |  <p>collection of unique tag maps for metric and entity.</p>  <p>[, hours] is an optional parameter, which specifies the time interval (in hours) for searching unique tag values.</p>  <p>Default interval is 24 hours.</p>  | 
|  <p>`getProperty('entity', 'property_type', 'tagKey')`</p>  |  <p>string collection</p>  |  <p>Retrieve a collection of property objects for specified entity, property type and tag.</p>  | 
|  <p>`getSeriesProperties("{entity}", "{property_type}")`</p>  |  <p>property objects for specified entity and property type</p>  |  <p>Retrieve a collection of property objects for specified entity and property type.</p>  <p>If no entity is specified, then retrieves a collection of property objects for all entities with the specified property type.</p>  | 
|  <p>`atsd_values(entity, metric, tags, type, interval, shift, duration)`</p>  |  <p>Aggregator object</p>  |  <p>see tables below</p>  | 
|  <p>`lastInsertTime('entity'[, ‘metric’])`</p>  |  <p>Double</p>  |  <p>Returns last insert time for the entity or entity/metric combination in milliseconds. Metric is an optional parameter.</p>  | 
|  <p>`lastInsertDate('entity'[, ‘metric’])`</p>  |  <p>Double</p>  |  <p>Returns last insert date for the entity or entity/metric combination in ISO format. Metric is an optional parameter.</p>  | 


#### atsd_values parameters

| Name | Description | 
| --- | --- | 
|  <p>entity</p>  |  <p>Entity</p>  | 
|  <p>metric</p>  |  <p>Metric</p>  | 
|  <p>tags</p>  |  <p>Tags</p>  | 
|  <p>type</p>  |  <p>Aggregation Type</p>  | 
|  <p>interval</p>  |  <p>Aggregation Interval</p>  | 
|  <p>shift</p>  |  <p>Interval: endTime = now – shift</p>  | 
|  <p>duration</p>  |  <p>Selection interval: startTime = endTime – duration</p>  | 


#### atsd_values parameters

| Name | Returns | 
| --- | --- | 
|  <p>min()</p>  |  <p>Double</p>  | 
|  <p>max()</p>  |  <p>Double</p>  | 
|  <p>sumOf()</p>  |  <p>Double</p>  | 
|  <p>average()</p>  |  <p>Double</p>  | 
|  <p>countOf()</p>  |  <p>Integer</p>  | 
|  <p>asList()</p>  |  <p>Double collection</p>  | 


