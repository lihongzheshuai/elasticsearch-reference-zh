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
* /d - 截断精确到天

支持到时间单位有: y(年)，M(月)，w(周)，d(天)，h(小时)，m(分钟)以及s(秒）。

举几个例子：

<table>
<thead>
<tr><td>表达式</td><td>描述</td></tr>
</thead>
<tr>

<td>&lt;logstash-{now/d}&gt;</td><td>logstash-2024.03.22</td>

</tr>

<tr>

<td>&lt;logstash-{now/M}&gt;</td><td>logstash-2024.03.01</td>

</tr>

<tr>

<td>&lt;logstash-{now/M{YYYY.MM}}&gt;</td><td>logstash-2024.03</td>

</tr>

<tr>

<td>&lt;logstash-{now/M-1M{YYYY.MM}}&gt;</td><td>logstash-2024.02</td>

</tr>

<tr>

<td>&lt;logstash-{now/d{YYYY.MM.dd | +12:00}}&gt;</td><td>logstash-2024.03.23</td>

</tr>

</table>


