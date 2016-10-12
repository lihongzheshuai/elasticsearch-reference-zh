# 配置

## 环境变量

Elasticsearch会使用脚本中内置的JAVA\_OPTS变量的值作为JVM的启动参数。其中最重要几个配置包括-Xmx，控制进程所允许使用的最大内存和-Xms，控制进程分配的最小内存（一般来说，给进程分配的内存越大越好）。

大多数情况下，推荐保留JAVA\_OPTS的默认值不变，而使用ES\_JAVA\_OPTS环境变量来设置\/修改 JVM的设置或参数。

ES\_HEAP\_SIZE环境变量用来设置Elasticsearch java进程的堆内存的大小。该变量会将最大和最小值设置成相同的值，不过也可以通过变量ES\_MIN\_MEM\(默认值为256m\)和ES\_MAX\_MEM\(默认值为1g\)，分别指定最小值和最大值（不推荐这么做）。

推荐将最小和最大内存设置成相同的值，并开启 mlockall 。

## 系统配置

### 文件描述符

> 注意：该部分内容仅适用于Linux和macOS用户，如果你在Windows上运行Elasticsearch可以忽略本部分内容。在Windows上，JVM使用的[API](https://msdn.microsoft.com/en-us/library/windows/desktop/aa363858(v=vs.85)仅受可用资源的限制。

确认增大所在机器（或者运行Elasticsearch的用户）的可打开文件描述符的数量。推荐将其设置为32k，甚至64k。

如果想要测试程序可以打开的文件数，可以在启动时设置参数 -Des.max-open-files 的值为 true。该参数将在启动期打印出程序最大可打开的文件数目。

另外，可以在每个节点上通过[Nodes Info API ](/cluster-apis/nodes-info.md)来获取max\_file\_descriptors 的值: [\(译者注 1\)](/setup/translator-note-configuration.md)

```bash
curl localhost:9200/_nodes/stats/process?pretty
```

### 虚拟内存

Elasticsearch 默认使用 [hybrid mmapfs \/ niofs](/index-modules/store.md) 文件系统来存储索引。操作系统默认允许的mmap 数量一般很低，这可能会导致内存溢出异常。在Linux上，你可以用root用户执行下面命令以提高该限制：

```bash
sysctl -w vm.max_map_count=262144
```

如果想要此修改永久生效，可以修改\/etc\/sysctl.conf配置文件中vm.max\_map\_count 的值。

> 注意：如果你是通过.deb或.rpm包安装的Elasticsearch，那么该配置会被自动修改。可以通过sysctl vm.max\_map\_count 命令来验证。

### 内存配置

大多数操作系统都会使用尽可能多的内存作为文件系统的缓存，并且积极的交换冲未使用的应用内存，这可能会导致Elasticsearch的程序也被交换。交换对性能和节点稳定性非常不利，所以不管任何损耗都应该避免。

有三个选择：

* **关闭交换分区**

  最简单的选择就是直接关闭分区。通常情况下，Elasticsearch都是沙箱环境中的唯一服务，并且它的内存使用情况由        ES\_HEAP\_SIZE环境变量控制。因此不再需要交换分区。

  在Linux上，你可以通过执行sudo swapoff -a 命令来关闭交换分区。如果想要永久生效，你需要修改\/etc\/fstab文件，注释掉所有含有swap 字符的行。

  在Windows上，你可以通过System Properties → Advanced → Performance → Advanced → Virtual memory 来关闭页面文件。

* **配置交换分区**

  第二个选择是保证vm.swappiness的值设置为0。这会降低内核进行内存交换的倾向性，并且在正常情况下不会进行内存交换，而是只会再紧急情况下才进行内存交换。


