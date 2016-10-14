# 目录结构

Elaticsearch安装包的目录结构如下：

| 名字 | 描述 | 默认路径 | 对应环境变量 |
| --- | --- | --- | --- |
| home | Elasticsearch安装包的根目录 |  | path.home |
| bin | 包括用于启动节点的elasticsearch脚本在内的各种脚本| {path.home}/bin ||
|conf|配置文件目录，包含elasticsearch.yml等配置文件|{path.home}/config|path.conf|
|data|节点上保存每个索引/分块数据文件的目录地址。可指定多个位置|{path.home}/data|path.data|
|logs|日志文件地址|{path.home}/logs|path.logs|
|plugins|插件文件地址。每个插件将会保存在一个子目录里。|{path.home}/plugins|path.plugins|
|repo|分块文件系统仓库地址。可以指定多个位置。文件系统仓库可被放置在这里指定的目录的任意子目录中|Not configured|path.repo|
|script|脚本文件地址|{path.conf}/scripts|path.scripts|

数据目录可以指定多个，以便将数据分散在多块磁盘位置上，不过属于同一块分块内的文件，将被写入相同的地址。可通过如下格式指定多个地址：

```javascript
---------------------------------
path.data: /mnt/first,/mnt/second
---------------------------------
```

也可以写成数组的形式：

```javascript
----------------------------------------
path.data: ["/mnt/first", "/mnt/second"]
----------------------------------------
```

> 小贴士：如果想要将分块写到多块磁盘上，请使用RAID驱动代替。

## 默认路径

下面是Elasticsearch使用的默认路径:

### deb和rpm包

|名字|描述|Debian/Ubuntu路径|RHEL/CentOS路径|
|---|---|---|---|
| home | Elasticsearch安装包的根目录 |/usr/share/elasticsearch| /usr/share/elasticsearch |
| bin | 包括用于启动节点的elasticsearch脚本在内的各种脚本| /usr/share/elasticsearch/bin|/usr/share/elasticsearch/bin|
|conf|配置文件目录，包含elasticsearch.yml等配置文件|/etc/elasticsearch|/etc/elasticsearch|
|conf|包含heap size, file description等环境变量的配置文件|/etc/default/elasticsearch|/etc/sysconfig/elasticsearch|
|data|节点上保存每个索引/分块数据文件的目录地址。可指定多个位置|/var/lib/elasticsearch/data|/var/lib/elasticsearch|
|logs|日志文件地址|/var/log/elasticsearch|/var/log/elasticsearch|
|plugins|插件文件地址。每个插件将会保存在一个子目录里。|/usr/share/elasticsearch/plugins|/usr/share/elasticsearch/plugins|

|repo|分块文件系统仓库地址。可以指定多个位置。文件系统仓库可被放置在这里指定的目录的任意子目录中|Not configured|path.repo|

|script|脚本文件地址|{path.conf}/scripts|path.scripts|
