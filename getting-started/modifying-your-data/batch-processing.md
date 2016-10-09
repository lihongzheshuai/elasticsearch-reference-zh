# 批量处理

除了可以单独索引，更新和删除单个文档之外，Elasticsearch还支持通过\_bulk API批量的执行上述操作。该功能非常的重要，因为它提供了一种非常高效的机制，可以在尽可能少的网络交互的情况下，高效的批量执行操作。

举个例子，下面请求通过一个bulk操作同时索引两个文档（ID为1的John Doe 和 ID为2的 Jane Doe）：

```
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
'
```

下面的例子通过一个bulk操作在更新第一个文档（ID为1的）的同时删除第二个文档（ID为2的）：

```
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
'
```

注意到，对于delete操作，之后没有关联的文档信息而只需要指定需要删除的文档ID。

bulk API顺序的执行所有的操作。不论前一个操作因为何种原因失败，它都会继续执行后续的操作。



