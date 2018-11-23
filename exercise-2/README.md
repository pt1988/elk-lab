#### Exercise2 Elasticsearch

##### 1. การใช้งาน REST API

###### 1.1) show elasticsearch information
```
curl -XGET 127.0.0.1:9200/
```

##### 2. การสใช้งาน CAT API
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html)

###### 2.1) แสดงรายการคำสั่งของ CAT API 
```
curl -XGET 127.0.0.1:9200/_cat
```

###### 2.2) คำสั่งแสดงสถานะของ Elasticsearch cluster 
```
curl 127.0.0.1:9200/_cat/health?v
```

###### 2.3) คำสั่งแสดงจำนวน node ภายใน cluster 
```
curl 127.0.0.1:9200/_cat/nodes?v
```

###### 2.4) คำสั่งแสดงรายการ indices ภายใน cluster
```
curl 127.0.0.1:9200/_cat/indices?v
```

###### 2.5) คำสั่งแสดงรายการ shades ภายใน cluster
```
curl 127.0.0.1:9200/_cat/shards?v
```

###### 2.6) คำสั่งแสดง template ภายใน cluster
```
curl 127.0.0.1:9200/_cat/templates?v
```

###### 2.6-1) ทอลองคิวรี template ชื่อ logstash-index-template 
```
curl 127.0.0.1:9200/_template/logstash-index-template?pretty=true
```


##### 3. Search API
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-search.html)

search command pattern
```
curl 127.0.0.1:9200/[index_pattern]/[type_name]/_search
```

###### 3.1) search for all type
```
curl 127.0.0.1:9200/logstash*/_search?pretty=true
```

###### 3.2) search specific type
```
curl 127.0.0.1:9200/logstash*/doc,user/_search?pretty=true
```

###### 3.3) URI search 
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-uri-request.html)

```
curl 127.0.0.1:9200/logstash*/doc,user/_search?q=status:200
```

###### 3.4) Request Body Search 
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)

```
curl 127.0.0.1:9200/logstash*/_search?pretty=true --header "Content-Type: application/json" \
-d '{
    "query" : {
        "term" : { "status" : "200" }
    }
}'
```


##### 4. Aggregation Framework
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)


###### 4.1 Metric aggregation
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics.html)

This example show metric aggregation with [cadinality](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-cardinality-aggregation.html). (Uniq Count)

```
curl 127.0.0.1:9200/logstash*/_search?pretty=true --header "Content-Type: application/json" \
-d '{
    "aggs" : {
	"type_count" : {
        	"cardinality" : { 
			"field" : "status.keyword"
		}
	}
    }
}'
```

###### 4.2) Bucket aggregation
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-terms-aggregation.html)

This example show bucket aggregation using term.
```
curl 127.0.0.1:9200/logstash*/_search?pretty=true --header "Content-Type: application/json" \
-d '{
    "aggs" : {
	"logstash*" : {
        	"terms" : { 
			"field" : "status.keyword",
			"size" : 10
		}
	}
    }
}'
```


###### 4.3) Pipeline aggregation
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-pipeline.html)


###### 4.4) Matrix aggregation
[reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-matrix.html#search-aggregations-matrix)
 
