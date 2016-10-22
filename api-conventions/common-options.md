# 通用选项

下面的选项适用于所有的REST API。

## 更好的结果展现

当在任何请求上添加参数 ?pretty=true 时，返回的JSON数据会被以更便于阅读的格式输出(注意仅用于调试！)。另一个选项是设置 ?format=yaml，这会使返回结果以更容易阅读的yaml格式输出。

## 便于阅读的输出格式

统计数据的结果会以更便于人阅读的格式（例如："exist_time": "1h" 或 "size":"1kb"）或更便于计算的格式返回(例如:"exists_time_in_millis": 3600000 或 "size_in_bytes": 1024)。可以通过参数 ?human=false关闭返回便于阅读的格式。这种情况用于返回值被监控工具使用而不是人来阅读。默认的human参数的值是false。

## 日期计算

大多数接受日期格式的参数都支持日期计算，例如：[范围查询](/query-dsl/term-level-query/range-query.md)中的 gt 和 lt参数，以及 [日期范围聚合](/aggregations/bucket-aggregations/date-range-aggregation.md)中的from和to参数。

表达式以一个基准日期开始，可以是now或是以||结尾的日期字符串。该基础日期后可添加数学表达式：

* +1h - 加一个小时
* -1d - 减一天
* /d - 向下精确到最近的天

支持到[时间单位](#时间单位)有: y(年)，M(月)，w(周)，d(天)，h(小时)，m(分钟)以及s(秒）。

举几个例子：

<table>
<thead>
<tr><td>表达式</td><td>描述</td></tr>
</thead>
<tr>
<td>now+1h</td><td>当前时间加一小时，以毫秒计算</td>
</tr>
<tr>
<td>now+1h+1m</td><td>当前时间加一小时一分钟，以毫秒计算</td>
</tr>
<tr>
<td>now+1h/d</td><td>当前时间加一小时后，向下精确到最近的天</td>
</tr>
<tr>
<td>2015-01-01||+1M/d</td><td>2015-01-01加一个月，然后向下精确到最近的天</td>
</tr>
</table>

## 响应过滤器

所有的REST API都可以使用filter_path参数来缩减Elasticsearch的响应值。该参数的值一组以逗号分隔的，.格式的列表：

```bash
curl -XGET 'localhost:9200/_search?pretty&filter_path=took,hits.hits._id,hits.hits._score'
{
  "took" : 3,
  "hits" : {
    "hits" : [
      {
        "_id" : "3640",
        "_score" : 1.0
      },
      {
        "_id" : "3642",
        "_score" : 1.0
      }
    ]
  }
}
```
该参数还支持符号*进行模糊匹配字段名：

```bash
curl -XGET 'localhost:9200/_nodes/stats?filter_path=nodes.*.ho*'
{
  "nodes" : {
    "lvJHed8uQQu4brS-SXKsNA" : {
      "host" : "portable"
    }
  }
}
```

也可以通过符号**来匹配不知道精确路径的字段。例如，我们可以通过下述请求返回每段(segment)的Lucene版本号：

```bash
curl 'localhost:9200/_segments?pretty&filter_path=indices.**.version'
{
  "indices" : {
    "movies" : {
      "shards" : {
        "0" : [ {
          "segments" : {
            "_0" : {
              "version" : "5.2.0"
            }
          }
        } ],
        "2" : [ {
          "segments" : {
            "_0" : {
              "version" : "5.2.0"
            }
          }
        } ]
      }
    },
    "books" : {
      "shards" : {
        "0" : [ {
          "segments" : {
            "_0" : {
              "version" : "5.2.0"
            }
          }
        } ]
      }
    }
  }
}
```
注意到，Elasticsearch有时会直接返回字段的原始值，例如_source 字段。如果你想要过滤_source字段，你需要组合已有的_source参数(详见[Get API文档](/document-apis/get-api.md))和filter_path参数：

```bash
curl -XGET 'localhost:9200/_search?pretty&filter_path=hits.hits._source&_source=title'
{
  "hits" : {
    "hits" : [ {
      "_source":{"title":"Book #2"}
    }, {
      "_source":{"title":"Book #1"}
    }, {
      "_source":{"title":"Book #3"}
    } ]
  }
}
```

## 扁平化设置(Flat Setting)

flat_setting参数会影响配置列表的展示效果。当flat_setting设置为true的时候，配置列表以扁平格式(flat format)输出：

```javascript
{
  "persistent" : { },
  "transient" : {
    "discovery.zen.minimum_master_nodes" : "1"
  }
}
```
当flat_setting设置为false的时候，配置列表以更易于人阅读的格式输出：

```javascript
{
  "persistent" : { },
  "transient" : {
    "discovery" : {
      "zen" : {
        "minimum_master_nodes" : "1"
      }
    }
  }
}
```
默认值是false。

## 参数

Rest参数（当使用HTTP时，映射到HTTP URL的参数）按照约定使用下划线的格式。

## 布尔值

所有REST API的参数（包括请求参数和JSON体）支持将值:false,0,no 和 off解析为"false"语义。其他所有的值都认为是"true"。注意，这个规则不适用于文档内部的布尔型字段。

## 数值类型值

所有REST API支持把数值类型的参数当做字符串(string)以及原生JSON数字类型处理。

## 时间单位

不论何时，当需要指定持续时间的时候，比如参数timeout，必须指定单位，如2d代表2天。支持的单位有：

|单位|含义|
|---|---|
|y|年|
|M|月|
|w|周|
|d|日|
|h|小时|
|m|分钟|
|s|秒|
|ms|毫秒|

## 数据大小单位

当需要指定数据大小的时候，例如，当设置缓冲区大小参数的时候，必须指定单位，如10kb代表10千字节。支持的单位有：

|单位|含义|
|---|---|
|b|字节|
|kb|千字节|
|mb|兆字节|
|gb|千兆字节|
|tb|兆兆字节|
|pb|千兆兆字节|

## 距离单位

当需要指定距离的时候，例如，在[Geo Distance Query](/query-dsl/geo-queries/geo-distance-query.md)中需要设置distance参数的时，默认单位是米。也可以使用其他单位，例如1km或2mi（2英里）。

所有支持的单位有：

|单位名称|缩写|
|---|---|
|英里|mi或miles|
|码|yd或yards|
|尺|ft或feet|
|英寸|in或inch|
|千米|km或kilometers|
|米|m或meters|
|厘米|cm或centimeters|
|毫米|mm或millimeters|
|海里|NM,nmi或nauticalmiles|

在[Geohash Cell Query](/query-dsl/geo-queries/geohash-cell-query.md)中的精度(precision)参数支持上述距离单位，但是如果没有指定单位，精度被解释为geohash的长度。

## 模糊匹配

某些查询条件和接口通过fuzziness参数支持模糊匹配。fuzziness参数是上下文敏感的，也就是说它依赖于被查询字段的类型。

### 数值，日期和IPv4字段

当时查询数值，日期和IPv4字段时，fuzziness被解释为+/-边界。其行为就类似于[范围查询](/query-dsl/term-level-query/range-query.md)：

```
-fuzziness <= field value <= +fuzziness
```

fuzziness参数需要指定数值类型的值，例如2或2.0。日期字段接受长整形参数作为毫秒数，不过也支持字符串类型的值，如[时间单位](#时间单位)部分介绍的1h。ip字段接受长整形或其他IPv4地址（也会被转换成长整形）。

### 字符串字段

当查询字符串类型字段时，fuzziness被解释为[Levenshtein Edit Distance(编辑距离)](https://en.wikipedia.org/wiki/Levenshtein_distance) - 将一个字符串转变成另一个字符串所需要修改的字符数。

fuzziness参数可指定如下值：

0,1,2
Levenshtein Edit Distance允许的最大数(或编辑数 number of edits)
