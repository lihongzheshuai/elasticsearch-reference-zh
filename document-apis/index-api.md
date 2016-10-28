# 索引API

所以API可增加或修改指定索引中JSON格式的文档，使其可以被检索到。下面列子展示了如何向索引"twitter"中的"tweet"类型下，以id 1 插入JSON格式的文档：

```bash
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```
上述操作的结果是：

```bash
{
    "_shards" : {
        "total" : 10,
        "failed" : 0,
        "successful" : 10
    },
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "created" : true
}
```
_shards部分提供了索引操作所涉及到的副本和分块信息。

* total - 指索引操作分布在多少个分块上（主分块和副本分块）。
* successful - 操作成功的分块数
* failures - 在索引操作在某个副本上执行失败时，包含副本相关错误信息的数组。

一个索引操作是成功的，successful的值至少是1。