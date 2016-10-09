# 探测你的数据

## 数据集\(Dataset\)样例

到目前为止我们已经了解一些基本概念，现在我们来处理一个更真实的数据集。我准备了一个虚构的有关客户银行账户信息的JSON文档。每个文档的结构都如下所示：

```
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```

出于好奇，我通过[www.json-generator.com](http://www.json-generator.com) 网站生成数据，因为所有的数据都是随机生成的，所以可以不必在意实际的值及其语义。

## 加载样例数据集

你可以从[这里](https://github.com/bly2k/files/blob/master/accounts.zip?raw=true)下载样例数据集。解压到当前目录，然后将其加载入我们的集群：

```
curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary "@accounts.json"
curl 'localhost:9200/_cat/indices?v'
```

响应信息为：

```
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
yellow bank    5   1       1000            0    424.4kb        424.4kb
```

说明我们已经成功的批量的索引了1000个文档进入名为bank的索引（在account type下）。

