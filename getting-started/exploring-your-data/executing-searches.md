# 执行查询

到目前为止我们已经见过一些基本的查询参数，现在我们来深入学习一下查询领域语言(Query DSL)。我们先来看一下返回的文档字段。默认的，所有查询请求的返回值中都会包含完整的JSON文档部分。该部分内容包含在source(查询请求中的_source字段)。如果你不想返回完整的文档，我们提供了从源文件中返回部分字段的能力。

下面的例子展示了如何让查询请求只返回account_number 和 balance两个字段（定义在_source中）:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}'
```
上面例子素偶写


