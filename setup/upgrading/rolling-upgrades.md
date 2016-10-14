# 滚动升级

滚动升级可以让Elasticsearch集群一次升级一个节点，不间断的对用户提供服务。除升级期间之外，其它时间Elasticsearch集群不允许多版本共存，因为分块不会从高版本复制到低版本。

可通过此[表格](/setup/upgrading/README.md)来确认你的Elasticsearch版本是否支持滚动升级。

执行滚动升级步骤如下：

## 第一步：禁用分块重分配

当你停止一个节点的时候，重分配程序将会立即尝试将该停止节点的分块复制到集群中的其他节点，这将会导致浪费很多的I/O资源。可在停止节点前禁用重分配来避免此资源浪费：

```bash
PUT /_cluster/settings
{
  "transient": {
    "cluster.routing.allocation.enable": "none"
  }
}
```

## 第二步：禁用不必要的索引并采用同步刷新（可选）
