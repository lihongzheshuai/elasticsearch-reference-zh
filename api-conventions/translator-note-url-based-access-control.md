# 译者注 - 访问控制样例

在没开启访问控制时，效果如下：

```bash
curl 'localhost:9200/_mget?pretty' -d '{
     "docs" : [
         {
             "_index" : "bank",
             "_type" : "account",
             "_id" : "1"
         },
         {
             "_index" : "customer",
             "_type" : "external",
             "_id" : "1"
         }
     ]
 }'
{
  "docs" : [ {
    "_index" : "bank",
    "_type" : "account",
    "_id" : "1",
    "_version" : 1,
    "found" : true,
    "_source" : {
      "account_number" : 1,
      "balance" : 39225,
      "firstname" : "Amber",
      "lastname" : "Duke",
      "age" : 32,
      "gender" : "M",
      "address" : "880 Holmes Lane",
      "employer" : "Pyrami",
      "email" : "amberduke@pyrami.com",
      "city" : "Brogan",
      "state" : "IL"
    }
  }, {
    "_index" : "customer",
    "_type" : "external",
    "_id" : "1",
    "_version" : 8,
    "found" : true,
    "_source" : {
      "name" : "John Doe becomes Jane Doe"
    }
  } ]
}
```
开启后，

```
rest.action.multi.allow_explicit_index: false
```

效果如下：

