# 仓库

对于APT和YUM安装包，我们提供了可用的仓库。不过我们只提供二进制包，没有提供源码包，因为该包是在Elasticsearch编译时附属产生的。

我们将不同主版本拆分到不同的urls下，为了避免错误的升级了某个主版本。对于所有2.x 发布版，使用2.x作为版本号，对于3.x.y系列使用3.x，以此类推。

我们使用PGP key [D88E42B4](https://pgp.mit.edu/pks/lookup?op=vindex&search=0xD27D666CD88E42B4)，Elasticsearch使用如下指纹码签名所有的包：

```
4609 5ACC 8548 582C 1A26 99A9 D27D 666C D88E 42B4
```

在此可查：[https://pgp.mit.edu/](https://pgp.mit.edu/)

## APT

下载并安装公开签名Key:

```bash
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

将仓库定义保存到/etc/apt/sources.list.d/elasticsearch-2.x.list:

```bash
echo "deb https://packages.elastic.co/elasticsearch/2.x/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-2.x.list
```

> 警告⚠️: 上面我们使用echo命令添加仓库地址。请不要使用add-apt-repository命令，因为该命令会同时添加deb-src配置，但是我们没有提供源码包。如果你添加了deb-src配置，你将会看到如下错误：
>
```
Unable to find expected entry 'main/source/Sources' in Release file (Wrong sources.list entry or malformed file)
```
> 只需要从/etc/apt/sources.list文件中删除deb-src配置，安装即可恢复正常。

执行apt-get update 命令使仓库出于就绪状态，然后可通过如下命令安装：

```bash
sudo apt-get update && sudo apt-get install elasticsearch
```

> 警告⚠️：如果存在两个关于同一个Elasticsearch仓库的配置，你会再执行apt-get update期间看到如下错误：
>
```
Duplicate sources.list entry https://packages.elastic.co/elasticsearch/2.x/debian/ ...`
```
> 请检查/etc/apt/sources.list.d/elasticsearch-2.x.list 文件中是否存在重复的配置，或者在/etc/apt/sources.list.d/文件下的文件以及/etc/apt/sources.list文件中定位重复配置。

配置Elasticsearch在操作系统启动是自动启动。如果你的系统是使用SysV初始化，你需要执行：

```bash
sudo update-rc.d elasticsearch defaults 95 10
```
如果你的系统使用systemd,则执行：

```bash
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
```

## YUM/DNF





