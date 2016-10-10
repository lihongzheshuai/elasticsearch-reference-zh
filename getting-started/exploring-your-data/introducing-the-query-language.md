# 查询语言介绍

Elasticsearch提供类似JSON格式的领域特定语言\(DSL\)用于执行查询请求。可参见[查询领域专用语言](/query-dsl/README.md)部分。该查询语言相当的全面并且初看起来可能有些“恐怖”，不过最好的学习它的方式就是从一些基础的例子开始。

回顾上一个例子中我们执行的查询请求：

```
{
  "query": { "match_all": {} }
}
```

分析上述信息，query部分用于说明我们查询的目标，match\_all 部分是我们想要执行的查询类型。match\_all 查询代表查询指定索引下的所有文档。

除了参数query，我们还可以提供其他参数从而影响查询结果。例如，下面的请求执行了match\_all 操作，但是仅返回一个文档：

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "size": 1
}'
```

需要注意的是，如果没有指定size参数，默认返回10条数据。

下面例子执行了一个match\_all 请求，并且返回第11条到第20条的文档:

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
```

from参数\(从0开始\)指定了返回文档中起始文档的序号，size参数指定了从起始文档开始需要返回文档的个数。该特性对于分页返回查询结果非常有用。

