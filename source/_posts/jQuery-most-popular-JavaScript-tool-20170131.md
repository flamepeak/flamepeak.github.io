---
title: jQuery Tips
date: 2017-01-31 16:10:38
tags: [jQuery, JavaScript, HTML]
categories: [Web前端]
---
[jQuery: write less, do more.](http://jquery.com/)
[jQuery: API](https://api.jquery.com/)
[jQuery: Learning Center](https://learn.jquery.com/)
[jQuery: Try](http://try.jquery.com/)

[jQuery API 3.1.0 速查表](http://jquery.cuishifeng.cn/)

jQuery是一个快速、简洁的JavaScript框架，是继Prototype之后又一个优秀的JavaScript代码库（或JavaScript框架）。jQuery设计的宗旨是“write Less，Do More”，即倡导写更少的代码，做更多的事情。它封装JavaScript常用的功能代码，提供一种简便的JavaScript设计模式，优化HTML文档操作、事件处理、动画设计和Ajax交互。

jQuery的核心特性可以总结为：具有独特的链式语法和短小清晰的多功能接口；具有高效灵活的css选择器，并且可对CSS选择器进行扩展；拥有便捷的插件扩展机制和丰富的插件。

## Basic
+ Script tags and document ready
  ```JavaScript
  <script>
    $(document).ready(function(){

    });
  </script>
  ```
  The important thing to know is that code you put inside this `function` will run as soon as your browser has loaded your page.
  This is important because without your `document ready function`, your code may run before your HTML is rendered, which would cause bugs.
+ All jQuery functions start with a `$`, usually referred to as a `dollar sign operator`, or as `bling`.
  jQuery often selects an HTML element with a `selector`, then does something to that element.
+ `.addClass()` function, which allows you to add classes to elements.
  为每个匹配的元素添加指定的类名。
+ `addClass()`: 从所有匹配的元素中删除全部或者指定的类。
+ jQuery has a function called `.css()` that allows you to change the CSS of an element.
  ```JavaScript
  $("#target1").css("color", "blue");
  ```
  This is slightly different from a normal CSS declaration, because the CSS property and its value are in quotes, and separated with a comma instead of a colon.

  `css(name|pro|[,val|fn])`: 访问匹配元素的样式属性。
+ You can also change the non-CSS properties of HTML elements with jQuery. For example, you can disable buttons.  When you disable a button, it will become grayed-out and can no longer be clicked.
  jQuery has a function called `.prop()` that allows you to adjust the properties of elements.

  `prop(name|properties|key,value|fn)`: 获取在匹配的元素集中的第一个元素的属性值。
+ jQuery has a function called `.html()` that lets you edit HTML tags and text within an element.
  `html([val|fn])`: 取得第一个匹配元素的html内容。这个函数不能用于XML文档。但可以用于XHTML文档。
  在一个 HTML 文档中, 我们可以使用 .html() 方法来获取任意一个元素的内容。 如果选择器匹配多于一个的元素，那么只有第一个匹配元素的 HTML 内容会被获取。

  jQuery also has a similar function called `.text()` that only alters text without adding tags. In other words, this function will not evaluate any HTML tags passed to it, but will instead treat it as the text you want to replace the existing content with.
  `text([val|fn])`: 取得所有匹配元素的内容。结果是由所有匹配元素包含的文本内容组合起来的文本。这个方法对HTML和XML文档都有效。
+ jQuery has a function called `.remove()` that will remove an HTML element entirely.
  `remove([expr])`: 从DOM中删除所有匹配的元素。
+ **Use appendTo to Move Elements**
  jQuery has a function called `appendTo()` that allows you to select HTML elements and append them to another element.
  `appendTo(content)`: 把所有匹配的元素追加到另一个指定的元素元素集合中。实际上，使用这个方法是颠倒了常规的`$(A).append(B)`的操作，即不是把B追加到A中，而是把A追加到B中。
  用于内部插入的函数还有：`append()、prepend()、prependTo()`
+ **Clone an Element Using jQuery**
  jQuery has a function called `clone()` that makes a copy of an element.
  `clone([Even[,deepEven]])`: 克隆匹配的DOM元素并且选中这些克隆的副本。在想把DOM文档中元素的副本添加到其他位置时这个函数非常有用。
  ```JavaScript
  $("#target2").clone().appendTo("#right-well");
  ```
+ **Target the Parent of an Element**
  Every HTML element has a `parent` element from which it `inherits` properties.
  jQuery has a function called `parent()` that allows you to access the parent of whichever element you've selected.
+ **Target the Children of an Element**
  Many HTML elements have `children` which `inherit` their properties from their parent HTML elements.
  jQuery has a function called `children()` that allows you to access the children of whichever element you've selected.
+ **Target a Specific Child of an Element**
  jQuery uses CSS Selectors to target elements. `target:nth-child(n)` css selector allows you to select all the nth elements with the target class or element type.
  `:nth-child`: 匹配其父元素下的第N个子或奇偶元素,`:nth-child`从1开始的
+ **Target Even Numbered Elements**
  `:odd`: 匹配所有索引值为奇数的元素，从 0 开始计数
  `:even`: 匹配所有索引值为偶数的元素，从 0 开始计数
