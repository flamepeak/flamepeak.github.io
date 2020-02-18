---
title: MongoDB 学习笔记(一)：基础
date: 2016-10-23 12:26:53
tags: MongoDB
categories: [Web后端]
---
## 介绍
MongoDB是一个基于分布式文件存储的数据库。由C++编写，旨在为Web应用提供可扩展的高性能存储方案。MongoDB将数据存储为一个文档，数据结构由键值(key:value)组成，文档类似JSON对象。

MongoDB 是一个**面向文档**(document-oriented)的数据库，而不是关系型数据库。与关系型数据库相比，面向文档的数据库不再有“行”(row)的概念，取而代之的是更为灵活的“文档”(document)模型。
> NoSQL,非关系型数据库。有时也称为Not only SQL，不同于传统的关系型数据库。
> NoSQL用于超大规模的数据存储，数据的存储不需要固定的模式，无需多余操作就可以横向扩展。

MongoDB的设计采用横向扩展(scale out).面向文档的数据模型使它能很容易地在多台服务器之间进行数据分割。MongoDB能自动处理跨集群的数据和负载，自动重新分配文档，以及将用户请求路由到正确的机器上。这样，开发者能够集中精力编写应用程序，而不需要考虑如何扩展的问题。如果一个集群需要更大的容量，只需要向集群添加新服务器，MongoDB就会自动将现有数据向新服务器传达。

MongoDB作为一款通用型数据库，除了能够创建、读取、更新和删除数据之外，还提供了一系列不断扩展的独特功能。例如：索引(indexing)、聚合管道(aggregation pipeline)、文件存储(file storage).

MongoDB在各方面的设计都旨在保持它的高性能。MongoDB能够对文档进行动态填充(dynamic padding)，也能预分配数据文件以利用额外的空间来换取稳定的性能。MongoDB把尽可能多的内存用作缓存(cache)，试图为每次查询自动选择正确的索引。

------

### 官方重要帮助文件链接地址：
[**Collection Methods**](https://docs.mongodb.com/manual/reference/method/js-collection/)



------

## 基础知识
+ **文档**是MongoDB中数据的基本单元。
+ **集合**(collection)可以看作是一个拥有动态模式(dynamic schema)的表。
+ MongoDB的一个实例可以拥有多个相互独立的数据库(database)，每一个数据库都拥有自己的集合。
+ 每一个文档都有一个特殊的键"_id",这个键在文档所属的集合中是唯一的。
+ MogonDB自带一个简单但是功能强大的JavaScript shell，可用于管理MongoDB的实例或数据操作。

### 文档
**文档**是MongoDB的核心概念。文档就是键值对的一个有序集。
文档的键是字符串。除少数例外情况，键可以使用任意UTF-8字符。
+ 键不能含有`\0` (空字符)。这个字符用于表示键的结尾。
+ `.`和`$`具有特殊意义，是被保留的字符，只能在特定环境下使用。

MongoDB不但**区分类型**，而且**区分大小写**。
MongoDB不能有重复的键；而且文档的键值对是**有序的**。
例如：`{"X":1, "Y":2}`与`{"Y":2,"X":1}`是不同的

### 集合
集合就是一组文档。
+ 集合名不能是空字符串
+ 集合名不能包含`\0`字符(空字符)，这表示集合名的结束。
+ 集合名不能以`system.`开头，这是系统集合保留的前缀。
+ 用户创建的集合不能在集合名中包含保留字符`$`。

组织集合的一种惯例是使用`.`分割不同命名空间的子集合。
在MongoDB中，使用子集合来组织数据非常高效，值得推荐。

### 数据库
在MongoDB中，多个文档组成集合，而多个集合可以组成**数据库**。一个MongoDB实例可以承载多个数据库，每个数据库拥有0个或多个集合。每个数据库都有独立的权限，即便是在磁盘上，不同的数据库也放置在不同的文件中。
按照经验，我们将有关一个应用程序的所有数据都存储在同一个数据库中。要想在同一个MongoDB服务器上存放所个应用程序或者用户的数据，就需要使用不同的数据库。

数据库是通过名称来标识，这点与集合类似。数据库名字可以是满足以下条件的任意UTF-8字符串。
+ 不能是空字符串。
+ 不得含有`/`、`\`、`.`、`"`、`*`、`<`、`>`、`:`、`|`、`?`、`$`、`\0`。基本上，只能使用ASCII中的字符和数字。
+ 数据库名字区分大小写。为简单起见，数据库名字应全部小写。
+ 数据库名字最多为64字节。

要记住一点：数据库最终会变成文件系统里的文件，而数据库名就是相应的文件名。
有些数据库名字是保留的，可以直接访问这些有特殊语义的数据库。如下：
+ admin
从身份验证角度来讲，这是“root”数据库。如果将用户添加到admin数据库，这个用户将自动获得所有数据的权限。再者，一些特定的服务器命令只能从admin数据库运行，例如列出所有数据库或关闭服务器。
+ local
这个数据库永远都不可以复制，且一台服务器上的所有本地集合都可以存储在这个数据库中。
+ config
MongoDB用于分片设置时，分片信息会存储在config数据库中。

将数据库名添加到集合名前，得到集合的完全限定名，即**命名空间**(namespace).命名空间的长度不得超过121字节，实际使用中应小于100字节。

### 启动MongoDB
通常，MongoDB作为网络服务器来运行，客户端可连接到该服务器并执行操作。
Linux：运行mongod 命令，启动数据库服务器
Windows：执行mongod.exe.(win7:默认安装位置在`C:\Program Files\MongoDB\Server\3.2\bin`)

注意：MongoDB 2.2以后的版本已经不支持XP系统了。

mongod在没有参数情况下会使用默认数据库目录`/data/db`(Windows系统中为`C:\data\db`).如果数据目录不存在或者不可写，服务器启动会失败。因此，启动MongoDB前，先创建数据目录，这点非常重要。

启动时，服务器会打印版本和系统信息，然后等待连接。默认情况下，MongoDB监听`27017`端口.如果端口没占用，启动会失败。通常是因为，已经有一个MongoDB实例在运行了。

mongod还会启动一个非常基本的HTTP服务器，监听比主端口号高1000的端口，也就是28017端口。这意味着，通过浏览器访问`http://localhost:28017`，能获取数据库的管理信息。
> 在3.2版本中，直接运行mongod.exe并不启动http服务器，需要加上参数`--httpinterface`

中止mongod的运行，只须`Ctrl+C`

### MongoDB shell 介绍
MongoDB自带JavaScript shell，可在shell中使用命令行与MongoDB实例交互。
对于MongoDB来说，`mongo shell`是至关重要的工具。

#### 运行shell
运行`mongo`(Windows中:mongo.exe)启动shell。
启动时，shell将自动连接MongoDB服务器，须确保mongod已启动。

    C:\Program Files\MongoDB\Server\3.2\bin>mongo.exe
    MongoDB shell version: 3.2.10
    connecting to: test
    >


mongo shell是一个功能完备的JavaScript解释器，可运行**任意**JavaScript程序。

    > x=200
    200
    > x/4
    50
    > x
    200
    > "Hello, World".replace("World","MongoDB")
    Hello, MongoDB

需要注意的是，可使用多行命令。shell会检测输入的JavaScript语句是否完整，如没写完可在下一行接着写。在某行连续三次按下回车键可取消未输入完成的命令，并退回到`>`提示符。

    > x=
    ...
    ...
    >

#### MongoDB 客户端
能运行任意JavaScript程序听上去很酷，不过shell的真正强大之处在于，它是一个独立的MongoDB客户端。启动时，shell会连接到MongoDB服务器的`test`数据库，并将数据库连接赋值给全局变量`db`。这个变量是通过shell访问MongoDB的主要入口点。

如果想查看db当前指向哪个数据库，可以使用`db`命令：

    > db
    test
    >

为了方便习惯使用SQL shell的用户，shell还包含一些非JavaScript语法的扩展。这些扩展并不提供额外的功能，而是一些非常棒的语法糖。例如，最重要的操作之一是选择数据库：

    > use foobar
    switched to db foobar

#### shell 中的基本操作
即：创建、读取、更新、删除
**创建**
`insert`函数可以将一个文档添加到集合中。
首先创建一个名为post的局部变量，这是一个JavaScript对象，用于表示我们的文档。
```JavaScript
    > post = {"title":"My Blog Post",
    ... "content":"Here is my blog post.",
    ... "date":new Date()}
    {
            "title" : "My Blog Post",
            "content" : "Here is my blog post.",
            "date" : ISODate("2016-10-24T00:59:47.760Z")
    }

```
这个对象是有效的MongoDB文档，所以可用`insert`方法将其保存到集合中：
```JavaScript
> db.blog.insert(post)
WriteResult({ "nInserted" : 1 })
```
这样，文档就被存到数据库中。要查看它可以使用`find`方法。
```JavaScript
> db.blog.find()
{ "_id" : ObjectId("580d5dc08ecbdd01825abb90"), "title" : "My Blog Post", "conte
nt" : "Here is my blog post.", "date" : ISODate("2016-10-24T00:59:47.760Z") }
```
或使用`pretty()`格式化显示
```
> db.blog.find().pretty()
{
        "_id" : ObjectId("580d5dc08ecbdd01825abb90"),
        "title" : "My Blog Post",
        "content" : "Here is my blog post.",
        "date" : ISODate("2016-10-24T00:59:47.760Z")
}
```

**读取**
`find`和`findOne`方法可以用于查询集合里的文档。
使用find时，shell会自动显示最多20个匹配的文档，也可以获取更多的文档。

**更新**
使用`update`更新文档。`update`接受(至少)两个参数：第一个是限定条件（用于匹配待更新的文档），第二个是新的文档。

首先，修改变量post，增加“comments”键：
```
> post.comments = []
[ ]
```
然后执行update操作，用新版本的文档替换标题为"My Blog Post"的文档：
```
> db.blog.update({"title":"My Blog Post"}, post)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```
再用find查看一下，可以看到新的键：
```
> db.blog.find().pretty()
{
        "_id" : ObjectId("580d5dc08ecbdd01825abb90"),
        "title" : "My Blog Post",
        "content" : "Here is my blog post.",
        "date" : ISODate("2016-10-24T00:59:47.760Z"),
        "comments" : [ ]
}
```

**删除**
使用`remove`方法可以将文档从数据库中**永久删除**。如果没有使用任何参数，它会将集合内的所有文档全部删除。但是**不会删除集合本身，也不会删除集合的元信息**。
它可以接受一个作为限定条件的文档作为参数。例如：
```
> db.blog.remove({"title":"My Blog Post"})
WriteResult({ "nRemoved" : 1 })
> db.blog.find().pretty()
>
```

### 数据类型
#### 基本数据类型
MongoDB的文档与JavaScript中的对象相近，因而可以认为它类似于JSON。
JSON中只包含了6中数据类型：null、布尔、数字、字符串、数组和对象，表达能力有一定的局限。
MongDB在保留JSON基本键值对的基础上，添加了其他数据类型。
+ null
`null`表示空值或者不存在的字段
+ 布尔型
有两个值`true`和`false`
+ 数值
shell默认使用`64位浮点值`。
对于整型值，可以使用`NumberInt`类(表示4字节带符号整数)或`NumberLong`类(表示8字节带符号整数)，例如：
{"x":NumberInt("3")}
{"x":NumberLong("3")}
+ 字符串
UTF-8字符串都可以表示为字符串类型的数据。
+ 日期
日期被存储为自新纪元以来经过的毫秒数，不存储时区。
创建日期对象时，应使用`new Date(···)`,而非`Date(···)`。如将构造函数(constructor)作为函数进行调用(即不包括new的方式)，返回的是日期的字符串表示，而非日期(Date)对象。如果不注意这一点，没有始终使用日期构造函数，将得到一对婚礼的日期对象和日期的字符串。由于日期和字符串之间无法匹配，在执行删除、更新、查询操作时会导致很多问题。：
{"x":`new Date()`}
+ 正则表达式
查询时，使用正则表达式作为限定条件，语法也与JavaScript的正则表达式语法相同：
{"x": /foobar/i}
+ 数组
数据列表或数据集可以表示为数组。
{"x":["a","b","c"]}
+ 内嵌文档
文档也可以嵌套其他文档，被嵌套的文档作为父文档的值。
{"x": {"foo":"bar"}}
+ 对象id
对象id是一个12字节的ID，是文档的唯一标识。
MongoDB中存储的文档**必须**有一个"_id"键。这个键的值可以是任何类型的，默认是个ObjectId对象。
{"x" : `ObjectId()`}

**关于ObjectId**
ObjectId使用12字节的存储空间，是一个由24个十六进制数字组成的字符串。

    0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11
         时间戳    |    机器   |  PID  |   计数器

+ 4字节时间戳，与后面的5字节组合起来，提供秒级别的唯一性。
+ 3字节机器唯一标识符，通常是主机名的hash。
+ 2字节进程PID，确保一台主机并发进程产生的ObjectId唯一。
+ 3字节计数器，自动增加，确保相同进程同一秒产生的ObjectId是不一样的。


### 使用MongoDB shell
可以将shell连接到任何MongoDB实例。
`mongo somehost:30000/myDB`
现在db就指向了somehost:30000上的myDB数据库。

启动时也可以不连接到任何mongod，可以使用`--nodb`参数启动shell；启动后，在需要时可以运行`new Mongo("somehost:30000")`连接到想要的mongod。

#### shell 技巧
`help` 查看帮助
`db.help()` 查看数据库级别的帮助
`db.foo.help()` 查看集合级别的帮助
如果想知道一个函数是做什么用的，可以直接在shell中输入函数名(函数名后不要加括号)，就可以看到相应的JavaScript实现代码。

#### 使用shell执行脚本
`mongo script1.js script2.js script3.js`
mongo shell依次执行传入脚本，然后退出。如果希望使用指定主机和端口上的mongod运行脚本，需要先指定地址，然后再跟上脚本文件的名称：
`mongo --quiet server1:30000/foo script1.js script2.js script3.js`
这样可以将db指向server1：30000上的foo数据库，然后执行这三个脚本。注意运行shell时命令行参数要在地址之前。
也可以使用load()函数，从交互式shell中运行脚本。
`load("script1.js")`

#### 创建`.mongorc.js`文件
如果某些脚本会被频繁加载，可以将它们添加到`mongorc.js`文件中。这个文件会在启动shell时自动运行。`mongorc.js`位于用户主目录下。
`.mongorc.js`最常见的用途之一是移除那些比较危险的shell辅助函数。
```JavaScript
var no = function(){
    print("Not on my watch.")
};

//禁止删除数据库
db.dropDatabase = DB.prototype.dropDatabase = no;
//禁止删除集合
DBCollection.prototype.drop = no;
//禁止删除索引
DBCollection.prototype.dropIndex = no;
```
改变数据库函数时，要确保同时对db变量和DB原型进行改变(如上)。
如果在启动shell时指定`--norc`参数，就可以禁止加载`.mongorc.js`.


------
