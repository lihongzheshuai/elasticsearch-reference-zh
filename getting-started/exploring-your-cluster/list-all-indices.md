# 列出所有索引

现在我们来看一下我们的索引情况：

```
curl 'localhost:9200/_cat/indices?v'
```

响应信息：

```
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
```

说明集群中尚无索引。

