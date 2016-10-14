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

