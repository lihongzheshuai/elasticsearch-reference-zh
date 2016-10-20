# 索引名中的日期计算

在索引名中提供日期计算能力使得你可以方便的查询一个范围内的时间序列索引，无需查询出所有的时间序列索引然后过滤出结果，或是维护别名。限制检索的索引数目可以降低集群负载，提高执行性能。例如，如果你在每日的滚动日志中查询错误信息，你可以是用时间计算模板将查询范围限制在最近两天。

几乎所有包含索引参数的API都支持针对索引参数名的日期计算。

日期计算的索引名格式如下：

```
<static_name{date_math_expr{date_format|time_zone}}>
```

其中：

| 参数名 | 描述 |
| --- | --- |
| static\_name | 索引名中固定文本部分 |
| date\_math\_expr | 动态的日期计算表达式 |
| date\_format | 可选项，用于指定计算后日期的结果格式。默认是YYYY.MM.dd |
| time\_zone | 可选项，指定时区。默认是utc |

日期计算表达式必须用尖括号括起来，例如：

```bash
curl -XGET 'localhost:9200/<logstash-{now%2Fd-2d}>/_search' {
  "query" : {
    ...
  }
}
```

> 注意：在日期计算表达式中使用 \/ 必须转义成 %2F 使用。

下面的以当前时间为2024年3月22日为例，来说明日期计算表达式与其匹配到实际索引名字的关系：

| 表达式 | 解析后结果 |
| --- | --- |
| &lt;logstash-{now/d}&gt; | logstash-2024.03.22 |
| &lt;logstash-{now/M}&gt; | logstash-2024.03.01 |
| &lt;logstash-{now/M{YYYY.MM}}&gt; | logstash-2024.03 |
| &lt;logstash-{now/M-1M{YYYY.MM}}&gt; | logstash-2024.02 |
| &lt;logstash-{now/d{YYYY.MM.dd \| +12:00}}&gt; | logstash-2024.03.23 |

如果想要固定文本部分使用大括号{和}，需要通过斜杠\进行转义，例如：

* &lt;elastic{ON}-{now\/M}&gt;  解析为 elastic{ON}-2024.03.01

下面的例子展示了如何查询过去三天的Logstash的索引数据，假定使用的是Logstash默认的索引命名格式，logstash-YYYY.MM.dd

```bash
curl -XGET 'localhost:9200/<logstash-{now%2Fd-2d}>,<logstash-{now%2Fd-1d}>,<logstash-{now%2Fd}>/_search' {
  "query" : {
    ...
  }
}
```

