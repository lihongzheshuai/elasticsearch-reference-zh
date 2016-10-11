# 设置

本部分内容包括如何配置和运行Elasticsearch。如果你还有没[下载Elasticsearch](https://www.elastic.co/downloads)，你可以阅读[安装文档。](#installation)

> 注意：也可以利用我们的仓库，通过apt或yum命令安装Elasticsearch。参见 [仓库（Repositories）](/setup/repositories.md)。

## 支持的平台

官方支持的操作系统和JVM列表矩阵参见：[支持矩阵](https://www.elastic.co/support/matrix)。Elasticsearch列表中的平台上进行过充分的测试，不过它也可能可以运行在其它平台上。

## 安装 {#installation}

在[下载](https://www.elastic.co/downloads/elasticsearch)并解压完最新版本后，Elasticsearch可通过如下命令运行：

```bash
$ bin/elasticsearch
```

在\*nix操作系统上，上述命令将会在前台运行程序。

### 以守护进程运行

可以通过添加 -d 参数，切换到后台运行模式:

```bash
$ bin/elasticsearch -d
```

### PID\(进程ID\)

Elasticsearch进程会在启动期将PID写入指定文件，便于后续方便的停止进程:

```bash
$ bin/elasticsearch -d -p pid 注1
$ kill `cat pid` 注2
```

> 注1：PID被写入名为pid的文件。
> 
> 注2：kill命令向PID值保存在pid文件的中的进程发送一个终止信号。

> 注意：针对[Linux](/setup/running-as-a-service-on-linux.md)和[Windows](/setup/running-as-a-service-on-windows.md)提供的启动脚本，可以帮助你启动和停止Elastisearch进程。





