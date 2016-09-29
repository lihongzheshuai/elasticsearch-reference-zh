# 安装

Elasticsearch最低需要Java7版本。以本当编写时间为参考，推荐使用Oracle JDK 1.8.0\_73版本（译者注：当前最新版为1.8.0\_102）。Java的安装在不同平台下差异很大，因此这里我们不细说这部分内容。Oralce推荐的安装文档参见[Oracle的官网](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html) 。这里我只提一点，在你安装Elasticsearch之前，请通过以下命令检查你的Java版本\(然后确定是否需要安装或升级Java\)。

```bash
java -version
echo $JAVA_HOME
```

安装好Java以后，我们可以下载并运行Elasticsearch。安装包可从网站[www.elastic.co\/downloads](http://www.elastic.co/downloads)下载，该网站还包含其他已发布的历史版本。对于每个发布的版本，你可以选择使用zip包或tar包，以及deb或rpm格式的包。为了简化，这里我们使用tar包。

通过下面的命令下载Elasticsearch 2.4.1的安装包（Windows用户请下载zip版本）：

```bash
curl -L -O https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.4.1/elasticsearch-2.4.1.tar.gz
```

然后解压（Windows用户解压zip包）：

```bash
tar -xvf elasticsearch-2.4.1.tar.gz
```

这将会在当前文件夹下生成大量的文件和文件夹。然后我们进入bin目录：

```bash
cd elasticsearch-2.4.1/bin
```

现在我们已经准备好启动节点和一个集群了（Windows用户需要运行elasticsearch.bat脚本）：

```
./elasticsearch
```

如果一切正常，你将看到如下输出信息：

```
./elasticsearch
[2014-03-13 13:42:17,218][INFO ][node           ] [New Goblin] version[2.4.1], pid[2085], build[5c03844/2014-02-25T15:52:53Z]
[2014-03-13 13:42:17,219][INFO ][node           ] [New Goblin] initializing ...
[2014-03-13 13:42:17,223][INFO ][plugins        ] [New Goblin] loaded [], sites []
[2014-03-13 13:42:19,831][INFO ][node           ] [New Goblin] initialized
[2014-03-13 13:42:19,832][INFO ][node           ] [New Goblin] starting ...
[2014-03-13 13:42:19,958][INFO ][transport      ] [New Goblin] bound_address {inet[/0:0:0:0:0:0:0:0:9300]}, publish_address {inet[/192.168.8.112:9300]}
[2014-03-13 13:42:23,030][INFO ][cluster.service] [New Goblin] new_master [New Goblin][rWMtGj3dQouz2r6ZFL9v4g][mwubuntu1][inet[/192.168.8.112:9300]], reason: zen-disco-join (elected_as_master)
[2014-03-13 13:42:23,100][INFO ][discovery      ] [New Goblin] elasticsearch/rWMtGj3dQouz2r6ZFL9v4g
[2014-03-13 13:42:23,125][INFO ][http           ] [New Goblin] bound_address {inet[/0:0:0:0:0:0:0:0:9200]}, publish_address {inet[/192.168.8.112:9200]}
[2014-03-13 13:42:23,629][INFO ][gateway        ] [New Goblin] recovered [1] indices into cluster_state
[2014-03-13 13:42:23,630][INFO ][node           ] [New Goblin] started
```

不关注过多细节，可以看到，我们名为“New Goblin”（在你本地可能会有不同的名字）的节点已经启动，并且选举它自己作为此单一集群主节点。此时此刻，你无需关心主节点的含义。重点是我们已经启动了集群中的一个节点。

如之前所说，我们可以指定集群或节点的名字。可在启动Elasticsearch时在命令输入如下命令：

```
./elasticsearch --cluster.name my_cluster_name --node.name my_node_name
```

同时注意到输出中关于http信息的部分，其显示了可访问节点的HTTP地址（192.168.8.112）和端口号（9200）。Elasticsearch默认使用9200端口暴露REST API。

