---
title: Linux 使用技巧
date: 2017-01-04 11:43:10
tags: [Linux, Tips]
categories: [Linux]
---

### Linux多个文件中查找指定字符串
```
# find <directory> -type f -name "*.c" | xargs grep "<strings>"

<directory>是你要找的文件夹；如果是当前文件夹可以省略
-type f 意思是只找文件
-name "*.c"  表示只找C语言写的代码，从而避免去查binary；也可以不写，表示找所有文件
<strings>是你要找的某个字符串
```

---

### Linux 在多个文件中批量替换字符串
```
sed -i "s/OldString/NewString/g"  `grep OldString -rl Dir`
```
例如：
将当前目录文件中含有的“unpacked”字符串替换为“mathjax27”
```
sed -i "s/unpacked/mathjax27/g" `grep unpacked -rl `
```

### Linux find "Permission denied" 解决

```bash
find / -name "admin"  2 > /dev/null
```
bash中0，1，2三个数字分别代表STDIN_FILENO、STDOUT_FILENO、STDERR_FILENO，即标准输入（一般是键盘），标准输出（一般是显示屏，准确的说是用户终端控制台），标准错误（出错信息输出）。

把错误信息写进Linux黑洞文件（`/dev/null`）,黑洞文件就是只写文件， 写进去后， 就被黑洞吸收了， 只可写， 不可读。


### notify-send 发送桌面通知
`notify-send`是一个可以让你发送桌面通知的命令.
```bash
notify-send [其他选项...] 标题 [内容]
```
**常用参数**
`-t –expire-time=TIME`
指定通知显示多长时间后消失,单位是毫秒. (Ubuntu的Notify OSD以及GNOME Shell会忽略该参数)
若参数为-1表示使用notification server上配置的超时时间.
若参数为0则表示永不自动消失.

`-u –urgency=LEVEL`
指定通知的级别,只能是 `low`, `normal`, `critical` 这三个值中的一个.
```Python
import os
mstr='Hello'
os.system('notify-send '+mstr) #subprocess.Popen(['notify-send',msg])
```
