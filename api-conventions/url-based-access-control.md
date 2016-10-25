# 基于URL的访问控制

许多用户使用具有基于URL访问控制功能的代理来安全访问Elasticsearch索引。对于[多查询(multi-search)](/search-apis/multi-search-api.md), [多获取(multi-get)](/document-apis/multi-get-api.md)和[批量(bulk)](/document-apis/bulk-api.md)请求，用户可以选择在URL上指定索引或是在每个独立请求的请求体中指定。这会使基于URL的访问控制变得非常困难。

为了避免用户覆盖URL中指定的索引，可在config.yml配置文件添加如下配置：

```
rest.action.multi.allow_explicit_index: false
```

该配置项默认值是true，当设置成false的时候，Elasticsearch会拒绝在请求体中指定索引的请求。