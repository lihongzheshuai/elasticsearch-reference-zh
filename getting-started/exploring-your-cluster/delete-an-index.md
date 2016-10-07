# 删除索引

现在我们删除刚刚创建的索引，然后再次查询索引列表：

```
curl -XDELETE 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
```

响应信息如下：

```
curl -XDELETE 'localhost:9200/customer?pretty'
{
  "acknowledged" : true
}
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
```

这代表索引已被成功删除，我们又回到了集群的初始状态。

在继续之前，我们来回顾一下目前学到的与文档有关的API：

```
curl -XPUT 'localhost:9200/customer'
curl -XPUT 'localhost:9200/customer/external/1' -d '
{
  "name": "John Doe"
}'
curl 'localhost:9200/customer/external/1'
curl -XDELETE 'localhost:9200/customer
```

如果我们是用心去学习的上述API，其实我们可以发现Elasticsearch中访问数据的url规则。概括起来就是：

```
curl -X<REST Verb> <Node>:<Port>/<Index>/<Type>/<ID>
```

该REST接口规则适用于所有API命令，

