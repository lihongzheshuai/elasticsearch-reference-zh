# 创建索引

现在，我们来创建一个名为“customer”的索引，然后再次调用列出所有索引接口：

```
curl -XPUT 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
```

第一行命令通过PUT方法创建了一个名为“customer”的索引。

