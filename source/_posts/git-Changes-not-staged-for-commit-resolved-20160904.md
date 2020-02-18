---
title: git "Changes not staged for commit" 错误解决
date: 2016-09-04 11:54:45
tags: [Hexo, git]
categories:
---

在同步的过程中遇到错误
> Changes not staged for commit:

查了一下资料，然后找到了解决办法
因为我在一次commit的过程中，没有执行add，解决办法就是：
> 在执行commit之前，先使用git add yourhtml.html 或者git add .
然后再git commit -m "modify yourhtml.html"

或者，尝试
> git commit -a -m "modify yourhtml.html"

**原理：**
git 分为工作区和版本库，工作区是你的代码，版本库就是git记录了。
在修改进入版本库前有一个暂存区也叫索引，就是做git add 操作后记录的区域。当commit的时候是将暂存区的记录添加到版本数据库。你的提示说staged没有内容，也就是说你没有执行过add操作。
详情看这里：[工作区和暂存区](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013745374151782eb658c5a5ca454eaa451661275886c6000)
![git description](/sourcepictures/20160904/git_description.jpg)
