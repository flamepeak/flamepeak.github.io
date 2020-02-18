---
title: linux 查询目录下包含关键字的所有文件
date: 2018-08-10 08:54:01
tags: [Linux]
categories: [Linux]
---

查找目录下的所有文件中是否含有某个字符串 

`find .|xargs grep -ri "IBM" `

查找目录下的所有文件中是否含有某个字符串,并且只打印出文件名 

`find .|xargs grep -ri "IBM" -l `


linux 查找某目录下包含关键字内容的文件
```
grep -r "{关键字}"  {路径}
例如：
grep -r "test"  /data/reports
```

----

### 对find，xargs，grep和管道的一些深入理解
相信大家都知道在目录中搜索含有固定字符串文件的命令：
```
find . -name '*.py' |xargs grep test
 ```

刚开始的时候，我不熟悉xargs命令，所以直接使用的命令是
```
find . -name '*.py' |grep test
```
结果并不是自己所期望的。此命令只是找出文件名`*.txt`有test的情况。

这里我就研究一下，究竟xargs做了什么，使得结果不相同。

#### 参数与标准输入
这两个词我们在Linux命令中是很常见的。但是参数和标准输入其实是有区别的。我们日常使用的很多命令，例如`ls -lah` .中。l， a， h ，都是命令`ls`的参数。至于标准输入，可以说它某种流数据。而通常来讲标准输入的流数据来源就是我们的终端输入。在Linux命令中，有些命令可以接收标准输入，有些是不能的。像上面的ls，就是只能接收参数，不能接收标准输入。像`cat`命令或`echo`命令，这些是可以的。

怎么分辨一个命令可不可以接收标准输入？很简单，当你敲完命令回车后，终端会等待接收你的输入，例如当你在终端输入`cat`后，终端会等待你输入字符，当你输入一些字符后，然后按`Ctrl-C`发送终止符号。这时`cat`命令接收标准输入完毕，执行命令，也就是将刚才键入的内容输出的标准输出上（屏幕）。

#### 管道
管道的作用是将前面命令的标准输出作为后面命令的标准输入。这里要注意，后面的命令接收的是标准输入，所以如果命令不支持接收标准输入，那么就不能直接使用管道，例如常用的`ls`命令，只能使用参数，而不能使用标准输入，所以`[command] | ls`是不能使用的。而命令如`echo`或`cat`就可以。那么肯定有方法来实现这些不能使用标准输入的命令与管道结合，这时候`xargs`便出场了。

#### xargs命令
`xargs`命令通俗来讲就是将标准输入转成各种格式化的参数，所以命令`[command 1] | xargs [command 2]`就是将`command 1`的标准输出结果，通过管道`|`变成`xargs`的标准输入，然后`xargs`再将此标准输入变成参数，传给`[command 2]`。这样一来，通过`xargs`命令，我们便可以在管道后面使用那些不接收标准输入的命令了。例如`[command 1]|xargs ls`，是不是很熟悉？

#### find与grep
我们首先来查看grep手册。通过man grep命令。

> grep searches the named input FILEs (or standard input if no files are named, or if a single hyphen-minus (-) is given as file name) for lines containing a match to the given PATTERN.  By default, grep prints the matching lines

那么对于第一个命令`find . -name '*.py' |grep test`，是将前面命令的标准输出作为标准输入传给了`grep test`，那么`grep`是从这些标准输入寻找test字符，也就是文件名组成的字符流, 最终选择出的是这些文件名。

对于第二个命令`find . -name '*.py' |xargs grep test`，通过`xargs`，`find`得到的文件名成为了参数传给后面的`grep`，那么这时候这些文件名就是实实在在的文件标识，`grep`接收后会按正常的使用方式在各文件中搜寻字符串。

参考：https://www.baidu.com/link?url=ViL8A5oAhPKpqoK14dedk0pJuvXoH8ok2eHf6-cZJmRaTMYqLuzjR3eS14oCLtothy1RnbqLwtBnz4aTAfB17q&wd=&eqid=fa94666f0002d501000000035b6ce3d0


### grep 参数
```
-a 不要忽略二进制数据。
-A<显示列数> 除了显示符合范本样式的那一行之外，并显示该行之后的内容。
-b 在显示符合范本样式的那一行之外，并显示该行之前的内容。
-c 计算符合范本样式的列数。
-C<显示列数>或-<显示列数>  除了显示符合范本样式的那一列之外，并显示该列之前后的内容。
-d<进行动作> 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep命令将回报信息并停止动作。
-e<范本样式> 指定字符串作为查找文件内容的范本样式。
-E 将范本样式为延伸的普通表示法来使用，意味着使用能使用扩展正则表达式。
-f<范本文件> 指定范本文件，其内容有一个或多个范本样式，让grep查找符合范本条件的文件内容，格式为每一列的范本样式。
-F 将范本样式视为固定字符串的列表。
-G 将范本样式视为普通的表示法来使用。
-h 在显示符合范本样式的那一列之前，不标示该列所属的文件名称。
-H 在显示符合范本样式的那一列之前，标示该列的文件名称。
-i 忽略字符大小写的差别。
-l 列出文件内容符合指定的范本样式的文件名称。
-L 列出文件内容不符合指定的范本样式的文件名称。
-n 在显示符合范本样式的那一列之前，标示出该列的编号。
-q 不显示任何信息。
-R/-r 此参数的效果和指定“-d recurse”参数相同。
-s 不显示错误信息。
-v 反转查找。
-w 只显示全字符合的列。
-x 只显示全列符合的列。
-y 此参数效果跟“-i”相同。
-o 只输出文件中匹配到的部分。
```


### 查找并替换
#### sed
`grep -rl matchstring somedir/ | xargs sed -i 's/string1/string2/g' `
例如：
`grep -rl 'windows' ./path | xargs sed -i 's/windows/linux/g' `

