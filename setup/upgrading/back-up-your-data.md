# 备份你的数据！

总是要在升级前备份你的数据。这可以让你出现意外时进行回滚。升级有些时候会包括对Lucence版本的升级，Elasticsearch使用Lucence来访问索引文件，在索引文件升级到可供新版本的Lucence使用之后，它将无法被老Elasticsearch集群中的Lucence版本 访问。

> 警告⚠️：**__总是要在升级前备份你的数据__**。如果你没有备份数据，你将无法回滚到之前的版本。

## 备份1.0及以后的版本

想要备份1.0及以后的版本，最简单的方式是使用snapshot特性。详见文档：[使用快照进行备份和故障恢复](/modules/snapshot-and-restore.md)。

## 备份0.90.x及之前的版本

想要备份0.90.x系统：

### 第一步: 禁用索引刷新

这将避免索引在备份期间索引刷新到磁盘上:

```bash
PUT /_all/_settings
{
  "index": {
    "translog.disable_flush": "true"
  }
}
```

### 第二步: 禁用重分配(reallocation)

这将避免集群在备份期间将数据从一个节点迁移到另一个节点:

```bash
PUT /_cluster/settings
{
  "transient": {
    "cluster.routing.allocation.enable": "none"
  }
}
```

### 第三部: 备份你的数据

在禁用索引刷新和重分配之后，你可以任何你熟悉的备份方法（tar, storage array snapshots, 备份软件）备份Elasticsearch的数据目录。

### 第四部: 重启重分配和刷新

当备份结束不需要再从Elasticsearch的数据目录读取数据的时候，需要重启重分配和索引刷新：

```bash
PUT /_all/_settings
{
  "index": {
    "translog.disable_flush": "false"
  }
}

PUT /_cluster/settings
{
  "transient": {
    "cluster.routing.allocation.enable": "all"
  }
}
```