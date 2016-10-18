# 译者注 补充样例

在本节中，官方文档概括的说明了很多有关多多索引的参数和支持的格式，但是没有给出具体的样例，可能会存在于后续的章节中，不过笔者这里还是根据理解自己先进行了一些实验。

当前集群中索引状如下：

```bash
curl -XGET 'localhost:9200/_cat/indices?v'
health status index     pri rep docs.count docs.deleted store.size pri.store.size 
green  open   lihongzhe   5   1          1            0      7.3kb          3.6kb 
green  open   bank        5   1       1000            0    885.3kb        442.6kb 
       close  close                                                               
green  open   customer    5   1          3            0     19.3kb          9.6kb 
```

## 跨多索引查询

**从lihongzhe和customer两个索引中查数据：**

```bash
curl -XGET 'localhost:9200/lihongzhe,customer/_search?q=*&pretty'
{
  "took" : 5,
  "timed_out" : false,
  "_shards" : {
    "total" : 10,
    "successful" : 10,
    "failed" : 0
  },
  "hits" : {
    "total" : 4,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "customer",
      "_type" : "external",
      "_id" : "AVenLFWSLAc7FsOe-vIu",
      "_score" : 1.0,
      "_source" : {
        "name" : "Jane Doe"
      }
    }, {
      "_index" : "customer",
      "_type" : "external",
      "_id" : "AVenLDXxLAc7FsOe-vIt",
      "_score" : 1.0,
      "_source" : {
        "name" : "Jane Doe"
      }
    }, {
      "_index" : "customer",
      "_type" : "external",
      "_id" : "1",
      "_score" : 1.0,
      "_source" : {
        "name" : "John Doe becomes Jane Doe"
      }
    }, {
      "_index" : "lihongzhe",
      "_type" : "one",
      "_id" : "1",
      "_score" : 1.0,
      "_source" : {
        "name" : "lihongzhe"
      }
    } ]
  }
}
```

**使用\*n\*查询，排除不想检索的索引bank:**

即只从lihongzhe索引检索数据

```bash
curl -XGET 'localhost:9200/*n*,-bank/_search?q=*&pretty'
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "lihongzhe",
      "_type" : "one",
      "_id" : "1",
      "_score" : 1.0,
      "_source" : {
        "name" : "lihongzhe"
      }
    } ]
  }
}
```

**ignore_unavailable**参数：

查询关闭的索引close，分别设置ignore_unavailable参数值为true和false：

```bash
curl -XGET 'localhost:9200/close/_search?q=*&ignore_unavailable=true&pretty'
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 0,
    "successful" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 0,
    "max_score" : 0.0,
    "hits" : [ ]
  }
}
```

```bash
curl -XGET 'localhost:9200/close/_search?q=*&ignore_unavailable=false&pretty'
{
  "error" : {
    "root_cause" : [ {
      "type" : "index_closed_exception",
      "reason" : "closed",
      "index" : "close"
    } ],
    "type" : "index_closed_exception",
    "reason" : "closed",
    "index" : "close"
  },
  "status" : 403
}

```

此场景下，默认是false。

**allow_no_indices**参数：

测试方法同上：

```bash
curl -XGET 'localhost:9200/aa*/_search?q=*&allow_no_indices=false&pretty'
{
  "error" : {
    "root_cause" : [ {
      "type" : "index_not_found_exception",
      "reason" : "no such index",
      "resource.type" : "index_or_alias",
      "resource.id" : "aa*",
      "index" : "aa*"
    } ],
    "type" : "index_not_found_exception",
    "reason" : "no such index",
    "resource.type" : "index_or_alias",
    "resource.id" : "aa*",
    "index" : "aa*"
  },
  "status" : 404
}


```

```bash
curl -XGET 'localhost:9200/aa*/_search?q=*&allow_no_indices=true&pretty'
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 0,
    "successful" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 0,
    "max_score" : 0.0,
    "hits" : [ ]
  }
}

```

**expand_wildcards**参数：

由于close的索引不支持检索数据，因此我们通过索引信息查看接口来进行对比。

```bash
curl -XGET 'localhost:9200/*o*?expand_wildcards=open&pretty'
{
  "lihongzhe" : {
    "aliases" : { },
    "mappings" : {
      "one" : {
        "properties" : {
          "name" : {
            "type" : "string"
          }
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1476712848214",
        "number_of_shards" : "5",
        "number_of_replicas" : "1",
        "uuid" : "KPcx-IGaQYaaxqIFJ-kIIA",
        "version" : {
          "created" : "2040199"
        }
      }
    },
    "warmers" : { }
  },
  "customer" : {
    "aliases" : { },
    "mappings" : {
      "external" : {
        "properties" : {
          "age" : {
            "type" : "long"
          },
          "name" : {
            "type" : "string"
          }
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1475976303880",
        "number_of_shards" : "5",
        "number_of_replicas" : "1",
        "uuid" : "8OK4tDlKR3auJewEPIhwng",
        "version" : {
          "created" : "2040199"
        }
      }
    },
    "warmers" : { }
  }
}

```

```bash
curl -XGET 'localhost:9200/*o*?expand_wildcards=closed&pretty'
{
  "close" : {
    "aliases" : { },
    "mappings" : {
      "one" : {
        "properties" : {
          "name" : {
            "type" : "string"
          }
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1476798191897",
        "number_of_shards" : "5",
        "translog" : {
          "disable_flush" : "false"
        },
        "number_of_replicas" : "1",
        "uuid" : "RljsldRcSLC-ZhG_rCkjLQ",
        "version" : {
          "created" : "2040199"
        }
      }
    },
    "warmers" : { }
  }
}


```
