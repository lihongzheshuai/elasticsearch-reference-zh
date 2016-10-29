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

> 注意：当一个索引操作成功返回的时候，副本分块可能尚未启动（默认需要达到要求的数量）。在这种情况下，total的值会等于索引设置的所有的分块数量，而successful的值可能会等于已启动的分块的数量（主分块加副本）。此时没有失败的分块，failed的值是0。

## 自动创建索引

如果之前索引不存在，那么索引操作会自动创建索引（可参考[创建索引API](/indices-apis/create-index.md)来手动创建索引），同时如果事先不存在类型映射不那么也会自动动态创建（可参考[设置映射](/indices-apis/put-mapping.md)API来手动创建类型映射）。

映射本身是非常灵活自由的。新的字段和对象会自动的随着指定的类型添加到映射定义中。