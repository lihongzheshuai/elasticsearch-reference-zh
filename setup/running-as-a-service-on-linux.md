# 在Linux上以服务形式运行

为了以服务形式在你的操作系统上运行Elasticsearch，提供的包尽可能简单的让你在系统重启或更新期间启动和停止elasticsearch。

## Linux

我们当前编译的版本自动生成一个debian包和一个RPM包，可在下载页面下载。包本身没有任何依赖，不过你需要首先安装JDK。

每个包提供了一个配置文件，允许你配置如下参数：

| 参数名 | 描述 |
| --- | --- |
| ES\_USER | 启动用户，默认是elasticsearch |
| ES\_GROUP | 启动用户群组，默认是elasticsearch |
| ES\_HEAP\_SIZE | 堆内存大小 |
| ES\_HEAP\_NEWSIZE | 新生带堆内存大小 |
| ES\_DIRECT\_SIZE | 直接内存最大值 |
| MAX\_OPEN\_FILES | 最大打开文件数，默认是65536 |
| MAX\_LOCKED\_MEMORY | 最大锁定内存大小。如果在elaticsearch.yml中配置了bootstrap.memory.lock，那么该参数需要设置成"unlimited"。你必须设置ES\_HEAP\_SIZE的值。 |
| MAX\_MAP\_COUNT | 进程所拥有的最大内存映射区域数。如果使用mmapfs索引存储类型，这里需要设置一个比较大的值。更多信息参见[linux内核文档](https://github.com/torvalds/linux/blob/master/Documentation/sysctl/vm.txt)中关于max\_map\_count的介绍。该配置是在Elasticsearch启动前，通过sysctl命令指定。默认值是65535。 |
| LOG\_DIR | 日志目录，默认是\/var\/log\/elasticsearch |
| DATA\_DIR | 数据目录，默认是\/var\/lib\/elasticsearch |
| CONF\_DIR | 配置文件目录（该目录中需要包含elasticsearch.yml和logging.yml配置文件），默认是\/etc\/elasticsearch |
| ES\_JAVA\_OPTS | 想要配置的额外的java选项。比如你在一个分布式环境下，由puppet或chef进行集群管理，此时你想设置node.name的值而又不想修改elasticsearch.yml配置文件，那么该参数就非常有帮助。例如：ES\_JAVA\_OPTS="-Des.node.name=search-01" |
| RESTART\_ON\_UPGRADE | 配置在包升级后重启，默认是false。这意味着在你在安装一个包后必须手动重启Elasticsearch。如此配置的原因是想确保集群升级不会导致分块的持续重分配，从而导致过高的网络负载和降低集群的响应速率。 |
| ES\_GC\_LOG\_FILE | 垃圾收集日志文件的绝对路径，由JVM创建。需要注意的是，该日志文件可能会增长非常迅速，因此该选项默认是关闭的。 |

### Debian/Ubuntu

由于使用了像update-rc.d 这样的debian包工具定义运行时需要的环境，因此debian包内置了所有需要的依赖。如你所想，初始化脚本为/etc/init.d/elasticsearch。配置文件存放在/etc/default/elasticsearch目录。

debian包默认不以服务的形式启动。目的是为了避免实例在没有配置好的情况下意外加入集群。在使用dpkg -i 命令安装后，你可以通过下列命令确保Elasticsearch 在系统启动后启动，然后再启动elasticsearch集群：

