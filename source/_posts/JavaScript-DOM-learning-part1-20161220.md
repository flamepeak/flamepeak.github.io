---
title: JavaScript DOM 编程艺术：JavaScript语法
date: 2016-12-20 15:07:52
tags: [笔记, JavaScript]
categories: [Web前端]
---

JavaScript DOM编程艺术
DOM Scripting
——Web Design with JavaScript and the Document Object Model

 DOM（文档对象模型）
DOM是一套对文档的内容进行抽象和概念化的方法。
DOM是一种API。W3C对DOM的定义是：“一个与系统平台和编程语言无关的接口，程序和脚本可以通过这个接口动态的访问和修改文档的内容、结构和样式。”
今天，几乎所有的浏览器都内置了对DOM的支持。

---

## JavaScript 语法
JavaScript编写的代码必须通过HTML/XHTML文档才能执行。有两种方式可以做到这点：
第一种方式是将JavaScript代码放到文档`<head>`标签中的`<script>`标签之间；
```HTML
<head>
  <meta charset = "utf-8"/>
  <title>Example</title>
  <script>
    *JavaScript goes here...*
  </script>
</head>
```
一种更好的方式是把JavaScript代码存为一个扩展名为`.js`的文件。典型的做法是在文档的`<head>`部分放一个`<script>`标签，并把它的`src`属性指向该文件;
```HTML
<head>
  <meta charset = "utf-8"/>
  <title>Example</title>
  <script src = "file.js"></script>
</head>
```
但最好的做法是把`<script>`标签放到HTML文档的最后，`</body>`标签之前，这样才能使浏览器更快的加载页面：
```HTML
<head>
  <meta charset = "utf-8"/>
  <title>Example</title>
</head>
<body>
  *Mark-up goes here...*
  <script src = "file.js"></script>
</body>

```

### 语法
JavaScript的语法与Java和C++语言的语法非常类似。
#### 语句statement
多条语句放在同一行，就必须用分号分割它们。
建议在每条语句的末尾都加上一个分号，这个一个良好的编程习惯。

#### 注释comment
使用两个斜线`//`作为一行的开始，会被当作注释。
注释多行可以使用`/*`和`*/`.

#### 变量variable
赋值 assignment
在JavaScript脚本中，如果程序员在对某个变量赋值之前未声明，赋值操作将会自动声明该变量。虽然JavaScript没有强制要求程序员必须提前声明变量，但是提前声明变量是一种良好的编程习惯。
```JavaScript
var mood, age;
mood = "mad";

var age = 33;
var mood="mad", age=34;
```
JavaScript变量名允许包含字母、数字、美元符号和下划线（但第一个字符不允许是数字）。为了使较长的变量名更可读，可以在变量名中的适当位置加入下划线，或者使用驼峰格式（camel case），单词后面的每个新单词改用大写字母。
`var myMood = "Happy";`
通常驼峰格式是函数名、方法名和对象属性命名的首选格式。

#### 数据类型
必须明确类型声明（typing）的语言称为**强类型**（strongly typed）语言。JavaScript不需要进行类型声明，因此它是一种**弱类型**（weakly typed）语言。这意味着程序员可以在任何阶段改变变量的数据类型。

JavaScript中最重要的几种数据类型：
**字符串**
字符串必须包含在引号里，单引号或者双引号都可以。
```JavaScript
var mood = "happy";
var mood = 'happy';
```
JavaScript使用反斜线`\`进行转义：
```JavaScript
var mood = 'don\'t ask';
var height = "about 5'10\" tall";
```

**数值**
JavaScript允许使用带小数点的数值，并允许任意位小数，即浮点数（float-point number）.
```JavaScript
var age = 33.25;
var temperature = -20;
```

**布尔型** boolean
true  false

**数组**
在JavaScript中，数组可以用关键字Array声明。声明数组的同时还可以指定数组初始元素的个数，也就是这个数组的长度（length）。
`var beatles = Array(4);`
在声明时也可以不给出元素个数。
`var beatles = Array();`
向数组中添加元素的操作称为填充(populating)。在填充数组时，不仅需要给出新元素的值，还需要给出新元素在数组中的存放位置，这个位置就是元素的下标（index）。数组里一个元素一个下标。下标必须用方括号括起来：
`array[index] = element;`
也可以在声明数组的同时进行填充。
```JavaScript
var beatles = Array("John", "Paul", "George", "Ringo");
var years = [1940, 1941, 1942, 1943];
```
**关联数组**
可以通过在填充数组时为每个新元素明确的给出下标来改变这种默认的行为。在为新元素给出下标时，不必局限于使用整个数字。可以使用字符串。
```javascript
var lennon = Array();
lennon["name"] = "John";
lennon["year"] = 1940;
lennon["living"] = false;
```
这种数字叫做关联数组。这种用法不是一个好习惯，不推荐使用。本质上，在创建关联数组时，你创建的是Array对象的属性。在JavaScript中，所有变量实际上都是某种类型的对象。比如，一个布尔型就是一个Boolean类型的对象，一个数组就是一个Array类型的对象。在上面的例子中，你实际上是给lennon数组添加了name、year和living三个属性。理想情况下，你不应该修改Array对象的属性，而是应该使用通用的对象（Object）。

**对象**
与数组类似，对象也是使用一个名字表示一组值。对象的每个值都是对象的一个属性。例如，上面的lennon数组也可以创建成下面这个对象：
```javascript
var lennon = Object();
lennon.name = "John";
lennon.year = 1940;
lennon.living = false;
```
创建对象还有一种更简洁的语法，即花括号语法：
`{propertyName:value, propertyName:value}`
比如：
```javascript
var lennon = {name:"John", year:1940, living:false};
```
属性名与JavaScript变量的命名规则有相同之处，属性值可以是任何JavaScript值，包括其他对象。
```JavaScript
var beatles = {};
beatles.vocalist = lennon;
```
那么，beatles.vocalist.name的值是“John”，beatles.vocalist.year的值是1940, beatles.vocalist.living的值是false。

### 操作
#### 算术操作（arithmetic operation）符
```JavaScript
var message = "I am feeling" + "happy";
var year =2010;
var message = "The year is";
message += year;
```
### 条件语句 conditional statement
```JavaScript
if(condition){
  statement;
}
```

相等操作符`==`并不表示严格相等，这一点很容易让人犯糊涂。例如，比较false与一个空字符串会得到什么结果？
```JavaScript
var a = false;
var b = "";
if (a==b)
{alert("a equals b");}
```
这个条件语句的求值结果是true，为什么？因为相等操作符==认为空字符串与false的含义相同。要进行严格比较，就要使用另一种等号（===）。这个全等操作符会执行严格的比较，不仅比较值，而且会比较变量的类型：
```JavaScript
var a = false;
var b = "";
if (a === b)
{alert("a equals b");}
```
这次表达式求值结果就是false了。因为即使可以认为false与空字符串具有相同的含义，但Boolean和String可不是一种类型。
当然，对于不等操作符`!=`也是如此。如果想比较严格不相等，就要使用`!==`。

### 循环语句
#### while循环
```JavaScript
while (condition){
statements;
}
```

```JavaScript
do {
statements;
} while(condition);
```

#### for循环
```JavaScript
for (initial_condition; test_condition; alter_condition){
statements;
}
```
for循环最常见的用途之一是对某个数组里的全体元素进行遍历处理。这往往用到数组的`array.length`属性。
```JavaScript
var beatles = Array("John", "Paul", "George", "Ringo");
for (var count=0; count < beatles.length; count++){
alert(beatles[count]);
}
```

### 函数
作为一个良好的编程习惯，应先对函数做出定义再调用它们。
```JavaScript
function name(arguments){
statements;
}
```

### 对象object
对象是自包含的数据集合，包含在对象里的数据可以通过两种形式访问——属性（property）和方法（method）。
+ 属性是隶属于某个特定对象的变量；
+ 方法是只有某个特定对象才能调用的函数；

对象就是由一些属性和方法组合在一起而构成的一个数据实体。
在JavaScript里，属性和方法都使用“点”语法来访问：
`Object.property`
`Object.method()`

为给定对象创建一个新的实例需要使用`new`关键字，如下所示：
```JavaScript
var jeremy = new Person;
```
#### 内建对象 native object
Math
Date
```javascript
var current_date = new Date();
var beatles = new Array();
```

#### 宿主对象 host object
除了内建对象，还可以在JavaScript脚本里使用一些已经预先定义好的其他对象。这些对象不是由JavaScript语言本身而是由它的运行环境提供的。具体到Web应用，这个环境就是浏览器。由浏览器提供的预定义对象称为宿主对象。
宿主对象包括Form、Image和Element等。另一种宿主对象也能用来获得网页上的任何一个元素的信息，它就是`document`对象。
