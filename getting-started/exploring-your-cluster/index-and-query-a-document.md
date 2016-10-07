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

从上述信息中可以看到，我们成功的在customer索引中创建了一个新的隶属于external类型的customer文档。该文档的内部ID为1，由我们在创建时指定。

需要指出的是，Elasticsearch并不需要在创建文档前事先创建好的索引。在上述例子中，如果customer索引不存在的话，Elasticsearch会自动创建它。

现在我们来获取刚刚索引的文档：

```
curl -XGET 'localhost:9200/customer/external/1?pretty'
```

响应信息为：





