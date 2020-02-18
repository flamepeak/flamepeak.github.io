---
title: "/bin/bash^M: bad interpreter: No such file or directory"
date: 2020-01-22 12:33:06
tags: [Linux]
categories: [Linux]
---


在运行`.sh`脚本时遇到这样的错误`/bin/bash^M: bad interpreter: No such file or directory`，Google之后才找到原因


在Windows中编写的脚本，直接在Linux下运行就可能会出现这样的问题。

解决办法是：

```bash
sed -i -e 's/\r$//' scriptname.sh
./scriptname.sh
```



