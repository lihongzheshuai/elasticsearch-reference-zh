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

即讲一个字符串变换成另一个字符串最少需要几步。例如：kitten -> sitting，最少需要3部，那这个编辑距离就是3。

因此文章中对于该距离进行模糊匹配的意思就是，对于字符串长度为0-2的字符串，只支持完全匹配。对于长度3-5的，可模糊匹配距离是1的字符串。5以上支持距离是2的模糊匹配。例如：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '{
> "query": {
>   "fuzzy" : { "firstname" : "Rachelle" }
> }
> }'
{
  "took" : 24,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 5.610157,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "542",
      "_score" : 5.610157,
      "_source" : {
        "account_number" : 542,
        "balance" : 23285,
        "firstname" : "Michelle",
        "lastname" : "Mayo",
        "age" : 35,
        "gender" : "M",
        "address" : "657 Caton Place",
        "employer" : "Biflex",
        "email" : "michellemayo@biflex.com",
        "city" : "Beaverdale",
        "state" : "WY"
      }
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "222",
      "_score" : 5.5900564,
      "_source" : {
        "account_number" : 222,
        "balance" : 14764,
        "firstname" : "Rachelle",
        "lastname" : "Rice",
        "age" : 36,
        "gender" : "M",
        "address" : "333 Narrows Avenue",
        "employer" : "Enaut",
        "email" : "rachellerice@enaut.com",
        "city" : "Wright",
        "state" : "AZ"
      }
    } ]
  }
}

```
通过Rachelle，可以模糊匹配到firstname为Rachelle和Michelle的账户，因为他们的edit distance<=2。
