# 更新文档

除了可以索引和替换文档外，我们还可以更新文档。不过需要注意的是，Elasticsearch并不是真正的做文档替换。在我们更新的时候，Elasticsearch实际是先删除旧文档然后索引一个新的文档。

下面的例子展示了如何把我们之间的文档（ID为1的）内容中name字段的值更新为"Jane Doe"：

```
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe" }
}'
```

下面的例子展示了如何把我们之前的文档（ID为1的）name字段的值更新为"Jane Doe"同时添加age字段:

```
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe", "age": 20 }
}'
```

我们也可以利用脚本做更新操作。

