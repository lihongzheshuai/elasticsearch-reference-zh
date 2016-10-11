# 译者注（关于开启执行脚本功能）

在本节中提到了Elasticsearch可通过执行脚本来更新数据，不过该功能在新版中默认是关闭的。在关闭状态下，执行上述例子中的脚本得到的响应信息如下：

```bash
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
 {
   "script" : "ctx._source.age += 5"
 }'
{
  "error" : {
    "root_cause" : [ {
      "type" : "remote_transport_exception",
      "reason" : "[Nth Man: the Ultimate Ninja][127.0.0.1:9301][indices:data/write/update[s]]"
    } ],
    "type" : "illegal_argument_exception",
    "reason" : "failed to execute script",
    "caused_by" : {
      "type" : "script_exception",
      "reason" : "scripts of type [inline], operation [update] and lang [groovy] are disabled"
    }
  },
  "status" : 400
}
```

为了做验证，笔者决定开启该功能。修改elasticsearch.yml配置文件，在结尾添加：

```bash
script.inline: true
script.indexed: true
```

然后重启集群。

再次调用同样的接口，得到信息如下：

```bash
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
 {
   "script" : "ctx._source.age += 5"
 }'
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 6,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "failed" : 0
  }
}
```

查看数据变化：

```bash
./esget customer/external/1
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 6,
  "found" : true,
  "_source" : {
    "name" : "Jane Doe",
    "age" : 25
  }
}
```

发现年龄从20变成了25岁。

注：esget是笔者为了方便验证写的一个脚本，把通用的curl前缀部分封装了起来。esget内容如下：

```bash
#!/bin/bash

curl -XGET "localhost:9200/$1?pretty"
```

