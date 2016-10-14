# 在Windows上以服务形式运行

Windows用户可以在没有用户干预的情况下实现Elasticsearch的以服务方式在后台运行或是在系统启动时自动运行。可通过bin目录下的service.bat脚本完成服务的安装，移除，管理或者服务配置以及临时启动、停止服务。完全通过命令行实现：

```bash
c:\elasticsearch-2.4.1\bin>service

Usage: service.bat install|remove|start|stop|manager [SERVICE_ID]
```

该脚本在所执行操作后需要参数service id\(在安装多个Elasticsearch服务时很有用。\)

支持的操作有：

| 命令 | 描述 |
| --- | --- |
| install | 安装Elasticsearch 服务 |
| remove | 移除已安装的Elasticsearch服务（会停止已启动的服务） |
| start | 启动Elasticsearch服务（如果已安装） |
| stop | 停止Elasticsearch服务\(如果已启动\) |
| manager | 启动一个图形界面管理已安装的服务 |

需要注意的是，在服务的安装阶段会拷贝环境变量的值，并在服务的整个生命周期中有效。也就是说，在服务安装后修改环境变量的值将不会生效，除非你重新安装服务。

根据安装的JDK\/JRE版本\(通过JAVA\_HOME指定\)，会对应安装64位或32位服务。这部分信息在安装时可见:

```bash
c:\elasticsearch-{version}bin>service install
Installing service      :  "elasticsearch-service-x64"
Using JAVA_HOME (64-bit):  "c:\jvm\jdk1.8"
The service 'elasticsearch-service-x64' has been installed.
```

> 注意：尽管Elasticsearch服务可以使用JRE，不过由于JRE使用的是客户端模式的VM（与服务端模式JVM相对，服务端模式对长期运行的应用提供了更好的性能支持。），因此我们不推荐使用JRE，并会给出警告。

## 自定义服务配置



