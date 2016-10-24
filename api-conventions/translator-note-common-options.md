# 译者注 - 通用选项

## 关于请求字符串中的请求体

这里给出一个通过GET请求，source参数传递查询条件的例子，对比之前的-XPOST样例：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
 {
   "query": { "match_all": {} },
   "size": 1
 }'
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "25",
      "_score" : 1.0,
      "_source" : {
        "account_number" : 25,
        "balance" : 40540,
        "firstname" : "Virginia",
        "lastname" : "Ayala",
        "age" : 39,
        "gender" : "F",
        "address" : "171 Putnam Avenue",
        "employer" : "Filodyne",
        "email" : "virginiaayala@filodyne.com",
        "city" : "Nicholson",
        "state" : "PA"
      }
    } ]
  }
}

```

通过source参数发送即：

```bash
curl -g -XGET 'localhost:9200/bank/_search?source={"query":{"match_all":{}},"size":1}&pretty'
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "25",
      "_score" : 1.0,
      "_source" : {
        "account_number" : 25,
        "balance" : 40540,
        "firstname" : "Virginia",
        "lastname" : "Ayala",
        "age" : 39,
        "gender" : "F",
        "address" : "171 Putnam Avenue",
        "employer" : "Filodyne",
        "email" : "virginiaayala@filodyne.com",
        "city" : "Nicholson",
        "state" : "PA"
      }
    } ]
  }
}
```

注意，通过curl发送JSON格式的source参数值是，需要添加-g参数，否则报错。

```bash
curl -XGET 'localhost:9200/bank/_search?source={"query":{"match_all":{}},"size":1}&pretty'
curl: (3) [globbing] nested braces not supported at pos 45
```

## 关于Levenshtein Edit Distance(编辑距离) 


