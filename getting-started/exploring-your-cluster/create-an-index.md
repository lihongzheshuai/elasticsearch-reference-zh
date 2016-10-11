# 创建索引

现在，我们来创建一个名为“customer”的索引，然后再次调用列出所有索引接口：

```bash
curl -XPUT 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
```

第一行命令通过PUT方法创建了一个名为“customer”的索引。我们通过在请求结尾加上pretty参数使得响应的JSON信息能够以更加便于阅读的格式进行输出。

响应信息如下：

```bash
curl -XPUT 'localhost:9200/customer?pretty'
{
 "acknowledged" : true
}

curl 'localhost:9200/_cat/indices?v'

health index pri rep docs.count docs.deleted store.size pri.store.size
yellow customer 5 1 0 0 495b 495b

```

第二行命令的输出说明，我们现在拥有了一个名为customer的索引，并且该索引有5个主要分块和一个副本（默认值），而且其中没有文档。

你还可能注意到，customer索引的健康状态是黄色的。回顾我们之前的介绍，黄色代表某些副本尚未分配。产生此现象的原因是Elasticsearch默认为该索引创建了一个副本，而我们当前只有一个运行节点，因此无法进行副本分配（为了高可用）。当有新的节点的加入时，便可完成分配。当副本分配到第二个节点上时，该索引的健康状态将变成绿色。

