# 安装

Elasticsearch最低需要Java7版本。以本当编写时间为参考，推荐使用Oracle JDK 1.8.0\_73版本（译者注：当前最新版为1.8.0\_102）。Java的安装在不同平台下差异很大，因此这里我们不细说这部分内容。Oralce推荐的安装文档参见[Oracle的官网](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html) 。这里我只提一点，在你安装Elasticsearch之前，请通过以下命令检查你的Java版本\(然后确定是否需要安装或升级Java\)。

```bash
java -version
echo $JAVA_HOME
```

安装好Java以后，我们可以下载并运行Elasticsearch。安装包可从网站[www.elastic.co\/downloads](http://www.elastic.co/downloads)下载，该网站还包含其他已发布的历史版本。对于每个发布的版本，你可以选择使用zip包或tar包，以及deb或rpm格式的包。

