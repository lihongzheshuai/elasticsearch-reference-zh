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

  > 注意：从内核版本3.5-rc1开始，设置swappiness的值为0将会在OOM的时候杀死进程而不是进行内存交换。因此，你可以设置swappiness的值1，从而允许在紧急情况下进行内存交换。


* **mlockall**

  第三个选择是，在Linux\/Unix系统上使用[mlockall](http://opengroup.org/onlinepubs/007908799/xsh/mlockall.html)函数或者在Windows系统上使用[VirtualLock](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366895%28v=vs.85%29.aspx)函数来在RAM中锁定程序的地址空间，从而避免Elasticsearch程序的内存被交换出去。可以通过在config\/elasticsearch.yml配置文件中添加如下信息来配置：

  ```js
  bootstrap.memory_lock: true
  ```

  启动Elasticsearch后，你可以通过下述请求响应值中的mlockall部分来检查配置是否生效：（[译者注 2](/setup/translator-note-configuration.md)）

  ```bash
  curl http://localhost:9200/_nodes/process?pretty
  ```

  如果你看到mlockall的值是false，那意味着mlockall请求失败了。对于Linux\/Unix系统，最有可能的原因是运行Elasticsearch的用户没有锁定内存的权限。可以在启动Elasticsearch之前，通过root用户执行 ulimit -l unlimited 命令来赋予权限。

  另一个可能的原因是，临时文件夹\(通常是\/tmp\) 是通过noexec 选项挂载的。这可以通过在启动Elasticsearch时指定新的临时文件夹解决：

  ```bash
  ./bin/elasticsearch -Djna.tmpdir=/path/to/new/dir
  ```

  > 警告 ⚠️：如果锁定的内存大于可用内存，mlockall命令会导致JVM或shell 会话退出。


## Elasticsearch 配置

Elasticsearch的配置文件存放在ES\_HOME\/config 目录下。初始有两个配置文件， elasticsearch.yml 和 logging.yml，其中elasticsearch.yml 负责配置Elasticsearch的各种[组件](/modules/README.md)， logging.yml负责配置Elasticsearch日志。

配置文件是[YAML](http://www.yaml.org/)格式的。下面的例子说明如何修改所有依赖网络的组件所绑定的网络地址：

```js
network :
    host : 10.0.0.4
```

### 路径

在生产环境下，你可能需要修改数据和日志的存放地址：

```js
path:
  logs: /var/log/elasticsearch
  data: /var/data/elasticsearch
```

### 集群名称

同样了，不要忘记给你的生产集群指定一个名字，该名字用于自动发现和加入其它节点：

```js
cluster:
  name: <NAME OF YOUR CLUSTER>
```

请确保你不要在不同的环境下使用相同的集群名字，否则可能会导致节点加入错误的集群。例如，你可以使用logging-dev, logging-stage 和 logging-prod  分别命名开发，预发布和生成环境集群。

### 节点名称

你可以想要修改每个节点的默认名称，如改成主机名。默认情况下，在节点启东市，Elasticsearch会从3000个名字中随机选择一个。

```js
node:
  name: <NAME OF YOUR NODE>
```

机器的主机名可通过环境变量HOSTNAME获取。如果在你机器上只运行一个节点，你可以利用${...}符号将节点名设置成主机名：

```js
node:
  name: ${HOSTNAME}
```

## 配置风格

在内部，所以的配置都包裹在命名空间（namespaced）中。例如，上面的配置node.name。这意味着可以很轻松的支持配置格式，如[JSON](http://www.json.org/)。如果喜欢JSON格式配置，可以简单的将elasticsearch.yml 重命名为 elasticsearch.json ，并且添加内容：

```js
{
    "network" : {
        "host" : "10.0.0.4"
    }
}
```

同样，这也意味着可以方便通过ES\_JAVA\_OPTS或作为elasticsearch命令的参数的将配置外置，例如：

```
$ elasticsearch -Des.network.host=10.0.0.4
```

另外一个选择是用es.default. 前缀代替es. 前缀，使用es.default前缀意味着该配置只在配置文件中没有设置的时候生效。

再一个选择是可以在配置文件中使用${...} 符号，该符号将会被视作环境变量进行解析，例如：

```js
{
    "network" : {
        "host" : "${ES_NET_HOST}"
    }
}
```

另外，如果你不想讲配置保存在配置文件中，你可以使用变量${prompt.text}或${prompt.secret}，然后使用前端模式启动Elasticsearch。区别是，${prompt.secret} 配置会禁用回显，也就是不会值不会显示在你的终端中；${prompt.text} 使你可以看到你输入的值。例如：

```js
node:
  name: ${prompt.text}
```

当执行elasticsearch命令的时候，会提示你输入实际的值：

```bash
Enter value for [node.name]:
```

> 注意：如果你在配置文件中使用了${prompt.text}或${prompt.secret}变量，那么Elasticsearch无法通过后端模式启动。

### 索引配置

在集群中创建索引时可以提供独立的配置。例如，下面的例子创建了一个刷新间隔为5秒的索引，而非使用默认间隔（可以使用YAML或JSON格式）:

```bash
$ curl -XPUT http://localhost:9200/kimchy/ -d \
'
index:
    refresh_interval: 5s
'
```

也可以在节点级别进行索引配置，例如，可在elasticsearch.yml文件进行如下配置：

```js
index :
    refresh_interval: 5s
```

这意味着任何在该节点上创建的索引都会使用该刷新间隔为5秒的配置，而无需在创建索引是进行指定。另一方面，在索引级别上的配置会覆盖节点上配置。当然，你同样可以采用折叠\("collapsed"\)格式完成上述配置，例如：

```bash
$ elasticsearch -Des.index.refresh_interval=5s
```

可以在[索引组件](/index-modules/README.md)部分查看到所有索引级别的配置。

### 日志

Elasticsearch使用内部的日志工具而没有使用[log4j](http://logging.apache.org/log4j/1.2/)。它力图使用[YAML](http://www.yaml.org/)配置来简化log4j配置，并且日志的配置文件为config\/logging.yml。

