# 索引API

所以API可增加或修改指定索引中JSON格式的文档，使其可以被检索到。下面列子展示了如何向索引"twitter"中的"tweet"类型下，以id 1 插入JSON格式的文档：

```bash
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```


