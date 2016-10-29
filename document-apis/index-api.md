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

映射本身是非常灵活自由的。新的字段和对象会自动的随着指定的类型添加到映射定义中。更多关于映射定义的信息科参考[映射](/mapping/README.md)章节。

可以通过将所有节点的配置文件中的选项action.auto_create_index的值设置为false来关闭索引自动创建功能。自动创建映射的功能可通过将选项index.mapper.dynamic的值设置为false来关闭（或者在指定索引上配置）。

自动创建索引功能可包含基于模式匹配的黑/白名单，例如，将选项action.auto_create_index的值设置为+aaa*,-bbb*,+ccc*,-*（+号代表允许，-号代表禁止）。

## 版本

每个索引的都有一个版本号。版本号会作为索引请求返回值的一部分返回。当指定version参数的时候，索引接口可选择弃用[乐观并发控制( optimistic concurrency control)](http://en.wikipedia.org/wiki/Optimistic_concurrency_control)这会控制操作所针对文档的版本。一个使用版本控制的很好的例子是执行一个读后更新的事务。从最初读取的文档中指定一个版本(version)可确保在这段时间内不会发生任何变化(当读取为了更新时，推荐设置preference的值为_primary)。例如：

```bash
curl -XPUT 'localhost:9200/twitter/tweet/1?version=2' -d '{ "message" : "elasticsearch now has versioning support, double cool!" }'
```