# 在Linux上以服务形式运行

为了以服务形式在你的操作系统上运行Elasticsearch，提供的包尽可能简单的让你在系统重启或更新期间启动和停止elasticsearch。

## Linux

我们当前编译的版本自动生成一个debian包和一个RPM包，可在下载页面下载。包本身没有任何依赖，不过你需要首先安装JDK。

每个包提供了一个配置文件，允许你配置如下参数：

|参数名|描述|
| --- | --- |
| ES\_USER | 启动用户，默认是elasticsearch |
| ES\_GROUP | 启动用户群组，默认是elasticsearch |
| ES\_HEAP\_SIZE | 堆内存大小 |
| ES\_HEAP\_NEWSIZE | 新生带堆内存大小 |
| ES\_DIRECT\_SIZE | 直接内存最大值 |
| MAX\_OPEN\_FILES | 最大打开文件数，默认是65536 |
| MAX_LOCKED_MEMORY | 最大锁定内存大小。如果在elaticsearch.yml中配置了bootstrap.memory.lock，那么该参数需要设置成"unlimited"。你必须设置ES_HEAP_SIZE的值。 |
|  |  |

