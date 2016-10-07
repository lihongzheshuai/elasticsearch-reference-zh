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





