---
title: MongoDB 学习笔记(四)：索引
date: 2016-10-26 21:52:38
tags: MongoDB
categories: [Web后端]
---

索引可以用来优化查询，而且在某些特定类型的查询中，索引是必不可少的。
为集合选择合适的索引是提高性能的关键。


## 索引介绍

官方文档：[Indexes](https://docs.mongodb.com/manual/indexes/)

不使用索引的查询称为*全表扫描*，服务器必须查找完一整本书才能找到查询结果。通常来说，应该尽量避卖弄全表扫描，因为对于大集合来说，全表扫描的效率非常低。

例如：
```javascript
> for(i=0; i<100000;i++){
... db.users.insert(
... {"i":i,
... "usersname":"user"+i,
... "age":Math.floor(Math.random()*120),
... "created":new Date()}
... );
... }
```
我们在这个集合上做查询，可以使用`explain()`函数查看MongoDB在执行查询过程中做的事情。
```javascript
> db.users.find({"username":"user101"}).explain()
{
        "queryPlanner" : {
                "plannerVersion" : 1,
                "namespace" : "users.users",
                "indexFilterSet" : false,
                "parsedQuery" : {
                        "username" : {
                                "$eq" : "user101"
                        }
                },
                "winningPlan" : {
                        "stage" : "COLLSCAN",
                        "filter" : {
                                "username" : {
                                        "$eq" : "user101"
                                }
                        },
                        "direction" : "forward"
                },
                "rejectedPlans" : [ ]
        },
        "serverInfo" : {
                "host" : "Ten-PC",
                "port" : 27017,
                "version" : "3.2.10",
                "gitVersion" : "79d9b3ab5ce20f51c272b4411202710a082d0317"
        },
        "ok" : 1
}
>
```

> 关于cursor.explain()
explain()在3.0版本发生了变化，主要是参数和输出格式的变化。
`db.collection.find().explain()`
参数verbose有三种模式：`queryPlanner`(默认)、`executionStats`和`allPlansExecution`，模式的区别在于返回的信息的数量不同。
详细请参考：[cursor.explain()](https://docs.mongodb.com/manual/reference/method/cursor.explain/)

### 创建索引
首先明确一下，`db.collection.ensureIndex()`自3.0版本开始已经不再使用。取而代之的是`db.collection.createIndex()`。
官方原话是：
> Deprecated since version 3.0.0: `db.collection.ensureIndex()` is now an alias for `db.collection.createIndex()`.
Use `db.collection.createIndex()` **rather than** `db.collection.ensureIndex()` to create new indexes.

定义：Creates indexes on collections.
`db.collection.createIndex(keys, options)`

例如：
```javascript
db.users.createIndex({"username":-1})
```
**复合索引**(compound index)
```javascript
> db.users.createIndex({"age":1, "username":1})
{
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 4,
        "numIndexesAfter" : 5,
        "ok" : 1
}
```

```javascript
> db.users.find({},{"_id":0, "i":0, "created":0})
{ "name" : "joe", "age" : 30 }
{ "rep" : 28 }
{ "name" : "tink", "rep" : 12 }
{ "y" : null }
{ "usersname" : "user0", "age" : 62 }
{ "usersname" : "user1", "age" : 52 }
{ "usersname" : "user2", "age" : 44 }
{ "usersname" : "user3", "age" : 51 }
{ "usersname" : "user4", "age" : 46 }
{ "usersname" : "user5", "age" : 34 }
{ "usersname" : "user6", "age" : 103 }
{ "usersname" : "user7", "age" : 109 }
{ "usersname" : "user8", "age" : 22 }
{ "usersname" : "user9", "age" : 39 }
{ "usersname" : "user10", "age" : 113 }
{ "usersname" : "user11", "age" : 0 }
{ "usersname" : "user12", "age" : 110 }
{ "usersname" : "user13", "age" : 101 }
{ "usersname" : "user14", "age" : 40 }
{ "usersname" : "user15", "age" : 99 }
Type "it" for more
```

**索引嵌套文档**
```javascript
db.user.createIndex({"loc.city":1})
```
