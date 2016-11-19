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
如果你仅需要_source中的一两个字段，你可以使用_source_include和_source_exclude参数来包含或过滤出你需要的部分。这在获取大文档的时候尤其有帮助，因为获取部分数据可降低网络负载。两个参数都是使用逗号分隔的方式列出字段或模糊匹配表达式。例如：

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1?_source_include=*.id&_source_exclude=entities'

```

如果你仅想指定包含的字段，可以使用简写的表示形式：

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1?_source=*.id,retweeted'
```

## 字段

获取操作支持通过fileds参数指定返回一组已存储的字段。例如：

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1?fields=title,content'
```
为了向后兼容，如果请求的字段没有被存储，那么将会从_source中（解析并抽取）获取。该功能已被[源数据过滤](#源数据过滤)参数取代。

从文档自身获取的字段值总是以数组的形式返回。诸如_routing和_parent等元数据字段则从不以数组形式返回。

仅有叶子节点字段可以通过field选项返回。因此对象字段不能返回并且这样的请求会失败。

## 生成字段

如果在索引和刷新中间没有发生刷新，那么GET请求将会访问事务日志以获取文档。然而，某些字段仅在索引时生成。如果你试图访问仅在索引时才会生成的字段，你将会得到一个异常（默认情况）。如果访问事务日志，你可以通过设置参数ignore_errors_on_generated_fields=true来忽略自动生成的字段。

## 直接获取_source字段

使用/{index}/{type}/{id}/_source 访问点来仅获取文档的_source字段而不包含任何多余的内容。例如：

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1/_source'
```

你还可以使用同样的内容过滤参数来控制_source中需要返回的部分：

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1/_source?_source_include=*.id&_source_exclude=entities'
```

注意，对_source访问点，也支持通过HEAD请求来高效的测试文档是否存在。Curl的例子：

```bash
curl -XHEAD -i 'http://localhost:9200/twitter/tweet/1/_source'
```

## 路由

当索引时使用了路由控制功能，那么在获取文档时也需要指定路由值。例如：

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1?routing=kimchy'
```

上述例子将会获取id为1的推文，但是将会根据用户进行路由分配。注意，在获取时未指定正确的路由将会导致获取不到文档。

## 倾向性(Preference)

可以控制获取请求更倾向于运行在哪个分块副本上。默认的，操作是在副本间随机分配的。

preference可设置下列值：

_primary  &nbsp仅会再主分块上执行操作。
    
