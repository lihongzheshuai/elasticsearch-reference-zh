# 删除API

删除API可通过id删除指定索引下的JSON类型的文档。下面的例子从索引twitter中删除类型为tweet，id为1的JSON格式的文档：

```bash
$ curl -XDELETE 'http://localhost:9200/twitter/tweet/1'
```