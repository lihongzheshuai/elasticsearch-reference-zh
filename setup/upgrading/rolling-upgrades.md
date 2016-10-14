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

你可以在升级期间继续进行索引。不过，如果你临时禁止非必须的索引并使用[同步刷新(synced-flush)](/flush/synced-flush.md)，那么分块恢复会快的多:

```bash
POST /_flush/synced
```

同步flush请求是一个"best effort"（尽力而为）的操作。如果存在任何挂起的索引操作它都会失败，但是，它在必要的情况下重新请求多次的时候是安全的。

### 第三步：停止并升级单个节点

在升级集群中的某个节点前先停止该节点。

> 小提示：当使用zip或tar包版的时候，config,data,logs和plugins目录都默认存放在Elasticsearch的主目录下。
>
一个比较好的习惯是讲它们放置在不同路径这，这样在升级Elasticsearch的时候删除这些目录，都无需其它的更改。这些自定义的路径可通过path.conf和path.data等选项进行[配置](/setup/configuration.md)。
>
Debain和RPM包将这些目录放置在操作系统[合适的位置](/setup/directory-layout.md)。

对于使用[Debian或RPM](/setup/repositories.md)包进行升级来说：

* 使用rpm或dpkg命令安装新的包。所有的文件会被放置在合适的位置，并且配置文件不会被覆盖。

对于使用zip或tar包升级来说：

* 将zip或tar包解压到新的位置，确认不要覆盖config和data目录。
* 可以将旧版本config目录下的配置文件拷贝到新版本下，也可以通过--path.conf选项指向外部配置文件目录。
* 可以将旧版data目录拷贝到新版中，也可以在config/elasticsearch.yml配置文件中，通过配置path.data选项配置data目录的位置。

## 第四步：启动升级后的节点

启动升级后的节点，然后通过日志文件或如下请求确认该节点是否成功加入集群：

```bash
GET _cat/nodes
```
## 第五步：重启分块重分配

当节点加入集群后，重新开启分块重分配功能:

```bash
PUT /_cluster/settings
{
  "transient": {
    "cluster.routing.allocation.enable": "all"
  }
}
```

## 第六步：等待节点恢复

在升级下一个节点之前，你需要等待集群完成分块重分配。你可以通过[_cat/health](/cat-apis/cat-health.md)请求检查该过程：

```bash
GET _cat/health
```

等待status列的值从黄色(yellow)变为绿色(green)。绿色代表所有的主从分块都分配完成。

>重要⚠️：在滚动升级期间，分配到高版本节点上的主分块不会将它分本分配到低版本的节点上，因为高版本可能采用新的数据格式，低版本无法解析。
>
如果无法对高版本的分块的副本进行分配，例如，在集群中只有一个高版本节点的时候，该副本将不会分配，集群状态将保持黄色。
>
在这种情况下，在继续之前需要检查未初始化或位分配的分块（检查init和relo列的值）。
>
当其它节点升级以后，分块副本会立即被分配，集群状态将会变为绿色。

未配置sync-flushed的分块，恢复将耗费更多的时间。单个分块的回复状态，可通过_cat/recovery 接口进行监控：

