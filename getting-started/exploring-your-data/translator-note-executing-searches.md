# 译者注 - 嵌套布尔查询

在[1.5.3执行查询](/getting-started/exploring-your-data/executing-searches.md)章节中提到了布尔查询支持嵌套组合，但是并未给出具体的样例。笔者做了如下实验：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "should": [
        { "match": { "age": 40 } },
        {"bool":{"must": [
          { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
          ]}}
      ]
    }
  },
  "_source": ["address","age"]
}'
```

简单说明一下上述请求规则，可以看到该请求是两层布尔嵌套。外层的规则是should，也就是其中条件满足其一即可。should中的第一个查询条件是满足age为40的（其实严格来说应该是age字段的值中包含40这个词组的）。另外一个条件又是一个布尔组合，其规则为must，即必须同时满足。这里需要满足的规则即为上节must样例中的规则，"address"字段的值必须同时包含"mill"和"lane"词组。总结起来，上述查询条件将会返回age为40的或者address中同时含有"mill"和"lane"的账户文档。

同时，为了便于查看，我们让结果集只返回"address"和"age"两个字段信息。

响应信息如下：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
 {
   "query": {
     "bool": {
       "should": [
         { "match": { "age": 40 } },
         {"bool":{"must": [
           { "match": { "address": "mill" } },
         { "match": { "address": "lane" } }
           ]}}
       ]
     }
   },
   "_source": ["address","age"]
 }'
{
  "took" : 11,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 46,
    "max_score" : 1.558547,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "136",
      "_score" : 1.558547,
      "_source" : {
        "address" : "198 Mill Lane",
        "age" : 38
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "549",
      "_score" : 0.98304415,
      "_source" : {
        "address" : "444 Schenck Place",
        "age" : 40
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "477",
      "_score" : 0.98304415,
      "_source" : {
        "address" : "369 Marconi Place",
        "age" : 40
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "509",
      "_score" : 0.98304415,
      "_source" : {
        "address" : "129 Plymouth Street",
        "age" : 40
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "916",
      "_score" : 0.98304415,
      "_source" : {
        "address" : "406 Bergen Avenue",
        "age" : 40
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "959",
      "_score" : 0.98304415,
      "_source" : {
        "address" : "931 Varick Avenue",
        "age" : 40
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "306",
      "_score" : 0.98304415,
      "_source" : {
        "address" : "196 Maujer Street",
        "age" : 40
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "938",
      "_score" : 0.98304415,
      "_source" : {
        "address" : "215 Matthews Place",
        "age" : 40
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "998",
      "_score" : 0.95721895,
      "_source" : {
        "address" : "206 Llama Court",
        "age" : 40
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "432",
      "_score" : 0.95721895,
      "_source" : {
        "address" : "239 Greenwood Avenue",
        "age" : 40
      }
    } ]
  }
}
```

可以看到，满足条件的共有5条数据，第一条为满足规则"address"中含有"mill"和"lane"词组的，其余四条为age为40的。

