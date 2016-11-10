# 获取（Get）API

获取API可以根据id从索引中取一个JSON格式的文档。下面的例子展示了从索引twitter中获取一个类型为tweet，id为1的文档：

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1'
```

上述操作的结果为：

```json
{
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "found": true,
    "_source" : {
        "user" : "kimchy",
        "postDate" : "2009-11-15T14:12:12",
        "message" : "trying out Elasticsearch"
    }
}
```
上述结果中包含了我们想要从文档获取的_index，_type，_id 和 _version字段，如果可以找到文档（可见返回值中的found字段）的话也包括文档实际内容的_source字段。

该API也支持通过HEAD来查询文档是否存在，例如：

```bash
curl -XHEAD -i 'http://localhost:9200/twitter/tweet/1'
```

## 实时

默认情况下，获取(get)API是实时的，并且不会受索引刷新速率的影响（当数据将变得对查询可见的时候）。

如果想要关闭实时获取（GET），可设置参数realtime的值为false，或设置节点上全局设置参数action.get.realtime的值为false。

当获取一个文档的时候，你可以指定想要获取的字段(fields)。当可用的时候，它们将会被作为存储字段（如存储在映射表中的字段映射一样）获取到。当使用实时获取的时候，没有已存储字段的概念（只少需要一段时间间隔，一般来说是下一次刷新时），因此它们将会从源数据(source)本身抽出出来(注意，哪怕是源数据不可用)。一个最佳实践是，当使用实时获取的时候假设字段会从源数据中获取，哪怕字段已经被存储。

## 可选类型

获取API允许选择_type字段。设置为_all可以获取所有类型中匹配给定id的第一个文档。

(**译者注：**例如：

```bash
curl -XGET 'http://localhost:9200/twitter/_all/1?pretty'
{
  "_index" : "twitter",
  "_type" : "tweet",
  "_id" : "1",
  "_version" : 2,
  "found" : true,
  "_source" : {
    "user" : "kimchy",
    "message" : "trying out Elasticsearch"
  }
}

```
)

## 源数据过滤

默认情况下，获取操作会返回_source字段的内容，除非你使用了fields参数或禁用了_source字段。你可以通过_source参数来关闭_source的获取:

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1?_source=false'
```
如果你仅需要_source中的一两个字段，你可以使用_source_include和_source_exclude参数来包含或过滤出你需要的部分。
