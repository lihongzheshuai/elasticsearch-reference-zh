# 执行聚合

聚合函数提供了对你的数据进行分组和抽取等统计分析的能力。理解聚合最简单的方式就把它与SQL中的聚合以及GROUP BY语句进行类比。在Elasticsearch中，你可以在返回查询数据的同时，返回进行过聚合分析的结果。这个特性可以让你通过一个简单的API，同时执行查询和多种聚合操作，从而通过一次请求得到所有的（或是其中一个）结果，从而避免进行多次网络交互，提高效率。

我们以一个例子开始，下面的例子将账户按照state进行分组，然后根据每组中含有的账户数量进行降序排序，返回前10条数据:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      }
    }
  }
}'
```

上述查询相当于如下SQL语句：

```
SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC
```

返回值为（部分）：

```js
"hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "buckets" : [ {
        "key" : "al",
        "doc_count" : 21
      }, {
        "key" : "tx",
        "doc_count" : 17
      }, {
        "key" : "id",
        "doc_count" : 15
      }, {
        "key" : "ma",
        "doc_count" : 15
      }, {
        "key" : "md",
        "doc_count" : 15
      }, {
        "key" : "pa",
        "doc_count" : 15
      }, {
        "key" : "dc",
        "doc_count" : 14
      }, {
        "key" : "me",
        "doc_count" : 14
      }, {
        "key" : "mo",
        "doc_count" : 14
      }, {
        "key" : "nd",
        "doc_count" : 14
      } ]
    }
  }
}
```

我们可以看到，有21个账户在AL\(abama\)，然后17个账户在TX，15个账户在ID\(aho\)，等等。

注意到，为了不展现搜索的数据内容我们设置了size=0，因为我们只关注响应中有关集合信息的部分。

在上述聚合查询的基础上，下面的例子计算每个state中账户余额的平均值\(同样根据state中的数据的数量降序排列，并返回前10条数据\)：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```

请注意我们是如何在group\_by\_state聚合操作中嵌套average\_state操作的。这是所有聚合操作的通用格式。你可以任意嵌套聚合操作，从而逐步抽取出你的数据的概要信息。

在上述聚合的基础上，我们现在根据平均余额降序排列：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state",
        "order": {
          "average_balance": "desc"
        }
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```



