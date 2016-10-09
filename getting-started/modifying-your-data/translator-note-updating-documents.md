# 译者注（关于开启执行脚本功能）

在本节中提到了Elasticsearch可通过执行脚本来更新数据，不过该功能在新版中默认是关闭的。在关闭状态下，执行上述例子中的脚本得到的响应信息如下：

```
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



