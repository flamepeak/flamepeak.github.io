---
title: MongoDB 学习笔记(二)：创建、更新和删除文档
date: 2016-10-25 17:52:30
tags: MongoDB
categories: [Web后端]
---
## 创建、更新和删除文档
### 插入
官方定义：
> [**db.collection.insert()**](https://docs.mongodb.com/manual/reference/method/db.collection.insert/#db.collection.insert): Creates a new document in a collection.

`insert`：单个插入
`batchInsert`：批量插入
如果要导入原始数据，可以使用命令行工具，如`mongoimport`，而不是批量插入。
如果在执行批量插入的过程中有一个文档插入失败，那么在文档之前的所有文档都会成功插入到集合中，而这个文档及以后的所有文档全部插入失败。

由于MongoDB只进行最基本的检查，所以插入非法数据很容易。因此，应该只允许信任的源连接数据库。

### 删除
`db.foo.remove({})`
**删除数据是永久的，不能撤销，也不能恢复。**
删除文档通常很快，但是要清空整个集合，使用`drop`直接删除集合会更快。

### 更新
官方文档定义：
> [**db.collection.update()**](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update)：Modifies a document in a collection.

一个常见的错误时查询条件匹配到了多个文档，然后更新时由于第二个参数的存在就产生重复的"_id"值。数据库会抛出错误，任何文档都不会更新。为避免这种情况，最好确保更新时总是指定一个唯一的文档，例如使用"_id"这样的键来匹配。

#### 使用修改器
通常文档只会有一部分更新。可以使用原子性的**更新修改器**(update modifier)，指定对文档中的某些字段进行更新。更新修改器是种特殊的键，用来指定复杂的更新操作，比如修改、增加或者删除键，还可能是操作数组或者内嵌文档。

**$set修改器**
`$set`用来指定一个字段的值。如果这个字段不存在，则创建它。
```javascript
db.users.update({"_id":ObjectId("580f860132248ee52c95818d")},{"$set":{"sex":"male"}})
```
`$unset`可以用来删除键
```javascript
 db.users.update({"_id":ObjectId("580f860132248ee52c95818d")},{"$unset":{"sex":1}})
```
在增加、修改或删除键时，应该使用`$修改器`。

**$inc 增加与减少**
`$inc`修改器用来增加已有的键的值，或者该键不存在那就创建一个。
`$inc`**只能**用于整型、长整型或双精度浮点型的值。

**数组修改器**
`$push`
如果数组已经存在，`$push`会向已有的数组**末尾**加入一个元素，要是没有就创建一个新的数组。
`$ne`
`$addToSet`
`$each`
The `$each` modifier is available for use with the `$addToSet` operator and the `$push` operator.
Use with the `$addToSet` operator to add multiple values to an array &lt;field\> if the values do not exist in the &lt;field\>.
`{ $addToSet: { <field>: { $each: [ <value1>, <value2> ... ] } } }`
Use with the `$push` operator to append multiple values to an array &lt;field>.
`{ $push: { <field>: { $each: [ <value1>, <value2> ... ] } } }`

> Operators 官方文档：[Operators](https://docs.mongodb.com/manual/reference/operator/)

从数组中删除元素有几种方法：
如果把数组看成队列或者栈，可以使用`$pop`，这个修改器可以从数组的任何一端删除元素。`{"$pop":{"key": 1}}`从数组末尾删除一个元素，`{"$pop":{"key": -1}}`则从头部删除;
有时需要基于特定条件来删除元素，而不是根据位置，这是可以使用`$pull`;
如果数组有多个值，而我们只想对其中一部分进行操作，可以通过位置或者定位操作符`$`.

#### upsert
upsert是一种特殊的更新，如果没有找到符合更新条件的文档，就会以这个条件和更新文档为基础创建一个新的文档。如果找到匹配的文档，则正常更新。
使用upsert，既可以避免竞态问题，又可以缩减代码量(update的第三个参数表示这个是upsert)：
```javascript
db.analytics.update({"url":"/blog"}, {"$inc": {"pageviews": 1}}, true)
```
```javascript
> use users
switched to db users
> db.users.find()
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
> db.users.update({"rep":25}, {"$inc":{"rep":3}}, true)
WriteResult({
        "nMatched" : 0,
        "nUpserted" : 1,
        "nModified" : 0,
        "_id" : ObjectId("580fee3acaf94008421f72a4")
})
> db.users.find().pretty()
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
{ "_id" : ObjectId("580fee3acaf94008421f72a4"), "rep" : 28 }
```
有时，需要在创建文档的同时创建字段并为它赋值，但是在之后的所有更新操作中，这个字段的值都不再改变，这就是`$setOnInsert`的作用。

#### 更新多个文档
默认情况下，更新只能对符合匹配条件的第一个文档执行操作。要是有多个文档符合条件，只有第一个文档会被更新，其它文档不会发生变化。要更新所有匹配的文档，可以将`update`的第4个参数设置为`true`.

要想知道多文档更新到底更新了多少文档，可以运行`getLastError`命令(可以理解为“返回最后一次操作的相关信息”)。键`n`的值就是被更新的文档的数量。

#### 返回被更新的文档
调用getLastError并不能返回被更新的文档。可以通过`findAndModify`命令得到被更新的文档。
findAndModify能够在一个操作中返回匹配结果并且进行更新。


### 写入安全机制
**写入安全**(Write Concern)是一种客户端设置，用于控制写入的安全级别。默认情况下，插入、删除和更新都会一直等待数据库响应(写入是否成功)，然后才继续执行。
两种最基本的写入安全机制是`应答式写入`(acknowledged write)和`非应答时写入`(unacknowledged write)。应答式写入是默认的方式：数据库会给出响应，告诉你写入操作是否成功执行。非应答式写入不返回任何响应，所以无法知道写入是否成功。

默认的写入机制变为安全写入之后，所有驱动程序都开始使用`MongoClient`这个类。如果程序使用的连接对象是`Mongo`或`Connection`，那么这段程序使用的就是旧的、默认不安全的API。
