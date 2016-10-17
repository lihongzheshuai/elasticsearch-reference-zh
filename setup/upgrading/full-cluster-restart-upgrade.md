# 全集群重启升级

当进行主版本升级的时候，比如从0.x升级到1.x或者从1.x升级到2.x系列，Elasticsearch升级时需要全集群重启。滚动升级不支持主版本升级。

执行全集群重启升级的步骤如下：

## 第一步：禁用分块重分配

当你停止一个节点的时候，重分配程序将会立即尝试将该停止节点的分块复制到集群中的其他节点，这将会导致浪费很多的I/O资源。可在停止节点前禁用重分配来避免此资源浪费：

```bash
PUT /_cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}
```
如果是从0.90.x升级到1.x系列，使用下列接口：

```bash
PUT /_cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.disable_allocation": true,
    "cluster.routing.allocation.enable": "none"
  }
}
```

## 第二步：进行同步刷新(synced flush)

如果你临时禁止非必须的索引并使用[同步刷新(synced-flush)](/flush/synced-flush.md)，那么分块恢复会快的多:

```bash

POST /_flush/synced

```

同步flush请求是一个"best effort"（尽力而为）的操作。如果存在任何挂起的索引操作它都会失败，但是，在有必要进行多次重复请求的情况下，它能确保该操作是安全的。

## 第三步：停止并升级所有节点

停止集群中所有节点上的Elasticsearch服务。每个节点都可以按照上节[第三步：停止并升级单节](/setup/upgrading/rolling-upgrades.md)点介绍那样完成升级。
