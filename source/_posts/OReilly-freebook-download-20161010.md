---
title: O'Reilly 免费电子书 下载
date: 2016-10-10 18:36:33
tags:
categories: [Tools]
---

著名计算机科学类图书发行商 O'Reilly 向用户提供了数十本免费的电子书，这些电子书根据其类别分为[编程类](http://www.oreilly.com/programming/free/)，[数据科学和人工智能类](http://www.oreilly.com/data/free/)，[安全类](http://www.oreilly.com/security/free/)，[Web开发类](http://www.oreilly.com/web-platform/free/)，以及[物联网类](http://www.oreilly.com/iot/free/)。这些电子书很多是近期出版的，如《Swift Pocket Reference》，《Real-World Maintainable Software》。电子书提供了epub、mobi和PDF三种版本，下载这些电子书需要注册，但有人发布了直接下载的[链接](https://www.reddit.com/r/learnprogramming/comments/556kxj/oreilly_offering_programming_ebooks_for_free/)（只包含一部分）。

然后，笔者在网上了找到了不用注册的下载方法，感谢[潘家邦](https://yq.aliyun.com/articles/36565):
在浏览器中启动F12启动检查元素，然后在Console中执行以下命令：

    $.map($('body > article:nth-child(4) > div > section > div > a'), function(e){return e.href.replace(/free/, "free/files").replace(/csp.*/, "pdf")})

就会输出下载地址。
至于需要mobi、epub的朋友，只需要把代码中的pdf改为mobi、和epub即可。
上图：
![oreilly_freebook_download.jpg](/sourcepictures/20161010/oreilly_freebook_download.jpg)


对于有些获取不出来的页面（比如Data Science 和 IoT），可以尝试：
`$.map($('body > article:nth-child(5) > div > section > div > a'), function(e){return e.href.replace(/free/, "free/files").replace(/csp.*/, "pdf")})`

对于Programming：
`$.map($('body > article:nth-child(4) > div > section > div >div> a'), function(e){return e.href.replace(/free/, "free/files").replace(/csp.*/, "pdf")})`
