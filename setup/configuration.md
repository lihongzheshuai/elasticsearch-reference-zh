# 配置

## 环境变量

Elasticsearch会使用脚本中内置的JAVA\_OPTS变量的值作为JVM的启动参数。其中最重要几个配置包括-Xmx，控制进程所允许使用的最大内存和-Xms，控制进程分配的最小内存（一般来说，给进程分配的内存越大越好）。

大多数情况下，推荐保留JAVA\_OPTS的默认值不变，而使用ES\_JAVA\_OPTS环境变量来设置\/修改 JVM的设置或参数。

ES\_HEAP\_SIZE环境变量用来设置Elasticsearch java进程的堆内存的大小。该变量会将最大和最小值设置成相同的值，不过也可以通过变量ES\_MIN\_MEM\(默认值为256m\)和ES\_MAX\_MEM\(默认值为1g\)，分别指定最小值和最大值（不推荐这么做）。

推荐将最小和最大内存设置成相同的值，并开启 mlockall 。

## 系统配置

### 文件描述符

> 注意：该部分内容仅适用于Linux和macOS用户，如果你在Windows上运行Elasticsearch可以忽略本部分内容。在Windows上，JVM使用的[API](https://msdn.microsoft.com/en-us/library/windows/desktop/aa363858(v=vs.85)仅受可用资源的限制。

