# elasticsearch

es version: 2.2.0
ik version: 1.8.1

Sample Query
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

Check Analyzer
curl 'http://localhost:9200/_analyze?analyzer=ik_smart&pretty=true' -d '
{
  "text":"車站"
}'

Delete index
curl -XDELETE http://localhost:9200/locsug-index/

Create new index
curl -XPUT http://localhost:9200/locsug-index/

Set index mapping
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
