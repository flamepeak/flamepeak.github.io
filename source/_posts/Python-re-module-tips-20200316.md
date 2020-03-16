---
title: Python re 模块 贪婪模式和非贪婪模式
date: 2020-03-16 08:08:12
tags: [Python]
categories: [Python]
---

Python里数量词默认是贪婪的，总是尝试匹配尽可能多的字符；非贪婪则相反，总是尝试匹配尽可能少的字符。在`*`,`?`,`+`,`{m,n}`后面加上`?`，使贪婪变成非贪婪。

```python
>>> s="This is a number 234-235-22-423"
>>> r=re.match(".+(\d+-\d+-\d+-\d+)",s)
>>> r.group(1)
'4-235-22-423'
>>> r=re.match(".+?(\d+-\d+-\d+-\d+)",s)
>>> r.group(1)
'234-235-22-423'
```



正则表达式模式中使用到通配字，那它在从左到右的顺序求值时，会尽量“抓取”满足匹配最长字符串

解决方式：非贪婪操作符`?`，这个操作符要求正则匹配的越少越好。

```python
>>> re.match(r"aa(\d+)","aa2343ddd").group(1)
'2343'
>>> re.match(r"aa(\d+?)","aa2343ddd").group(1)
'2'
>>> re.match(r"aa(\d+)ddd","aa2343ddd").group(1) 
'2343'
>>> re.match(r"aa(\d+?)ddd","aa2343ddd").group(1)
'2343'
```





