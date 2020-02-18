---
title: 关于Linux批量重命名rename令人崩溃
date: 2018-08-10 16:36:09
tags: [Linux]
categories: [Linux]
---

需要批量重命名文件名，文件很多，分布在各个文件夹。

查了很多资料，各种尝试都不行，简直要崩溃。百度坑爹，各种搜索基本一个答案，最后Google到一个朋友的文章，看到我都要崩溃了。原因是语法的一点差异。。。

最后感慨有三点，一是遇到问题尽量自己读官方帮助，自己思考解决，不要盲目相信经验；二是，技术问题尽量用Google，不要在百度上浪费时间；三是，Linux的发行版太多了，各种小差异能害死人，希望今后能统一。

感谢此帖：https://foofish.net/linux-rename.html

在Linux系统中修改文件名可以用`mv`命令，但是它只能对单个文件进行操作，如要要批量执行还要写shell脚本，用for语句迭代执行，不过Linux中另外一个命令支持批量替换文件名，它是`rename`，`rename`支持正则表达式匹配。需要注意的是，`rename`命令在不同的Linux发行版语法格式不一样。

### 语法
在Debian或者Ubuntu环境下使用的语法是：
```
rename 's/stringx/stringy/' files
```

而在CentOS下或者RedHat下是：
```
rename stringx stringy files
```

rename的参数分为三部分：  
+ stringx ： 被替换字符串  
+ stringy ：替换字符串  
+ files ：匹配的文件列表  


### 例如
例如有如下文件，想把所有文件中的@符号去掉，

```
uodong_pic@2x.png
eixin_pic@2x.png
inkehu_pic@2x.png
anting_pic-@2x.png
huanfa_pic@2x.png
```

CentOS这么写：
```
rename \@2x.png 2x.png *.png
```

Ubuntu这么写：
```
rename 's/\@2x/2x/' *.png
```

### 我的问题
```
find . -name "*roselove*" | xargs rename roselove love *

```

