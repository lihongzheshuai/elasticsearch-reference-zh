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



