# 获取（Get）API

获取API可以根据id从索引中取一个JSON格式的文档。下面的例子展示了从索引twitter中获取一个类型为tweet，id为1的文档：

```bash
curl -XGET 'http://localhost:9200/twitter/tweet/1'
```

上述操作的结果为：

```json
{
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "found": true,
    "_source" : {
        "user" : "kimchy",
        "postDate" : "2009-11-15T14:12:12",
        "message" : "trying out Elasticsearch"
    }
}
```
上述结果中包含了我们想要从文档获取的_index，_type，_id 和 _version字段，如果可以找到文档的话也包括文档实际内容的_source字段。

