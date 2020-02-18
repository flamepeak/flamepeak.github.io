---
title: JavaScript DOM 编程艺术：DOM
date: 2016-12-21 15:30:46
tags: [笔记, JavaScript]
categories: [Web前端]
---


### 文档：DOM中的“D”
如果没有document（文档），DOM也就无从谈起。当创建了一个网页并把它加载到Web浏览器时，DOM就在幕后悄然而生。它把你编写的网页文档转换为一个文档对象。

### 对象：DOM中的“O”
与某个特定对象相关联的变量被称为这个对象的属性，只能通过某个特定对象去调用的函数被称为这个对象的方法。

JavaScript语言里的对象可以分为三种类型：
+ 用户定义对象（user-defined object）:由程序员自行创建的对象。
+ 内建对象（native object）：内建在JavaScript语言里的对象。
+ 宿主对象（host object）：由浏览器提供的对象。

即使在JavaScript的最初版本里，对编写脚本来说非常重要的一些宿主对象就已经可用了，它们当中最基础的对象是window对象。
window对象对应着浏览器窗口本身，这个对象的属性和方法通常统称为BOM（浏览器对象模型），但我觉得称为Window Object Model（窗口对象模型）更为贴切。BOM提供了`window.open`和`window.blur`等方法，这些方法某种程度上要为到处被滥用的各种弹出窗口和下拉菜单负责。
document对象的主要功能就是处理网页内容。

### 模型：DOM中的“M”
DOM中的M代表着“Model”（模型），但说它代表着“Map”（地图）也未尝不可。
浏览器提供了网页的地图（或者说模型），而我们可以通过JavaScript去读这张地图。

### 节点 node
节点（node）表示网络中的一个连接点，一个网络就是由一些节点构成的集合。
+ 元素节点（element node）
  文本段落元素的名字是`p`，无序清单元素的名字是`ul`，列表项元素的名字是`li`。
+ 文本节点（text node）
  文本节点总是被包含在元素节点内部。
+ 属性节点（attribute node)
  属性节点用来对元素做出更具体的描述。

例如：
```JavaScript
<p title="a gentle reminder">Do not forget to buy this stuff.</p>
```
在上面DOM中，`title="a gentle reminder"`是一个属性节点，`p`是一个元素节点，`Don't forget to buy this stuff.`是文本节点。
如图：
![节点](/sourcepictures/20161221/node.jpg)


**CSS**
DOM并不是与网页结构打交道的唯一技术。我们还可以使用CSS（层叠样式表）告诉浏览器应该如何显示一份文档的内容。

### 获取元素
获取元素的方法主要有：getElementById()、getElementsByTagName()、getElementsByClassName()、getElementsByName()、

#### getElementById
返回对拥有指定ID的第一个对象的引用。
JavaScript区分大小写。
它是document对象特有的函数。在脚本代码里，函数名的后面必须跟有一对圆括号，这对圆括号包含着函数的参数。`getElementById`方法只有一个参数：你想获得的那个元素的id属性的值，这个id值必须放在单引号或双引号里。
`document.getElementById(id)`
e.g:
```JavaScript
document.getElementById("purchases")
```

#### getElementsByTagName
返回带有指定标签名的对象的集合。这个方法只有一个参数，它的参数是标签的名字：
`element.getElementsByTagName(tag)`
注意，它返回的是一个**数组**。
e.g:
```javascript
document.getElementsByTagName("li")
```
getElementsByTagName允许把一个通配符作为它的参数，而这意味着文档里的每个元素都将在这个函数所返回的数组里占有一席之地。通配符必须放在引号里，这是为了让通配符和乘法操作符有所区别。如果你想知道某份文档里总共有多少元素节点，代码如下：
```javascript
document.getElementsByTagName("*").length
```
e.g:
```javascript
var shopping = document.getElementById("purchase");
var items = document.getElementsByTagName("*");
```

#### getElementsByClassName
返回文档中所有指定类名的元素集合。其只接受一个参数，就是类名：
`getElementsByClassName(class)`
其返回结果为数组。
使用这个方法还可以查找那些带有多个类名的元素。要指定多个类名，只要在字符串参数中用空格分割类名即可。eg：
```javascript
document.getElementsByClassName("important sale")
```
上述代码将会匹配同时带有“important”和“sale”的对象。
一些老版本的浏览器不支持getElementsByClassName,可以使用以下代码：
```JavaScript
function getElementsByClassName(node, classname){
  if (node.getElementsByClassName){
    //使用现有方法
    return node.getElementsByClassName(classname);
  }
  else{
    var results = new Array();
    var elems = node.getElementByTagName("*");
    for(var i=0; i<elems.length; i++){
      if(elems[i].className.indexOf(classname) != -1){
        results[results.length] = elems[i];
      }
    }
  return results;
  }
}

```

#### getElementsByName
该方法与getElementById()方法相类似，但是它查询的是元素的`name`属性，而不是`id`属性。
因为一个文档中的name属性可能不唯一，所以它返回的方法是元素的数组，而不是一个元素。

#### getElementsByTagNameNS(ns,name)
返回带有指定名称和命名空间的所有元素的一个节点列表。
该方法与 getElementsByTagName() 方法相似，只是它根据命名空间和名称来检索元素。只有使用命名空间的 XML 文档才会使用它。
参数：
+ ns，字符串值，规定需要检索的命名空间名称。
+ name, 字符串值，规定要检索的标签名。


---

### 获取和设置属性
我们已经知道获取元素的方法：getElementById()、getElementsByName()、getElementsByTagName()、getElementsByClassName()
得到元素之后，我们可以设法获取它的属性。

#### getAttribute（）
获取属性
`object.getAttribute(attribute)`
getAttribute方法不属于document对象，所以不能通过document对象调用。它只能通过元素节点对象调用。
```javascript
document.getElementsByTagName("a")[107]
输出：<a class="strong" href="http://ln.ifeng.com/a/20161223/5258480_0.shtml" target="_blank">辽宁:向25个单位反馈巡视情况 一针见血指出问题</a>

document.getElementsByTagName("a")[107].getAttribute("href")
输出："http://ln.ifeng.com/a/20161223/5258480_0.shtml"
```

#### setAttribute()
对属性节点的值作出修改。
`object.setAttribute(attribute, value)`
```javascript
#接上例
document.getElementsByTagName("a")[107].setAttribute("href","flamepeak.com")
##然后
document.getElementsByTagName("a")[107].getAttribute("href")
#输出
“flamepeak.com”
```

#### childNodes属性
一棵节点树上，childNodes属性可以用来获取任何一个元素的所有子元素，它一个包含这个元素全部子元素的数组。
*element.childNodes*

#### nodeType属性
由childNodes属性返回的数组包含所有类型的节点，而不仅仅是元素节点。事实上，文档里几乎每一样东西都是一个节点，甚至连空格和换行符都会被解释为节点，而它们也全部包含在childNodes属性所返回的数组当中。
还好，每一个节点都有一个nodeType属性，这个个属性可以让我们知道自己正在和哪种节点打交道，差劲的一点是nodeType的值并不是英文。
获取节点的nodeType属性的语法是：
*node*.nodeType
nodeType属性共有12种可取的值，但其中最重要的是：
+ **元素节点** 的nodeType属性值是1.
+ **属性节点** 的nodeType属性值是2.
+ **文本节点** 的nodeType属性值是3.
+ **注释** 的nodeType属性值是8.
+ **文档** 的nodeType属性值是9.

#### nodeValue属性
如果想改变一个文本节点的值，那就使用DOM提供的`nodeValue`属性,它用来得到一个节点的值。
*node*.nodeValue

#### firstChild和lastChild属性
无论何时，只要需要访问childNodes数组的第一个元素，都可以把它写成`firstChild`:
*node*.firstChild
其与下面的写法完全等价：
*node*.childNodes[0]

与之对应，还有一个`lastChild`属性：
*node*.lastChild
其与下列语法完全等价：
*node*.childNodes[*node*.childNodes.lenght - 1]


---

### 事件处理函数
事件处理函数的作用是，在特定事件发生时调用特定的JavaScript代码。
添加事件处理函数的语法是：
*event* = *"JavaScript statement(s)"*
需要注意的是，JavaScript代码包含在一对引号之间。可以吧任意数量的JavaScript语句放在这对引号之间，只要把各条语句用分号隔开即可。
例如：`onClick = "showPic(this)"`
其中，`this`关键字代表当前这个语句。

事件处理函数的工作机制：
在给某个元素添加了事件处理函数后，一旦事件发生，相应的JavaScript代码就会得到执行。被调用的JavaScript代码可以返回一个值，这个值将会被传递给那个事件处理函数。例如，我们可以给某个链接添加一个onClick事件处理函数，并让这个处理函数触发的JavaScript代码返回值是true或者false。这样一来，当这个链接不点击时，如果那段JavaScript代码返回的是true，onClick事件处理函数就认为“这个链接被点击了”，反之，如果返回值是false，onClick事件处理函数就认为“这个链接没有被点击”。
例如，下面的代码：
```javascript
<a href="images/fire.jpg" onclick="showPic(this);">Fire</a>
```
其在点击后会调用showPic(this)，然后跳转打开链接。
更改为下面的之后：
```javascript
<a href="images/fire.jpg" onclick="showPic(this);return false;">Fire</a>
```
这样，点击后调用执行showPic(this)，然后执行return false, 这样不会跳转。



---

### 最佳实践
+ 确保网页在没有JavaScript的情况下也能正常工作
+ 把网页的结构和内容与JavaScript脚本的动作行为分开。
+ 向后兼容，确保老版本的浏览器不会因为你的JavaScript脚本死掉
+ 性能考虑，确定脚本执行的性能最优

易学易用的技术是一把双刃剑。因为容易学习和掌握，它们会在很短的时间内就为人们广泛接受，但也往往意味着缺乏高水平的质量控制措施。

JavaScript使用window对象的`open()`方法来创建新的浏览器窗口。这个方法有三个参数：
`window.open(url, name, features)`
这三个参数都是可选的。第一个参数是想在浏览器里打开的网页的URL地址。如果省略这个参数，屏幕上将弹出一个空白的浏览器窗口。第二个参数是新窗口的名字。可以在代码里通过这个名字与新窗口进行通信。最后一个参数是一个以逗号分割的字符串，其内容是新窗口的各种属性。
```javascript
function popUp(winURL){
  window.open(winURL, "popup", "width=320, height=480");
}
```

#### "javascript:" 伪协议
“真”协议用来在因特网上的计算机之间传输数据包，如HTTP协议、FTP协议。伪协议则是一种非标准化的协议，“javascript:” 伪协议使我们通过一个链接来调用JavaScript函数。
下面是通过“javascript：”伪协议调用popUp（）函数的具体做法。
```javascript
<a href="javascript:popUp('http://www.example.com/');">Example</a>
```
正确的写法为：
```javascript
<a href="http://www.example.com/" onclick="popUp(this.href);return false;">Example</a>
```

#### CSS 层叠样式表
表面上看，CSS技术并无新内容，CSS能够做到的用<table>和<font>等标签也可以做到。CSS技术最大的优点是，他能够帮助你将Web文档的内容结构和版面设计分离开来。
我们经常会遇到一些几乎每个元素都带有style属性的Web文档，而这是CSS技术最缺乏效率的用法之一。真正能从CSS技术获益的方法，是把样式全部转移到外部文件中去。

CSS的突出优点，文档结构与文档样式的分离可以确保网页都能平稳退化。具备CSS支持的浏览器可以把网页呈现的美仑美焕，不支持的浏览器也可以把网页正确的显示出来。

要获得最佳的效果，应该把CSS代码从HTML文档里分离出来放在一些外部文件里，下面的代码可以运行，但是这种做法弊大于利：
```html
<p style="font-weight:bold; color:red;">
Be careful!
</p>
```
更好的做法是，先把样式信息存入一个外部文件，再把文档的head部分用`<link>`标签来调用这个文件：
```html
.warning {
  font-weight:bold;
  color: red;
}
```
`class`属性是样式与文档内容之间的联接纽带：
```html
<p class="warning">
Be careful!
</p>
```
这样做更容易理解和阅读，也便于以后修改。

#### 分离JavaScript
类似于使用`style`属性，在HTML文档里使用诸如`onclick`之类的属性也是一种既没有效率又容易引发问题的做法。如果我们使用一种“挂钩”, 就像CSS机制中的`class`或`id`属性那样，把JavaScript代码调用行为与HTML文档的结构和内容分离开，网页就会健壮的多。能否用下面的的语句表明“当这个链接被点击时，它将调用popUp()函数”的意思呢？
`<a href="http://www.example.com/" class="popup">Example</a>`
很高兴的是：完全可以这样做。JavaScript语言不要求事件必须在HTML文档里处理，我们可以在外部JavaScript文件里把一个事件添加到HTML文档的某个元素上：
*element.event = action...*
关键是怎样才能确定应该获得这个事件的元素。这个问题可以利用`class`或`id`属性来解决。
如果想把一个时间添加到某个带有特定`id`属性的元素上，用`getElementById`就可以解决问题：
`getElementById(id).event = action`
如果事情涉及多个元素，我们可以用`getElementsByTagName`和`getAttribute`把事件添加到有着特定属性的一组元素上。
代码如下：
```javascript
var links = document.getElementsByTagName("a");
for (var i=0; i< links.length; i++){
  if (links[i].getAttribute("class")=="popup"){
    links[i].onclick = function(){
      popUp(this.getAttribute("href"));
      return false;
     }
   }
}
```
以上代码把调用`popUp()`函数的`onclick`事件添加到有关的链接上。只要把他们存入一个外部JavaScript文件，就等于把这些操作从HTML文档中分离出来了。

还有一个问题要解决：如果把这段代码存入一个外部JavaScript文档，它们将无法正常运行。因为这段代码的第一行是：
`var links = document.getElementsByTagName("a");`

这条语句将在JavaScript文件被加载时立刻执行。如果JavaScript文件是从HTML文档的`<head>`部分用`<script>`标签调用的，它将在HTML文档之前加载到浏览器里。同样，如果`<script>`位于文档底部`</body>`标签之前，就不能保证哪个文件最先结束加载。因为脚本加载时文档可能不完整，所以模型也不完整，没有完整的DOM，getElementsByTagName等方法就不能正常工作。
必须让这些代码在HTML文档全部加载到浏览器之后马上开始执行。还好，HTML文档全部加载完毕时将触发一个事件，这个事件有它自己的事件处理函数。
文档将被加载到一个浏览器窗口里，`document`对象又是`window`对象的一个属性。当window对象出发`onload`事件时，`document`对象已经存在。

我们将把JavaScript代码打包在一个prepareLinks函数里，并把这个函数添加到`window`对象的`onload`事件上去。这样一来，DOM就可以正常工作了：
```javascript
window.onload = prepareLinks;
function prepareLinks(){
  if(!document.getElementsByTagName) return false; //对象检测
  var links = document.getElementsByTagName("a");
  for (var i=0; i< links.length; i++){
    if (links[i].getAttribute("class")=="popup"){
      links[i].onclick = function(){
        popUp(this.getAttribute("href"));
        return false;
       }
     }
  }
}
```

#### 性能考虑
为了保证流畅的运行，在编写脚本时应注意一些问题。
**尽量少访问DOM和尽量减少标记**
  访问DOM的方式对脚本性能产生非常大的影响。

**合并和放置脚本**
  将脚本尽可能的放置在一个脚本文件，这样可以减少加载页面时发送的请求数量。

**压缩脚本**
  所谓压缩脚本，指的是把脚本文件中不必要的字节，如空格和注释，统统删除，达到压缩文件的目的。有很多工具可以替你做这件事，甚至重写代码，使用更短的变量名，从而减少文件大小。

#### 共享onload事件
假设我们有两个函数：firstFunction和secondFunction。如果想让它们俩都在页面加载时得到执行，应该怎么办？如果把它们逐一绑定到onload事件上，它们当中将只有最后那个才被实际执行：
window.onload = firstFunction;
window.onload = secondFunction;
有一种办法可以，可以先创建一个匿名函数来容纳这两个函数，然后把匿名函数绑定到onload事件上，如下所示：
```javascript
window.onload = function(){
  firstFuction();
  secondFuction();
}
```
在需要绑定的函数不是很多的场合，这应该是最简单的解决方案。
还有一个更好的解决方案——不管你打算在页面加载完毕时执行多少个函数，它都可以应付自如。这个函数名字是`addLoadEvent`，它由Simon Willison编写：
```javascript
function addLoadEvent(func){
  var oldonload = window.onload;
  if (typeof window.onload != "function"){
    window.onload = func;
  }else {
    window.onload = function(){
      oldonload();
      func();
    }
  }
}
```
它将那些在页面加载完毕时执行的函数创建一个队列。如果把刚才那个函数添加到这个队列里去，只需要写出以下代码即可：
addLoadEvent(firstFunction);
addLoadEvent(secondFunction);


---

### 动态创建标记
#### document.write
document对象的`write()`方法可以方便快捷的把字符串插入到文档里。
它的最大缺点是违背了”行为应该与表现相分离“的原则。应该避免使用。

#### innerHTML属性
现如今的浏览器几乎都支持innerHTML，其已经包含到HTML5规范中。
innerHTML属性可以用来读、写某给定元素里的HTML内容。
要想获得细节，就必须使用DOM方法和属性，标准化的DOM就像DOM一样精细，innerHTML属性就像一把大锤那样粗放。
在你需要把大段的HTML内容插入网页时，innerHTML属性更适用。它既支持读取，又支持写入，你不仅可以用它来读取元素的HTML内容，还可以用它把HTML内容写入元素。利用这个技术无法区分”插入一段HTML内容“和”替换一段HTML内容“。一旦使用了innerHTML属性，它的全部内容都将被替换。

类似于document.write方法，innerHTML属性也是HTML专有属性，不能用于其他任何标记语言。在任何时候，标准的DOM都可以用来替代innerHTML。

### DOM方法
在DOM看来，一个文档就是一颗节点树。如果你想在节点树上添加内容，就必须插入新的节点。如果你想添加一些标记到文档，就必须插入元素节点。
#### createElement方法
创建一个新的元素。
documnent.createElement(*nodeName*)

#### appendChild方法
把新创建的节点插入某个文档的节点树的最简单的办法是，让它成为这个文档某个现有节点的一个子节点。语法：
parent.appendChild(*child*)

#### createTextNode 方法
创建一个文本节点可以使用`createTextNode`,语法：
document.createTextNode(*text*)

#### insertBefore 方法
把一个新元素插入到一个现有元素的前面。语法：
parentElement.insertBefore(*newElement*, *targetElement*)
我们不必搞清楚父元素到底是哪个，因为targetElement元素的`parentNode`属性值就是它。在DOM中，元素节点的父元素必须是另一元素节点。
但是没有insertAfter()方法，但是我们可以编写一个：
```javascript
function insertAfter(newElement, targetElement){
  var parent = targetElement.parentNode;
  if (parent.lastChild==targetElement){
    parent.appendChild("newElement");
  }else {
    parent.insertBefore(newElement, targetElement.nextSibling);
  }
}
```

---

### Ajax
2005年，Adaptive Path公司的Jesse James Garrett发明了Ajax这个词，用于概括异步加载页面内容的技术。以前，Web应用都要涉及大量的页面刷新：用户点击某个链接，请求发回服务器，然后服务器根据用户的操作再返回新页面。即便用户看到的只是页面中的一小部分有变化，也要刷新和重新加载整个页面。

使用Ajax就可以做到只更新页面的一小部分。Ajax的主要优势就是对页面的请求以异步方式发送到服务器，而服务器不会用整个页面来响应请求，它会在后台处理请求，与此同时用户还能继续浏览页面并与页面交互。你的脚本则可以按需加载和创建页面内容，而不会打断用户的浏览体验。利用Ajax，Web应用可以呈现出丰富的、交互敏捷的体验。

和任何新技术一样，Ajax有它的适用范围，它依赖JavaScript，所以可能会有浏览器不支持它，而搜索引擎的爬虫也不会抓取到有关内容。

Ajax应用主要依赖于服务器端处理，而非客户端处理。

#### XMLHttpRequest对象
Ajax技术的核心就是`XMLHttpRequest`对象，这个对象充当着浏览器中的脚本（客户端）与服务器之间的中间人角色。以往的请求都由浏览器发出，而JavaScript通过这个对象可以自己发送请求，同时也自己处理响应。


---

### 应该怎么做
+ 渐进增强（progressive enhancement）：渐进增强原则基于这样一种思想：你应该总是从最核心的部分，也就是从内容开始。应该根据内容使用标记实现良好的结构，然后再逐步加强这些内容。这些增强工作既可以是通过CSS改进呈现效果，也可以是通过DOM添加各种行为。
+ 平稳退化。渐进增强的实现必然支持平稳退化。如果你按照渐进增强的原则去充实内容，你为内容添加的样式和行为就自然支持平稳退化，那些缺乏必要的CSS和DOM支持的访问者仍可以访问到你的核心内容。

#### 选用HTML、XHTML还是HTML5
对于标记而言，选用HTML还是XHTML是你的自由。重要的是不管选用的哪种类型文档，你使用的标记必须与你的`DOCTYPE`声明保持一致。

HTML既允许使用大写字母，也允许使用小写字母；XHTML要求所有标签名和属性名都必须使用小写字母。
HTML在某些情况下会允许省略结束标签。而在XHTML世界里，所有的标签都必须闭合——对诸如`<img>`和`<br>`之类的孤立元素也不例外，在书写时它们必须有一个反斜线字符表示标签结束：即`<img/>`和`<br/>`这样。

若要使用XHTML DOCTYPE，应该将下列内容写在文档的开头：
```html
<!DOCTYPE html
  PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
  "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```
另一种方案更简单，那就是使用HTML5的文档类型声明，它非常简单：
`<!DOCTYPE html>`
而且这个声明同样也支持HTML和XHTML标记。

---

### 三位一体的网页
+ 结构层（structural layer）：由HTML或XHTML之类的标记语言负责创建。
+ 表示层（presentation  layer）：由CSS负责完成。CSS描述了网页应该如何呈现。
+ 行为层（behavior layer）：负责内容应该如何响应事件这一问题。这是JavaScript语言和DOM主宰的领域。

具体到网页设计工作中：
+ 使用(X)HTML搭建文档的结构;
+ 使用CSS设置文档的呈现效果；
+ 使用JavaScript脚本实现文档的行为；

例如：
将下面的CSS代码存入文件styles.css：
```css
p#example {
  color: grey;
  font: 12px 'Arial', sans-serif;
}
```
把html代码写入example.html文件中，并在开头部分加上一个`link`元素指向styles.css文件：
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8"/>
    <title>Test</title>
    <link rel="stylesheet" media="screen" href="styles/styles.css" />
  </head>
  <body>
    <p id="example">An example of a paragraph</p>
  </body>
</html>
```
通过CSS声明样式的具体做法主要有三种:
1. 第一种是为标签元素（比如p元素）统一的声明样式，如：
```css
p {
  font-size: 1em;
}
```
2. 第二种是为特定class属性的所有元素统一声明样式，如:
```css
.fineprint {
  font-size: .8em;
}
```
3. 第三种是为有独一无二id属性的元素单独声明样式，如：
```css
#intro {
  font-size: 1.2em;
}
```
也可以为有类似属性的多个元素声明样式，如下所示：
```css
input[type*="text"]{
  font-size: 1.2em;
}
```

只要有可能，最好选用CSS为文档设置样式。
CSS提供的`:hover`等伪class属性允许我们根据HTML元素的状态来改变样式。DOM可以通过`onmouseover`等事件对HTML元素的状态变化做出相应。如何判断该选用哪种方法呢？
最简单的办法就是选择容易实现的方法。比如，如果只想让链接在鼠标指针悬停在其上时改变颜色，就应该选用CSS：
```css
a:hover {
  color: #c60;
}
```
伪类`:hover`已经得到绝大多数浏览器的支持。
使用DOM实现也可以，将下列代码存入文件highlightRows.js并把它放入scripts文件夹，然后在主html文档`</body>`之前增加一个如下的`<script>`标签：
`<script src="scripts/hightlightRows.js"></script>`:
```javascript
function hightlightRows() {
  if (!document.getElementsByTagName) return false;
  var rows = document.getElementsByTagName("tr");
  for(var i=0; i<rows.length; i++) {
    row[i].onmouseover = function(){
      this.style.fontWeight = "bold";
    }
    row[i].onmouseout = function(){
      this.style.fontWeight = "normal";
    }
  }
}
addLoadEvent(highlightRows);
```
如果想要改变某个元素的呈现效果，使用CSS；如果想改变某个元素的行为，使用DOM；如果你想根据某个元素的行为去改变它的呈现效果，请用你的智慧，这个问题没有放之四海皆准的答案。

---

### TEMP
#### className 属性
`className`属性是一个可读/可写的属性，凡是**元素节点**都有这个属性。
可以用className属性得到一个元素的`class`属性：
element.className
用className属性和赋值操作符设置一个元素的class属性：
element.className = value
但是，通过className属性设置某个元素的class属性时将替换（而不是追加）该元素原有的class的设置。
但可以使用字符串拼接操作，把新的class设置值追加到className属性上。例如：
```js
function addClass(element, value){
  if (!element.className){
    element.className = value;
  }else {
    newClassName = element.className;
    newClassName += " ";
    newClassName += value;
    element.className = newClassName;
  }
}
```
