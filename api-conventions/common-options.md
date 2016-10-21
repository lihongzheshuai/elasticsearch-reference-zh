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

支持到时间单位有: y(年)，M(月)，w(周)，d(天)，h(小时)，m(分钟)以及s(秒）。

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

也可以通过符号**来匹配不知道精确路径的字段。例如，我们可以通过下述请求返回每段的Lucene版本号：

```bash

```