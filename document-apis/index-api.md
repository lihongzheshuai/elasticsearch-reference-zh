# 索引API

所以API可增加或修改指定索引中JSON格式的文档，使其可以被检索到。下面列子展示了如何向索引"twitter"中的"tweet"类型下，以id 1 插入JSON格式的文档：

```bash
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```
上述操作的结果是：

```bash
{
    "_shards" : {
        "total" : 10,
        "failed" : 0,
        "successful" : 10
    },
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "created" : true
}
```
_shards部分提供了索引操作所涉及到的副本和分块信息。

* total - 指索引操作分布在多少个分块上（主分块和副本分块）。
* successful - 操作成功的分块数
* failures - 在索引操作在某个副本上执行失败时，包含副本相关错误信息的数组。

一个索引操作是成功的，successful的值至少是1。

> 注意：当一个索引操作成功返回的时候，副本分块可能尚未启动（默认需要达到要求的数量）。在这种情况下，total的值会等于索引设置的所有的分块数量，而successful的值可能会等于已启动的分块的数量（主分块加副本）。此时没有失败的分块，failed的值是0。

## 自动创建索引

如果之前索引不存在，那么索引操作会自动创建索引（可参考[创建索引API](/indices-apis/create-index.md)来手动创建索引），同时如果事先不存在类型映射不那么也会自动动态创建（可参考[设置映射](/indices-apis/put-mapping.md)API来手动创建类型映射）。

映射本身是非常灵活自由的。新的字段和对象会自动的随着指定的类型添加到映射定义中。更多关于映射定义的信息科参考[映射](/mapping/README.md)章节。

可以通过将所有节点的配置文件中的选项action.auto_create_index的值设置为false来关闭索引自动创建功能。自动创建映射的功能可通过将选项index.mapper.dynamic的值设置为false来关闭（或者在指定索引上配置）。

自动创建索引功能可包含基于模式匹配的黑/白名单，例如，将选项action.auto_create_index的值设置为+aaa*,-bbb*,+ccc*,-*（+号代表允许，-号代表禁止）。

## 版本

每个索引的都有一个版本号。版本号会作为索引请求返回值的一部分返回。当指定version参数的时候，索引接口可选择弃用[乐观并发控制( optimistic concurrency control)](http://en.wikipedia.org/wiki/Optimistic_concurrency_control)这会控制操作所针对文档的版本。一个使用版本控制的很好的例子是执行一个读后更新(read-then-update)的事务。指定从最初读取的文档中的版本(version)可确保在这段时间内不会发生任何变化(当读取为了更新时，推荐设置preference的值为_primary)。例如：

```bash
curl -XPUT 'localhost:9200/twitter/tweet/1?version=2' -d '{
    "message" : "elasticsearch now has versioning support, double cool!"
}'
```

**注意**：版本是完全实时的，不会受准实时的查询操作的影响。如果没有指定版本，那么不会针对操作进行版本检查。

默认情况下使用内部版本号，从1开始并且每次更新、删除操作增加一个版本。也可以选择外部提供版本号（例如，在数据库中维护版本号）。如果想启用该功能，需要将version_type的值设置为external。版本号的值必须是数字long型的，大于等于0且小于9.2e+18。当使用外部版本号时，系统不会检查版本号是否相等，而只是要求给定的版本号是否大于当前文档的版本号。如果大于，文档将会被索引并且使用新的版本号。如果给定的版本号小于等于已存储文档的版本号，将会报版本冲突的错误，索引操作失败。

一个附加的好处是，不需要再严格维护对源数据库执行异步索引操作的执行顺序，只需要使用源数据库中的版本号即可。甚至是像从一个数据库来更新Elasticsearch索引这种简单的场景也可以通过使用外部版本号来简化，因为哪怕不论由于何种原因索引操作顺序混乱，只有最新的版本会被使用。

（**译者注**：如果对于该部分的内容不是很理解，可参考《Elasticsearch权威指南》中的[版本控制](http://es.xiaoleilu.com/030_Data/40_Version_control.html)部分，有具体的例子进行说明。）

### 版本类型

除了上面介绍的内部(internal)和外部(external）版本类型，Elasticsearch还支持用于特定场景的版本类型。下面是对于不同版本类型及其语义的简介。

内部(internal)

    仅当给定的版本号与已存储文档的版本号完全一致时才索引文档。

外部(external) 或 external_gt

    仅当给定的版本号大于已存储文档的版本号或文档不存在时索引文档。将使用给定的版本号作为新存储文档的版本号。给定的版本号必须是非负long型数字。

external_gte

    仅当给定的版本大于或等于已存储文档的版本号是索引文档。如果文档不存在，操作也会成功。将使用给定的版本号作为新存储文档的版本号。给定的版本号必须是非负long型数字。

**注意：**external_gte版本类型针对特殊的用户场景，需要小心使用。如果错误使用，可能会导致丢失数据。另外还有一个不推荐使用的选项force, 因为该选项可能会导致主分块和副本分块不一致。

## 操作类型

索引操作还支持op_type参数，该可参数可用来将允许"存在就更新(put-if-absent)"的操作强制指定为创建(create)操作。当时指定create值的时候，如果文档已经存在，索引操作将会失败。

下面是一个使用op_type参数的例子：

```bash
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1?op_type=create' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```

另一种使用create操作类型的方式是使用如下url：

```bash
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1/_create' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```

## ID自动生成

索引操作支持不指定ID。在这种情况下，id会自动生成。另外，参数op_type会被自动设置成值create。下面是一个例子(注意到使用POST代替PUT请求):

```bash
$ curl -XPOST 'http://localhost:9200/twitter/tweet/' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```

上述索引操作的返回值是：

```json
{
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "6a8ca01c-7896-48e9-81cc-9f70661fcb32",
    "_version" : 1,
    "created" : true
}
```

## 路由
``
默认情况下，分块分配 - 或称作路由 - 是通过文档id的hash值控制的。为了更精确的控制，可以在每个操作中通过参数routing直接指定传入路由所用hash函数的入参。例如：

```bash
$ curl -XPOST 'http://localhost:9200/twitter/tweet?routing=kimchy' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```

在上述例子中，文档"tweet"根据参数routing的值"kimchy"进行hash计算确定分块路由规则。

当设置明确的映射时，字段_routing可被选择用来指导索引操作，从而将路由值从文档本身分离出来。这会导致一部分文档解析的消耗（非常小）。如果定义了_routing映射并设置为required，那么当没有提供routing值的时候操作将会失败。

## 父&子

当索引的时候，子文档可以指定其父文档。例如：

```bash
curl -XPUT localhost:9200/blogs/blog_tag/1122?parent=1111 -d '{
    "tag" : "something"
}'
```

当索引子文档的时候，路由值会被自动的设置成与其父文档一致，除非使用routing参数显式指定路由值。

## 时间戳

> 警告⚠️：在版本2.0.0-beta2中已经废弃。_timestamp字段已经废弃。现在使用[date](/mapping/field-datatypes/date-datatype.md)字段代替，并显式赋值。

文档在索引时可添加时间戳(timestamp)。可通过参数timestamp设置时间戳的值。例如：

```bash
$ curl -XPUT localhost:9200/twitter/tweet/1?timestamp=2009-11-15T14%3A12%3A12 -d '{
    "user" : "kimchy",
    "message" : "trying out Elasticsearch"
}'
```

如果没有在外部或者_source中指定时间戳的值，那么时间戳的值会被制动设置成文档被索引时的时间。更多信息可参见[_timestamp映射页面](/mapping/meta-field/_timestamp-field.md)。

## TTL

> 警告⚠️：在2.0.0-beta2中废弃。当前的_ttl实现已经废弃并会在未来的版本中被不同的实现替代。

文档在索引时可指定ttl(存活时间 time to live)。过期的文档会被自动删除。文档的过期时间可通过相对于文档时间戳(timestamp）的ttl时间指定，也就说可以相对于索引时间也可以相对于给定的时间戳时间。ttl的值必须是正数，可以是数字（毫秒数）也是可以是下面列子中展示的任何正确的时间值：

```bash
