# Entity Tags for entityGroup

## Request
### URI
```elm
POST https://atsd_host:8443/api/v1/properties/query
```
### Payload
```json
[
    {
        "entityGroup": "nur-entities-name",
        "type": "$entity_tags"
    }
]
```

## Response

```json
[
    {
        "type": "$entity_tags",
        "entity": "nurswgvml003",
        "key": {},
        "tags": {
            "app": "Shared NFS/CIFS disk, ntp server",
            "app-test": "1",
            "ip": "10.102.0.2",
            "os": "Linux"
        },
        "date": "2015-09-08T09:37:13Z"
    },
    {
        "type": "$entity_tags",
        "entity": "nurswgvml006",
        "key": {},
        "tags": {
            "app": "Hadoop/HBASE",
            "ip": "10.102.0.5",
            "os": "Linux"
        },
        "date": "2015-09-08T09:37:13Z"
    },
    {
        "type": "$entity_tags",
        "entity": "nurswgvml007",
        "key": {},
        "tags": {
            "alias": "007",
            "app": "ATSD",
            "ip": "10.102.0.6",
            "loc_area": "dc2",
            "loc_code": "nur,nur",
            "os": "Linux"
        },
        "date": "2015-09-08T09:37:13Z"
    }
]
```
