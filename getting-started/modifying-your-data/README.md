# 修改你的数据

Elasticsearch提供了准实时的数据处理和查询能力。一般来说，从你进行数据的索引\/更新\/删除操作到可查询到该操作的产生的结果大约会有1秒左右的延迟（刷新间隔）。这是与其他SQL类型数据平台一个重要的区别，在SQL类平台中数据在事务结束后是立即可用的。

## 索引\/替换文档

之前我们介绍过如何索引一个文档。现在我们再次调用该命令：

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
```

上述命令会将指定文档保存到customer索引中，类型是external，ID是1。如果我们用不同（或相同）的文档内容再次执行该命令，Elasticsearch将会替换（也就是重新索引）新的文档，ID仍是1：

```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "Jane Doe"
}'
```

上述命令将文档内容中的名字从"John Doe"改为"Jane Doe"。如果我们用不同ID，那么将会索引一个新的文档，原有文档不会改变。

```bash
curl -XPUT 'localhost:9200/customer/external/2?pretty' -d '
{
  "name": "Jane Doe"
}'
```

上述命令索引了一个新的文档，ID为2。

在索引时，ID值是可选的。如果未指定，Elasticsearch将会生成一个随机ID用来索引文档。由Elasticsearch生成的实际ID（或是我们指定的ID）会在索引API的响应值中返回。

下面例子展示了如何索引一个未指定ID的文档：

```bash
curl -XPOST 'localhost:9200/customer/external?pretty' -d '
{
  "name": "Jane Doe"
}'
```

需要注意的是，在上面的例子中，由于没有指定ID我们使用POST方法替代了PUT方法。

