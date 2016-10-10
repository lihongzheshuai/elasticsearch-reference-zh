# 执行查询

到目前为止我们已经见过一些基本的查询参数，现在我们来深入学习一下查询领域语言\(Query DSL\)。我们先来看一下返回的文档字段。默认的，所有查询请求的返回值中都会包含完整的JSON文档部分。该部分内容包含在source\(查询请求中的\_source字段\)。如果你不想返回完整的文档，我们提供了从源文件中返回部分字段的能力。

下面的例子展示了如何让查询请求只返回account\_number 和 balance两个字段（定义在\_source中）:

```bash
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}'
```

上面例子缩减了\_source字段。该请求仍会返回一个名为\_source的字段，不过其中只包含account\_number和balance两个字段。

如果你写过SQL，你就会发现上述例子与SQL中的SELECT 字段列表 FROM 语句很类似。

现在我们继续深入学习查询部分。前面我们学习了如何通过match\_all请求查询所有文档。现在我们介绍一种称为[匹配查询\(match query\)](/query-dsl/full-text-query/match-query.md)的新的查询类型，可以理解为基于字段的查询（也就是针对特定字段或字段组的查询）。

下面的例子返回账户编号为20的文档:

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "account_number": 20 } }
}'
```

下面的例子返回所有地址中包含"mill"词组的文档:

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill" } }
}'
```

下面的例子返回所有地址中含有"mill"或"lane"词组的文档:

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill lane" } }
}'
```

下面的例子是match一个的变形\(match\_phrase\)，将会返回所有地址中含有短语"mill lane"账户文档:

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_phrase": { "address": "mill lane" } }
}'
```

现在我们来介绍[布尔查询\(bool\(ean\) query\)](/query-dsl/compound-queries/bool-query.md)。布尔查询允许我们将若干小的查询通过布尔逻辑组合成一个大的查询。

下面的例子组合了两个match 查询，返回所有地址中同时包含"mill"和"lane"词组的账户文档:

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

在上述例子中，布尔语句must代表其包含的查询语句必须同时满足，对应的文档才算匹配成功。







