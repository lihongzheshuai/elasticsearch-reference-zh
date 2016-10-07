# 索引和查询文档

现在我们向customer索引中存入一些数据。记得我们之前介绍过，如果想要索引一个文档，必须先让Elasticsearch知道它所属的类型。

现在我们在customer索引中索引一个简单的customer文档，类型为“external”，ID是1，JSON文档格式为： { "name": "John Doe" }

```
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
```

响应如下：

```
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "created" : true
}
```

从上述信息中可以看到，

