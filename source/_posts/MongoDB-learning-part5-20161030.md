---
title: MongoDB 学习笔记(五)：特殊的索引与集合
date: 2016-10-30 11:53:20
tags: MongoDB
categories: [Web后端]
---

## 固定集合
MongoDb中的“普通”集合是动态创建的，而且可以自动增长以容纳更多的数据。MongoDB中还有另一种不同类型的结合叫做**固定集合**，固定集合需要事先创建，而且他的大小是固定的。
如果向固定集合插入文档的话，最老的文档会被删除以释放空间。
> **capped collection**
A fixed-sized collection that automatically overwrites its oldest entries when it reaches its maximum size. The MongoDB oplog that is used in replication is a capped collection.


### 固定集合的创建
不同于普通集合，固定集合的创建必须在使用之前显示创建。
`db.createCollection(name, options)`
Creates a new collection explicitly.
```javascript
db.createCollection(<name>, { capped: <boolean>,
                              autoIndexId: <boolean>,
                              size: <number>,
                              max: <number>,
                              storageEngine: <document>,
                              validator: <document>,
                              validationLevel: <string>,
                              validationAction: <string>,
                              indexOptionDefaults: <document> } )
```
参数
> **capped**
Optional. To create a capped collection, specify true. If you specify true, you must also set a maximum size in the size field.

> **size**   
Optional. Specify a maximum size in bytes for a capped collection. Once a capped collection reaches its maximum size, MongoDB removes the older documents to make space for the new documents. The size field is required for capped collections and ignored for other collections.

> **max**
Optional. The maximum number of documents allowed in the capped collection. The size limit takes precedence over this limit. If a capped collection reaches the size limit before it reaches the maximum number of documents, MongoDB removes old documents. If you prefer to use the max limit, ensure that the size limit, which is required for a capped collection, is sufficient to contain the maximum number of documents.

> **autoIndexId**
Optional. Specify false to disable the automatic creation of an index on the _id field.


例如:
```javascript
> db.createCollection("my_collection2",{"capped":true, "size":100000, "max":10})
{ "ok" : 1 }
```
固定集合创建后就不能改变了，如果需要修改固定集合的属性，只能将其删除之后再重建。因此，在创建固定集合前应该考虑好它的大小。

### 自然排序
对固定集合可以进行一种特殊的排序，称为**自然排序**(natural sort). 自然排序返回的就是集合中文档文档在磁盘中的顺序。
例如：
```javascript
> db.my_collection2.find()
> for (i=0;i<10;i++){
... db.my_collection2.insert({"news":"article"+i})
... }
WriteResult({ "nInserted" : 1 })
> db.my_collection2.find()
{ "_id" : ObjectId("5816476cd7f38c449494a65e"), "news" : "article0" }
{ "_id" : ObjectId("5816476cd7f38c449494a65f"), "news" : "article1" }
{ "_id" : ObjectId("5816476cd7f38c449494a660"), "news" : "article2" }
{ "_id" : ObjectId("5816476cd7f38c449494a661"), "news" : "article3" }
{ "_id" : ObjectId("5816476cd7f38c449494a662"), "news" : "article4" }
{ "_id" : ObjectId("5816476cd7f38c449494a663"), "news" : "article5" }
{ "_id" : ObjectId("5816476cd7f38c449494a664"), "news" : "article6" }
{ "_id" : ObjectId("5816476cd7f38c449494a665"), "news" : "article7" }
{ "_id" : ObjectId("5816476cd7f38c449494a666"), "news" : "article8" }
{ "_id" : ObjectId("5816476cd7f38c449494a667"), "news" : "article9" }
>
> db.my_collection2.find().sort({"$natural":-1})
{ "_id" : ObjectId("5816476cd7f38c449494a667"), "news" : "article9" }
{ "_id" : ObjectId("5816476cd7f38c449494a666"), "news" : "article8" }
{ "_id" : ObjectId("5816476cd7f38c449494a665"), "news" : "article7" }
{ "_id" : ObjectId("5816476cd7f38c449494a664"), "news" : "article6" }
{ "_id" : ObjectId("5816476cd7f38c449494a663"), "news" : "article5" }
{ "_id" : ObjectId("5816476cd7f38c449494a662"), "news" : "article4" }
{ "_id" : ObjectId("5816476cd7f38c449494a661"), "news" : "article3" }
{ "_id" : ObjectId("5816476cd7f38c449494a660"), "news" : "article2" }
{ "_id" : ObjectId("5816476cd7f38c449494a65f"), "news" : "article1" }
{ "_id" : ObjectId("5816476cd7f38c449494a65e"), "news" : "article0" }
>
> db.my_collection2.insert({"news":"article10"})
WriteResult({ "nInserted" : 1 })
> db.my_collection2.find().sort({"$natural":-1})
{ "_id" : ObjectId("5816486fd7f38c449494a668"), "news" : "article10"
{ "_id" : ObjectId("5816476cd7f38c449494a667"), "news" : "article9" }
{ "_id" : ObjectId("5816476cd7f38c449494a666"), "news" : "article8" }
{ "_id" : ObjectId("5816476cd7f38c449494a665"), "news" : "article7" }
{ "_id" : ObjectId("5816476cd7f38c449494a664"), "news" : "article6" }
{ "_id" : ObjectId("5816476cd7f38c449494a663"), "news" : "article5" }
{ "_id" : ObjectId("5816476cd7f38c449494a662"), "news" : "article4" }
{ "_id" : ObjectId("5816476cd7f38c449494a661"), "news" : "article3" }
{ "_id" : ObjectId("5816476cd7f38c449494a660"), "news" : "article2" }
{ "_id" : ObjectId("5816476cd7f38c449494a65f"), "news" : "article1" }
>
```

### 循环游标(Tailable Cursor)
> By default, MongoDB will automatically close a cursor when the client has exhausted all results in the cursor. However, for **capped collections** you may use a Tailable Cursor that remains open after the client exhausts the results in the initial cursor.
After clients insert new additional documents into a capped collection, the tailable cursor will continue to retrieve documents.

循环游标通常用于当文档插入到固定集合时，对新插入的文档进行处理。

## 没有_id索引的集合
默认情况下，每一个集合都有一个"_id"索引。但是，如果在调用createCollection创建集合时指定`autoIndexId`选项为false，创建的集合就不会自动创建_id。

## TTL(time to live)索引
> Expire Data from Collections by Setting TTL
 TTL collections make it possible to store data in MongoDB and have the mongod automatically remove data after a specified number of seconds or at a specific clock time.
 To create a TTL index, use the **db.collection.createIndex()** method with the **expireAfterSeconds** option on a field whose value is either a date or an array that contains date values.

例如：
For example, the following operation creates an index on the log_events collection’s createdAt field and specifies the expireAfterSeconds value of 3600 to set the expiration time to be one hour after the time specified by createdAt.
```javascript
db.log_events.createIndex( { "createdAt": 1 }, { expireAfterSeconds: 3600 } )
```
When adding documents to the log_events collection, set the createdAt field to the current time:
```javascript
db.log_events.insert( {
   "createdAt": new Date(),
   "logEvent": 2,
   "logMessage": "Success!"
} )
```
MongoDB will automatically delete documents from the log_events collection when the document’s createdAt value is older than the number of seconds specified in expireAfterSeconds.

MongoDB每分钟对TTL索引进行一次清理，所以不应该依赖以秒为单位的时间保证索引的存活状态。
