# 升级

> 重要⚠️：在升级Elasticsearch之前：
> 
> * 检查[破坏性变化文档](/breaking-changes/README.md)。
> * 在升级生产环境集群之前，先在开发环境进行升级测试。
> * 总是在你升级之前备份数据。如果没有备份数据，你无法回滚到之前的版本。

Elasticsearch 可以进行滚动升级，从而不中断服务。本章将详细介绍如何进行滚动升级和全集群重启升级。

通过下表来确认你的版本是否支持滚动升级:

|原版本|升级至版本|支持的升级类型|
|---|---|---|
|0.90.x|2.x|[全集群重启](/setup/upgrading/full-cluster-restart-upgrade.md)|
|1.x|2.x|[全集群重启](/setup/upgrading/full-cluster-restart-upgrade.md)|
|2.x|2.y|[滚动升级](/setup/upgrading/rolling-upgrades.md)(y > x)|

## 升级带插件的Elasticsearch

考虑插件的升级。插件必须与Elasticsearch一起升级。

查看插件的提供者，确认插件是否与你要升级到的版本兼容。如果执行滚动升级，还需要确认插件是否同时兼容集群中各种混合版本。大多数插件需要随Elasticsearch一起升级，一些主要通过浏览器访问的插件(_site 插件)，在API兼容的情况下，可能可以继续提供服务。

滚动升级和全集群重启升级在每个节点的上的执行过程一般如下：

* 停止Elasticsearch
* 升级Elasticsearch
* 升级插件
* 启动Elasticsearch

