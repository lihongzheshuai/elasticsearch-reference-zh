# 在Windows上以服务形式运行

Windows用户可以在没有用户干预的情况下实现Elasticsearch的以服务方式在后台运行或是在系统启动时自动运行。可通过bin目录下的service.bat脚本完成服务的安装，移除，管理或者服务配置以及临时启动、停止服务。完全通过命令行实现：

```bash
c:\elasticsearch-2.4.1\bin>service

Usage: service.bat install|remove|start|stop|manager [SERVICE_ID]
```



