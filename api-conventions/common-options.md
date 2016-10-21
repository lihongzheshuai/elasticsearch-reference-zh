# 通用选项

下面的选项适用于所有的REST API。

## 更好的结果展现

当在任何请求上添加参数 ?pretty=true 时，返回的JSON数据会被以更便于阅读的格式输出(注意仅用于调试！)。另一个选项是设置 ?format=yaml，这会使返回结果以更容易阅读的yaml格式输出。

## 便于阅读的输出格式

统计数据的结果会以更便于人阅读的格式（例如："exist_time": "1h" 或 "size":"1kb"）或更便于计算的格式返回(例如:"exists_time_in_millis": 3600000 或 "size_in_bytes": 1024)。可以通过参数 ?human=false关闭返回便于阅读的格式。这种情况用于返回值被监控工具使用而不是人来阅读。默认的human参数的值是false。

## 日期计算

大多数接受日期格式的参数都支持日期计算，例如：[范围查询](/query-dsl/term-level-query/range-query.md)中的 gt 和 lt参数，以及 [日期范围聚合](/aggregations/bucket-aggregations/date-range-aggregation.md)中的from和to参数。