---
title: JavaScript DOM 编程艺术：动画
date: 2016-12-27 17:17:21
tags: [JavaScript, 笔记]
comments: true
categories: [Web前端]
---

### 动画基础知识
#### 位置
网页元素在浏览器窗口里的位置是一种表示性的信息。因此，位置信息通常是由CSS负责设置的。
下面的CSS代码对某个元素在网页上的位置进行了规定：
```CSS
element {
  position: absolute;
  top: 50px;
  left:100px;
}
```
下面是实现同样效果的DOM代码：
```javascript
element.style.position = "absolute";
element.style.left = "100px";
element.style.top = "50px";
```

#### 时间
JavaScript函数`setTimeout`能够让某个函数在经过一段预定时间之后开始执行。有两个参数，第一个参数是字符串，其内容是要执行的那个函数的名字，第二个参数是一个数值，它以毫秒为单位设定了需要多长时间后才开始执行第一个参数所给出的函数。
`setTimeout("function", interval)`
在绝大多数时候，把这个函数调用赋值给一个变量是个好主意：
`variable = setTimeout("function", interval)`

如果想取消某个正在排队等待执行的函数，就必须事先像上面这样把setTimeout函数赋值给一个变量。然后，用一个名为`clearTimeout`函数来取消“等待执行”队列里的某个函数。eg：
`clearTimeout(variable)`

JavaScript函数`parseInt`可以把字符串里的数值信息提取出来。如果把一个以数字开头的字符串传递给这个函数，它将返回一个数值。
`parseInt(string)`
parseInt函数返回值通常是整数。如果需要提取的是带小数点的数值（float），就应该使用相应的`parseFloat`函数：
`parseFloat(string)`

例如：
```javascript

window.onload=positionMessage

function positionMessage(){
    if (!document.getElementById) return false;
    if (!document.getElementById("example")) return false;
    var elem = document.getElementById("example");
    elem.style.position = "absolute";
    elem.style.left = "50px";
    elem.style.top= "50px";

    movement = setTimeout("moveMessage()", 1000);
}

function moveMessage(){
    if (!document.getElementById) return false;
    if (!document.getElementById("example")) return false;
    var elem = document.getElementById("example");
    var xpos = parseInt(elem.style.left);
    var ypos = parseInt(elem.style.top);
    if (xpos==200 && ypos ==200){
        return true;
    }
    if (xpos < 200){
      xpos++;
    }
    if (xpos>200){
        xpos--;
    }
    if(ypos<200){
        ypos++;
    }
    if(ypos>200){
        ypos--;
    }
    elem.style.left = xpos+"px";
    elem.style.top = ypos+"px";
    movement = setTimeout("moveMessage()", 10);

}
```
