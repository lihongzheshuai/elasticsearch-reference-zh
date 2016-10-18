# 译者注 补充样例

在本节中，官方文档概括的说明了很多有关多多索引的参数和支持的格式，但是没有给出具体的样例，可能会存在于后续的章节中，不过笔者这里还是根据理解自己先进行了一些实验。

当前集群中索引状如下：

```bash
curl -XGET 'localhost:9200/_cat/indices?v'
health status index     pri rep docs.count docs.deleted store.size pri.store.size 
green  open   lihongzhe   5   1          1            0      7.3kb          3.6kb 
green  open   bank        5   1       1000            0    885.3kb        442.6kb 
       close  close                                                               
green  open   customer    5   1          3            0     19.3kb          9.6kb 
```

## 跨多索引查询

