# 在Linux上以服务形式运行

为了以服务形式在系统上运行Elasticsearch，我们提供的安装包尽可能简单的让你在系统重启和升级期间启动和停止elasticsearch。

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

## Debian\/Ubuntu

由于使用了像update-rc.d 这样的debian包工具定义运行时需要的环境，因此debian包内置了所有需要的依赖。如你所想，初始化脚本为\/etc\/init.d\/elasticsearch。配置文件存放在\/etc\/default\/elasticsearch目录。

debian包默认不以服务的形式启动。目的是为了避免实例在没有配置好的情况下意外加入集群。在使用dpkg -i 命令安装后，你可以通过下列命令确保Elasticsearch 在系统启动后启动，然后再启动elasticsearch集群：

```bash
sudo update-rc.d elasticsearch defaults 95 10
sudo /etc/init.d/elasticsearch start
```

使用Debian8 或 Ubuntu 14 以后版本的用户需要配置systemd 而不是 update-rc.d，请参见 "[使用systemd](#使用systemd)"章节。

### 安装oracle版JDK

Elastics推荐使用Oracle 版的JDK。然而由于协议的原因Ubuntu 和 Debian 只能在包中内嵌OpenJDK。不过你可以很容易的安装oracle版的安装包。如果在你的Debian GNU\/Linux 系统上没有add-apt-repository 命令，你需要安装最新版的Debian Jessie 和 python-software-properties 工具包。

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
java -version
```

最后的命令式用于确认Oracle JDK已经成功安装。

## 基于RPM的安装包

### 使用chkconfig

某些基于RPM的安装包使用chkconfig命令来启用和禁用服务。初始化脚本默认存放在\/etc\/init.d\/elasticsearch，配置文件默认存放在\/etc\/sysconfig\/elasticsearch。与debian包一样，在默认情况下RPM在安装后也不启动，你需要手动执行如下命令：

```bash
sudo /sbin/chkconfig --add elasticsearch
sudo service elasticsearch start
```

## 使用systemd

像Debian Jessie ， Ubuntu 14 这样的系统以及许多SUSE派生的系统使用systemd 而不是 chkconfig 工具来注册服务，同时使用\/bin\/systemctl 命令来启动和停止服务（最少在新版本中如此，否则使用chkconfig命令）。基于rpm的系统配置文件同样存放在\/etc\/sysconfig\/elasticsearch中，基于deb包的系统，配置文件存放在\/etc\/default\/elasticsearch中。在安装RPM包之后，你需要修改systemd配置，然后启动Elasticsearch

```bash
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
sudo /bin/systemctl start elasticsearch.service
```

同时需要注意，修改\/etc\/sysconfig\/elasticsearch 配置文件中的MAX\_MAP\_COUNT没有任何效果，你需要修改\/usr\/lib\/sysctl.d\/elasticsearch.conf 配置文案件中的值才可以使其在启动时生效。\([译者注 3](/setup/translator-note-configuration.md)\)

