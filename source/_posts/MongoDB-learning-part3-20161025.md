---
title: MongoDB 学习笔记(三)：查询
date: 2016-10-25 17:55:49
tags: MongoDB
categories: [Web后端]
---
官方的一些关于查询的帮助页面：
[Query Modifiers](https://docs.mongodb.com/manual/reference/operator/query-modifier/)
[Query and Projection Operators](https://docs.mongodb.com/manual/reference/operator/query/)

## find 简介
官方定义：
> [**db.collection.find()**](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)：Performs a query on a collection and returns a cursor object.

`
db.collection.find( { <query> } )._addSpecial( <option> )
db.collection.find( { $query: { <query> }, <option> } )
`

find的第一个参数决定了要返回哪些文档，这个参数是一个文档，用于指定查询条件。
空的查询文档(`{}`)会匹配集合的全部内容。如果不指定查询文档，默认就是`{}`

多个查询条件：
```javascript
db.users.find({"username":"joe", "age":27})
```

### 指定需要返回的键
可以使用find的第二个参数来指定想要的键。
例如：如果只对用户集合的"name","age"感兴趣，可以使用一下查询：
```javascript
> db.users.find()
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
{ "_id" : ObjectId("580fee3acaf94008421f72a4"), "rep" : 28 }
{ "_id" : ObjectId("580fef20caf94008421f72a5"), "rep" : 28 }
> db.users.find({},{"name":1, "age":1}).pretty()
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
{ "_id" : ObjectId("580fee3acaf94008421f72a4") }
{ "_id" : ObjectId("580fef20caf94008421f72a5") }
```
当然也可以把`_id`键去除：
```javascript
> db.users.find({},{"name":1,"age":1, "_id":0})
{ "name" : "joe", "age" : 30 }
{  }
{  }
```

## 查询条件
### Comparison
比较操作符有以下几种：

|Name|Description|
|--|--|
|`$eq` | Matches values that are **equal to** a specified value.|
|`$gt` | Matches values that are **greater than** a specified value.|
|`$gte`| Matches values that are **greater than or equal to** a specified value.|
|`$lt` | Matches values that are **less than** a specified value.|
|`$lte`| Matches values that are **less than or equal to** a specified value.|
|`$ne` | Matches all values that are **not equal to** a specified value.|
|`$in` | Matches **any** of the values specified **in an array**.|
|`$nin`| Matches **none** of the values specified **in an array**.|

例如：
```javascript
db.users.find({"age": {"$gte":18, "$lte":30}})
```
以及时间查询：
```javascript
> start = new Date("01/01/2007")
ISODate("2007-01-01T08:00:00Z")
> db.temp.find({"createAt":{"$gt":start}}).pretty()
{
        "_id" : ObjectId("580ff0d1caf94008421f72a6"),
        "createAt" : ISODate("2016-10-25T23:54:57.159Z")
}
> db.temp.find({"createAt":{"$lt":start}}).pretty()
>
```
由于文档中的日期是精确到毫秒的，精确匹配用处不大，范围查询就很有必要。

### Logical

|Name|Description|
|---|---|
|`$or`  | Joins query clauses with a logical OR returns all documents that **match** the conditions of **either clause**.
|`$and` | Joins query clauses with a logical AND returns all documents that **match** the conditions of **both clauses**.
|`$not` | Inverts the effect of a query expression and returns documents that **do not match** the query expression.
|`$nor` | Joins query clauses with a logical NOR returns all documents that **fail to match both** clauses.

比较：
`$in`可以用来查询一个键的多个值；
`$or`可以在多个键中查询给定的任意值。
例如：
```javascript
> db.users.find()
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
{ "_id" : ObjectId("580fee3acaf94008421f72a4"), "rep" : 28 }
{ "_id" : ObjectId("580fef20caf94008421f72a5"), "name" : "tink", "rep" : 12 }
> db.users.find({"rep":{"$in":[28, 10]}}).pretty()
{ "_id" : ObjectId("580fee3acaf94008421f72a4"), "rep" : 28 }

> db.users.find({"$or":[{"name":"joe"}, {"rep":12}]}).pretty()
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
{
        "_id" : ObjectId("580fef20caf94008421f72a5"),
        "name" : "tink",
        "rep" : 12
}
>
```
**$not**用法
```javascript
> db.users.find({"rep":{"$not":{"$mod":[3,1]}}}).pretty()
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
{
        "_id" : ObjectId("580fef20caf94008421f72a5"),
        "name" : "tink",
        "rep" : 12
}
>
```
`$mod`将查询的值除以第一个给定值，如果余数与第二个值相等则匹配成功。


### Element

|Name|Description|
|---|---|
|`$exists` | Matches documents that have the specified field.|
|`$type`   | Selects documents if a field is of the specified type.|

例如一下情形：
```javascript
> db.users.find()
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
{ "_id" : ObjectId("580fee3acaf94008421f72a4"), "rep" : 28 }
{ "_id" : ObjectId("580fef20caf94008421f72a5"), "name" : "tink", "rep" : 12 }
{ "_id" : ObjectId("5810325c32248ee52c958192"), "y" : null }
```
从中提取null的文档：
```javascript
> db.users.find({"y":null})
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
{ "_id" : ObjectId("580fee3acaf94008421f72a4"), "rep" : 28 }
{ "_id" : ObjectId("580fef20caf94008421f72a5"), "name" : "tink", "rep" : 12 }
{ "_id" : ObjectId("5810325c32248ee52c958192"), "y" : null }
```
结果不正确，null不仅会匹配某个键的值为null的文档，而且还会匹配不包含这个键的文档。
所以，我们不仅要检查键值是否为null，还要通过`$exists`检查键存在。
```javascript
> db.users.find({"y":{"$eq":null, "$exists":true}})
{ "_id" : ObjectId("5810325c32248ee52c958192"), "y" : null }
>
```


### Evaluation

|Name|Description|
|---|---|
|`$mod`   |  Performs a **modulo operation**(模操作) on the value of a field and selects documents with a specified result.|
|`$regex` |  Selects documents where values match a specified **regular expression**.|
|`$text`  |  Performs **text search**.|
|`$where` |  Matches documents that **satisfy a JavaScript expression**.|

说说正则表达式，MongoDB使用Perl兼容的正则表达式(PCRE)库来匹配。
`$regex`: Provides regular expression capabilities for pattern matching **strings** in queries
有三种语法：
```perl
{ <field>: { $regex: /pattern/, $options: '<options>' } }
{ <field>: { $regex: 'pattern', $options: '<options>' } }
{ <field>: { $regex: /pattern/<options> } }
```
在MongoDB中，你同样可以使用正则表达式对象(regular expression objects)（例如：`/pattern/`）来指定正则表达式。
```perl
{ <field>: /pattern/<options> }
```
可用的`options`有以下几种：
`i`
`m`
`x`
`s`

例如：
```javascript
> db.users.find({"name":/Joe/i})
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
>
> db.users.find({"name":{"$regex":/Joe/, "$options":"i"}})
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
>
> db.users.find({"name":{"$regex":/Joe/i}})
{ "_id" : ObjectId("580f860132248ee52c95818d"), "name" : "joe", "age" : 30 }
>
```

### Array

|Name|Description|
|---|---|
|`$all`       | Matches arrays that contain all elements specified in the query.|
|`$elemMatch` |  Selects documents if element in the array field matches all the specified $elemMatch conditions.|
|`$size`      |   Selects documents if the array field is a specified size.|

**$all**
例如：
```javascript
> db.food.find()
{ "_id" : ObjectId("58103fb432248ee52c958194"), "fruit" : [ "apple", "banana", "peach" ] }
{ "_id" : ObjectId("58103fd532248ee52c958195"), "fruit" : [ "apple", "kumquat","orange" ] }
{ "_id" : ObjectId("58103ff032248ee52c958196"), "fruit" : [ "cherry", "banana","apple" ] }
>
> db.food.find({"fruit":{"$all":["apple", "banana"]}})
{ "_id" : ObjectId("58103fb432248ee52c958194"), "fruit" : [ "apple", "banana", "peach" ] }
{ "_id" : ObjectId("58103ff032248ee52c958196"), "fruit" : [ "cherry", "banana","apple" ] }
>
```
注意观察上面结果，`$all`与顺序无关。
如果想查询数组特定位置上的元素，需要使用`key.index`语法指定下标，下标从0开始，如下：
```javascript
> db.food.find({"fruit.2":"peach"})
{ "_id" : ObjectId("58103fb432248ee52c958194"), "fruit" : [ "apple", "banana", "peach" ] }
>
```
**$size**
查询特定长度的数组
```javascript
> db.food.find({"fruit":{"$size":3}})
{ "_id" : ObjectId("58103fb432248ee52c958194"), "fruit" : [ "apple", "banana", "peach" ] }
{ "_id" : ObjectId("58103fd532248ee52c958195"), "fruit" : [ "apple", "kumquat","orange" ] }
{ "_id" : ObjectId("58103ff032248ee52c958196"), "fruit" : [ "cherry", "banana","apple" ] }
>
```
但是，`$size`并不能与其它查询条件（比如`$gt`）组合使用。错误如下：
```javascript
> db.food.find({"fruit":{"$size":{"$gt":2}}})
Error: error: {
        "waitedMS" : NumberLong(0),
        "ok" : 0,
        "errmsg" : "$size needs a number",
        "code" : 2
}
```


### Projection Operators
|Name|Description|
|---|---|
|`$`   | Projects the **first element** in an array that matches the query condition.|
|`$elemMatch` | Projects the first element in an array that matches the specified $elemMatch condition.|
|`$meta` | Projects the document’s **score** assigned during $text operation.|
|`$slice` | **Limits** the number of elements projected from an array. Supports skip and limit slices.|

**$silice**
`db.collection.find( { field: value }, { array: {$slice: count } } )`

`$slice`操作符可以返回某个键匹配的元素的一个子集。
例如：
`{"$slice":10}`：返回前10个
`{"$slice":-10}`：返回后10个
`{"$slice": [23,10]}`：跳过前23个，返回24—33个元素。

例如：
```javascript
> db.food.find()
{ "_id" : ObjectId("58103fb432248ee52c958194"), "fruit" : [ "apple", "banana", "peach" ] }
{ "_id" : ObjectId("58103fd532248ee52c958195"), "fruit" : [ "apple", "kumquat","orange" ] }
{ "_id" : ObjectId("58103ff032248ee52c958196"), "fruit" : [ "cherry", "banana","apple" ] }
>
> db.food.findOne({},{"fruit":{$slice:1}})
{ "_id" : ObjectId("58103fb432248ee52c958194"), "fruit" : [ "apple" ] }
>
> db.food.find({},{"fruit":{$slice:1}})
{ "_id" : ObjectId("58103fb432248ee52c958194"), "fruit" : [ "apple" ] }
{ "_id" : ObjectId("58103fd532248ee52c958195"), "fruit" : [ "apple" ] }
{ "_id" : ObjectId("58103ff032248ee52c958196"), "fruit" : [ "cherry" ] }
>
> db.food.find({},{"fruit":{$slice:[1,1]}})
{ "_id" : ObjectId("58103fb432248ee52c958194"), "fruit" : [ "banana" ] }
{ "_id" : ObjectId("58103fd532248ee52c958195"), "fruit" : [ "kumquat" ] }
{ "_id" : ObjectId("58103ff032248ee52c958196"), "fruit" : [ "banana" ] }
>
```
注意范围查询，面对数组是没有用的，范围会匹配任意多元素数组：
```javascript
{ "_id" : ObjectId("5810dea832248ee52c958197"), "x" : 5 }
{ "_id" : ObjectId("5810deaf32248ee52c958198"), "x" : 15 }
{ "_id" : ObjectId("5810deb332248ee52c958199"), "x" : 25 }
{ "_id" : ObjectId("5810deba32248ee52c95819a"), "x" : [ 5, 25 ] }
>
> db.test.find({"x":{"$gt":10,"$lt":20}})
{ "_id" : ObjectId("5810deaf32248ee52c958198"), "x" : 15 }
{ "_id" : ObjectId("5810deba32248ee52c95819a"), "x" : [ 5, 25 ] }
>
```
返回结果明显不是我们想要的。
这时可以使用`$elemMatch`要求MongoDB同时使用查询条件中的两个语句与一个数组元素进行比较。但是，还有一个问题，`$elemMatch`不会匹配非数组元素。如下：
```javascript
> db.test.find({"x":{$elemMatch:{"$gt":10,"$lt":20}}})
>//查不到任何结果
```
如果查询字段上创建过索引，可以使用`min()`和`max()`将查询条件遍历的索引范围限制为`$gt`和`$lt`的值：
```javascript
> db.test.find({"x":{"$gt":10,"$lt":20}}).min({"x":10}).max({"x":20})
>{"x":15}
```
> 在可能包含数组的文档上应用范围查询时，使用min()和max()是非常好的：如果在整个索引范围内对数组使用`$gt/$lt`查询，效率是非常低的。查询条件会与所有值进行比较，会查询每一个索引，而不仅仅是指定索引范围内的值。


### Geospatial

|Name|Description|
|---|---|
|`$geoWithin`    | Selects geometries within a bounding GeoJSON geometry. The 2dsphere and 2d indexes support $geoWithin.|
|`$geoIntersects`| Selects geometries that intersect with a GeoJSON geometry. The 2dsphere index supports $geoIntersects.|
|`$near`         | Returns geospatial objects in proximity to a point. Requires a geospatial index. The 2dsphere and 2d indexes support $near.|
|`$nearSphere`   | Returns geospatial objects in proximity to a point on a sphere. Requires a geospatial index. The 2dsphere and 2d indexes support $nearSphere.|





### Bitwise

|Name|Description|
|---|---|
|`$bitsAllSet`  | Matches numeric or binary values in which a set of bit positions all have a value of 1.|
|`$bitsAnySet`  | Matches numeric or binary values in which any bit from a set of bit positions has a value of 1.|
|`$bitsAllClear`| Matches numeric or binary values in which a set of bit positions all have a value of 0.|
|`$bitsAnyClear`| Matches numeric or binary values in which any bit from a set of bit positions has a value of 0.|


### 内嵌文档的查询
内嵌文档查询可以用点表示法：
例如：
```javascript
> db.people.find().pretty()
{ "_id" : ObjectId("5810e52632248ee52c95819b"), "age" : 45 }
{
        "_id" : ObjectId("5810e63732248ee52c95819c"),
        "name" : {
                "first" : "Joe",
                "last" : "Schmoe"
        },
        "age" : 45
}
>
> db.people.find({"name.first":"Joe"})
{ "_id" : ObjectId("5810e63732248ee52c95819c"), "name" : { "first" : "Joe", "last" : "Schmoe" }, "age" : 45 }
>
> db.people.find({"name.first":"Joe", "age":45})
{ "_id" : ObjectId("5810e63732248ee52c95819c"), "name" : { "first" : "Joe", "last" : "Schmoe" }, "age" : 45 }
```


### 游标
数据库使用游标返回find的执行结果。
要想从shell中创建一个游标，首先要对集合填充一些文档，然后对其执行查询，并将结果分配给一个局部变量（用var声明的变量就是局部变量）。
例如：
```javascript
> for (i=0; i<100;i++){
... db.collection.insert({x:i});
... }
WriteResult({ "nInserted" : 1 })
> db.collection.find()
{ "_id" : ObjectId("5811002a32248ee52c95819d"), "x" : 0 }
{ "_id" : ObjectId("5811002a32248ee52c95819e"), "x" : 1 }
{ "_id" : ObjectId("5811002a32248ee52c95819f"), "x" : 2 }
{ "_id" : ObjectId("5811002a32248ee52c9581a0"), "x" : 3 }
{ "_id" : ObjectId("5811002a32248ee52c9581a1"), "x" : 4 }
{ "_id" : ObjectId("5811002a32248ee52c9581a2"), "x" : 5 }
{ "_id" : ObjectId("5811002a32248ee52c9581a3"), "x" : 6 }
{ "_id" : ObjectId("5811002a32248ee52c9581a4"), "x" : 7 }
{ "_id" : ObjectId("5811002a32248ee52c9581a5"), "x" : 8 }
{ "_id" : ObjectId("5811002a32248ee52c9581a6"), "x" : 9 }
{ "_id" : ObjectId("5811002a32248ee52c9581a7"), "x" : 10 }
{ "_id" : ObjectId("5811002a32248ee52c9581a8"), "x" : 11 }
{ "_id" : ObjectId("5811002a32248ee52c9581a9"), "x" : 12 }
{ "_id" : ObjectId("5811002a32248ee52c9581aa"), "x" : 13 }
{ "_id" : ObjectId("5811002a32248ee52c9581ab"), "x" : 14 }
{ "_id" : ObjectId("5811002a32248ee52c9581ac"), "x" : 15 }
{ "_id" : ObjectId("5811002a32248ee52c9581ad"), "x" : 16 }
{ "_id" : ObjectId("5811002a32248ee52c9581ae"), "x" : 17 }
{ "_id" : ObjectId("5811002a32248ee52c9581af"), "x" : 18 }
{ "_id" : ObjectId("5811002a32248ee52c9581b0"), "x" : 19 }
Type "it" for more
> var cursor = db.collection.find();
> cursor.hasNext()
true
>
> cursor.next()
{ "_id" : ObjectId("5811002a32248ee52c95819d"), "x" : 0 }
>
> cursor.next()
{ "_id" : ObjectId("5811002a32248ee52c95819e"), "x" : 1 }
>
```
如果要迭代结果，可以使用游标的`next`方法；也可以使用`hasNext`来查看游标中是否还有其它结果。典型的结果遍历如下：
```javascript
> while (cursor.hasNext()){
... obj = cursor.next();
... //do stuff
···}
>
```

### limit、skip和sort
最常用的查询选项就是限制返回结果的数量、忽略一定数量的结果以及排序。
```javascript
> db.collection.find().limit(4)
{ "_id" : ObjectId("5811002a32248ee52c95819d"), "x" : 0 }
{ "_id" : ObjectId("5811002a32248ee52c95819e"), "x" : 1 }
{ "_id" : ObjectId("5811002a32248ee52c95819f"), "x" : 2 }
{ "_id" : ObjectId("5811002a32248ee52c9581a0"), "x" : 3 }
>
> db.collection.find().skip(97)
{ "_id" : ObjectId("5811002a32248ee52c9581fe"), "x" : 97 }
{ "_id" : ObjectId("5811002a32248ee52c9581ff"), "x" : 98 }
{ "_id" : ObjectId("5811002a32248ee52c958200"), "x" : 99 }
>
```
sort接受一个对象作为参数，这个对象时一组键值对，键对应文档的键名，值代表排序的方向。排序的方向可以是1（升序）或者-1（降序）。如果指定了多个键，则按照这些键被指定的顺序逐个排序。
```javascript
> db.collection.find().limit(15).sort({"x":-1})
{ "_id" : ObjectId("5811002a32248ee52c958200"), "x" : 99 }
{ "_id" : ObjectId("5811002a32248ee52c9581ff"), "x" : 98 }
{ "_id" : ObjectId("5811002a32248ee52c9581fe"), "x" : 97 }
{ "_id" : ObjectId("5811002a32248ee52c9581fd"), "x" : 96 }
{ "_id" : ObjectId("5811002a32248ee52c9581fc"), "x" : 95 }
{ "_id" : ObjectId("5811002a32248ee52c9581fb"), "x" : 94 }
{ "_id" : ObjectId("5811002a32248ee52c9581fa"), "x" : 93 }
{ "_id" : ObjectId("5811002a32248ee52c9581f9"), "x" : 92 }
{ "_id" : ObjectId("5811002a32248ee52c9581f8"), "x" : 91 }
{ "_id" : ObjectId("5811002a32248ee52c9581f7"), "x" : 90 }
{ "_id" : ObjectId("5811002a32248ee52c9581f6"), "x" : 89 }
{ "_id" : ObjectId("5811002a32248ee52c9581f5"), "x" : 88 }
{ "_id" : ObjectId("5811002a32248ee52c9581f4"), "x" : 87 }
{ "_id" : ObjectId("5811002a32248ee52c9581f3"), "x" : 86 }
{ "_id" : ObjectId("5811002a32248ee52c9581f2"), "x" : 85 }
```
注意观察以上返回结果，与预期不同。事实上，是先排序，然后在使用limit或者skip。


### 高级查询选项
有两种类型的查询：**简单查询**(plain query) 和 **封装查询**(wrapped query)。
简单查询就是我们上面进行的查询。
高级查询的长用选项：
+ `$maxscan`：*interger*
指定本次查询中扫描文档数量的上限。
`db.foo.find(criteria)._addSpecial("$maxscan", 20)`
+ `$min`：*document*
查询的开始条件。
+ `$max`：*document*
查询的结束条件。
