# 查询API

我们现在来做一些简单的查询。有两种方法可以进行查询: 一种是通过[REST请求的URI](/search-apis/uri-search.md)发送查询参数进行查询，另一种是通过[REST请求体](/search-apis/request-body-search/README.md)发送参数进行查询。相较而言，请求体的方式可以让你用一种更具有可读性的JSON格式组织请求，更加具有表现力。我们将会用一个例子来说明URI请求的方式，然后在剩下的文档将都会采用请求体的方式。

查询的REST API的入口为\_search。下面的例子将返回bank索引中的所有文档：

```bash
curl 'localhost:9200/bank/_search?q=*&pretty'
```

我们来仔细分析该查询请求。我们查询的是bank索引（通过\_search 入口）下的内容，参数q=\* 意味着通知Elasticsearch 匹配该索引在的所有文档。参数pretty在这里依然只是通知Elasticsearch将返回信息以一种更方便阅读的JSON格式输出。

响应信息如下（只展示一部分内容）：

```bash
curl 'localhost:9200/bank/_search?q=*&pretty'
{
  "took" : 63,
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
      "_id" : "1",
      "_score" : 1.0, "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "6",
      "_score" : 1.0, "_source" : {"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
    }, {
      "_index" : "bank",
      "_type" : "account",
```

从响应信息中，我们可以看到：

* took - Elasticsearch用于执行该查询请求消耗的时间，单位是毫秒。
* timed\_out - 用于告知我们查询是否超时。
* \_shards - 告诉我们检索了多少个分块，以及成功\/失败检索的分块的个数。
* hits - 查询结果。
* hits.total - 查询结果条数。
* hits.hits - 实际查询结果数据的数组（默认展示头10条）。
* \_score 和 max\_score - 目前暂时忽略该字段。

下面是通过请求体发送的，作用一致的查询请求样例：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
```

与URI方式主要的区别是，URI上不再有显式的查询参数q=\*，而是通过POST请求发送一个JSON格式的请求体到\_search API上。下一节我们会讨论该JSON格式的请求体。

响应信息如下（部分展示）：

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
{
  "took" : 26,
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
      "_id" : "1",
      "_score" : 1.0, "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "6",
      "_score" : 1.0, "_source" : {"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "13",
```

需要注意的是，一旦你得到查询结果，Elasticsearch就完全完成了该请求并且不会在服务端保留任何信息或者存有任何关于你的结果集的游标信息。这与你使用其他的诸如SQL类平台有很大的不同，那些平台允许你先获取部分结果集，然后通过服务端持有游标信息，你可以继续与服务端通信获取剩下的（分页的）数据。

