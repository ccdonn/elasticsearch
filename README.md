# elasticsearch

es version: 2.2.0  
ik version: 1.8.1

## Term Concept
|SQL DB|Elasticsearch|
|---|---|
|Database|Indeices|
|Table|Type|
|Row|Document|
|Column|Field|

## Search Query
### Sample Query-1
```
curl -XGET localhost:9200/loc-index/loc/97c3fa1
```

### Sample Query-2
```
curl -XGET localhost:9200/loc-index/loc/_search -d '
{
  "query": {
    "bool": {
      "must": [
        {
          "term": { "id.raw":"97c3fa1" }
        },
        {
          "range": { "expireTime": { "gte":"now"} }
        },
      ],
      "should": [
        {
          "term": { "areacode.id":123 }
        },
        ...
      ]
    }
  },
  "sort": [
    {
      "createTime": { "order":"desc" },
      "_score"
    }
  ]
}'
```

### Sample Query-#
```
curl -XGET localhost:9200/locsug-index/_search -d '
{ 
  "query":{
    "bool":{
      "should":[
        {"multi_match":{"query":"車站", "fields":["name.*"], "analyzer":"ik_smart"}},
        {"multi_match":{"query":"車站", "fields":["cityName.*"], "analyzer": "ik_smart"}}
      ]
    }
  }
}'
```

### Insert Document
```
curl -XPOST localhost:9200/review-index/review/93548c1b/_create -d '
{
  "id": "93548c1b",
  "value": 10
}'
```

### Upsert Document 
```
curl -XPOST localhost:9200/review-index/review/93548c1b/_update -d '
{
  "doc": {
    "id": "93548c1b",
    "value": 33
  },
  "doc_as_upsert": true
}'
```

### Partial Update Document
```
curl -XPOST localhost:9200/review-index/review/93548c1b/_update -d '
{
  "doc": {
    "category": {
      "iconUrl":"default.png"
    }
  }
}'
```

### Delete Document
```
curl -XDELETE localhost:9200/review-index/review/935481b
```

## Index

### Create index
```
curl -XPUT http://localhost:9200/locsug-index/
```

### Create new index w/ specific properties
```
curl -XPUT localhost:9200/coupon-index -d '
{
  "mappings": {
    "coupon": {
      "properties": {
        "expireTime": {"type":"date"},
        "activeTime": {"type":"date"},
        "id":{
          "type":"string",
          "fields":{
            "raw":{
              "type":"string",
              "index":"not_analyzed",
              "ignore_above":256
            }
          }
        },
        "merchant":{
          "type":"string",
          "fields":{
            "raw":{
              "type":"string",
              "index":"not_analyzed",
              "ignore_above":256
            }
          }
        }
      }
    }
  }
}'
```

### Properties with Format
```
"properties": {
    "updated_at": {
         "type": "date",
         "format": "yyyy-MM-dd HH:mm:ss"
     }
 }
```

### Delete index
```
curl -XDELETE http://localhost:9200/locsug-index/
```

### Set index mapping
```
curl -XPOST http://localhost:9200/locsug-index/locsug/_mapping -d '
{
  "locsug": {
    "properties": {
      "name": {
        "type": "object",
        "properties": {
          "zh-TW":{
            "type": "string",
            "analyzer": "ik",
            "search_analyzer": "ik_smart",
            "boost": 8
          },"ja":{
            "type": "string",
            "analyzer": "ik",
            "search_analyzer": "ik_smart",
            "boost": 4
          },"en":{
            "type": "string",
            "analyzer": "ik",
            "search_analyzer": "ik_smart",
            "boost": 2
          }
        }
      }
    }
  }
}'
```

### Check Analyzer
```
curl 'http://localhost:9200/_analyze?analyzer=ik_smart&pretty=true' -d '
{
  "text":"車站"
}'
```



