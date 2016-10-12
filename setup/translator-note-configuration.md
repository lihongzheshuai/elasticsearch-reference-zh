# 译者注

## 关于max\_file\_descriptors 信息获取

在本节中提到了可以通过[Nodes Info API](/cluster-apis/nodes-info.md)来获取最大文件数，这里笔者补充一下本地实验的响应信息：

```bash
curl localhost:9200/_nodes/stats/process?pretty
{
  "cluster_name" : "elasticsearch",
  "nodes" : {
    "J3YNgJtqQmyj__V_kQMTUw" : {
      "timestamp" : 1476237913761,
      "name" : "Snowbird",
      "transport_address" : "127.0.0.1:9300",
      "host" : "127.0.0.1",
      "ip" : [ "127.0.0.1:9300", "NONE" ],
      "process" : {
        "timestamp" : 1476237913761,
        "open_file_descriptors" : 169,
        "max_file_descriptors" : 1024000,
        "cpu" : {
          "percent" : 0,
          "total_in_millis" : 443060
        },
        "mem" : {
          "total_virtual_in_bytes" : 3780382720
        }
      }
    },
    "IdxNrXo3T6KAtLHgIy_onA" : {
      "timestamp" : 1476237913762,
      "name" : "Ariann",
      "transport_address" : "127.0.0.1:9301",
      "host" : "127.0.0.1",
      "ip" : [ "127.0.0.1:9301", "NONE" ],
      "process" : {
        "timestamp" : 1476237913762,
        "open_file_descriptors" : 164,
        "max_file_descriptors" : 1024000,
        "cpu" : {
          "percent" : 0,
          "total_in_millis" : 437690
        },
        "mem" : {
          "total_virtual_in_bytes" : 3773870080
        }
      }
    }
  }
}
```

从响应信息里可以看到，在笔者本地实验环境中。max\_file\_descriptors的值为1024000。

## 关于mlockall 信息获取

这里补充一下响应信息：

```bash
curl http://localhost:9200/_nodes/process?pretty
{
  "cluster_name" : "elasticsearch",
  "nodes" : {
    "J3YNgJtqQmyj__V_kQMTUw" : {
      "name" : "Snowbird",
      "transport_address" : "127.0.0.1:9300",
      "host" : "127.0.0.1",
      "ip" : "127.0.0.1",
      "version" : "2.4.1",
      "build" : "c67dc32",
      "http_address" : "127.0.0.1:9200",
      "process" : {
        "refresh_interval_in_millis" : 1000,
        "id" : 21384,
        "mlockall" : false
      }
    },
    "IdxNrXo3T6KAtLHgIy_onA" : {
      "name" : "Ariann",
      "transport_address" : "127.0.0.1:9301",
      "host" : "127.0.0.1",
      "ip" : "127.0.0.1",
      "version" : "2.4.1",
      "build" : "c67dc32",
      "http_address" : "127.0.0.1:9201",
      "process" : {
        "refresh_interval_in_millis" : 1000,
        "id" : 21444,
        "mlockall" : false
      }
    }
  }
}
```

可以看到，在笔者的实验环境中，mlockall的值是false。原因就是文中的提到了第一种情况，运行ES的用户没有权限。不过笔者的实验环境中并没有配置交换分区，也就无需关注这里的情况。

