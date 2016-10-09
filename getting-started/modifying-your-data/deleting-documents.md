# 删除文档

删除文档相当的简单直接。下面的例子展示了如何删除之前创建的ID为2的文档：

```
curl -XDELETE 'localhost:9200/customer/external/2?pretty'
```

delete-by-query插件可以支持删除满足指定条件的所有文档。

