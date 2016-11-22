# 删除API

删除API可通过id删除指定索引下的JSON类型的文档。下面的例子从索引twitter中删除类型为tweet，id为1的JSON格式的文档：

```bash
$ curl -XDELETE 'http://localhost:9200/twitter/tweet/1'
```

上述删除操作的结果如下：

```json
{
    "_shards" : {
        "total" : 10,
        "failed" : 0,
        "successful" : 10
    },
    "found" : true,
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 2
}
```

## 版本