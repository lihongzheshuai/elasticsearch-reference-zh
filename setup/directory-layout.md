# 目录结构

Elaticsearch安装包的目录结构如下：

| 名字 | 描述 | 默认路径 | 对应环境变量 |
| --- | --- | --- | --- |
| home | Elasticsearch安装包的根目录 |  | path.home |
| bin | 包括用于启动节点的elasticsearch脚本在内的各种脚本| {path.home}/bin ||
|conf|配置文件目录，包含elasticsearch.yml等配置文件|{path.home}/config|path.conf|
|data|保存索引/分块等数据文件的目录地址。可指定多个位置|{path.home}/data|path.data|
|logs|日志文件地址|{path.home}/logs|path.logs|
|plugins|插件文件地址。每个插件将会保存在一个子目录里。|{path.home}/plugins|path.plugins|
