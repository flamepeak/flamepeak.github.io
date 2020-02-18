---
title: Python 字典排序
date: 2016-09-06 17:51:46
tags: Python
categories: [Python]
---

Python中的字典元素没有顺序，不能像list一样通过序号读取元素，而且没有重复的键值。

但是我们如何给字典排序呢？

**sorted**
`sorted(iterable[, key][, reverse])`
> Return a new sorted list from the items in iterable.
> iterable 是要排序的对象，key为排序的键值
reverse：注明升序还是降序，True--降序，False--升序（默认）

e.g:
```Python
>>> dic = {'a':3 , 'b':2 , 'c': 1}
>>> sorted(dic.iteritems(), key =lambda x:x[1], reverse=True)
[('a', 3), ('b', 2), ('c', 1)]
>>> sorted(dic.iteritems(), key =lambda x:x[1])
[('c', 1), ('b', 2), ('a', 3)]
>>> sorted(dic.iteritems(), key =lambda x:x[0], reverse=True)
[('c', 1), ('b', 2), ('a', 3)]
```
 在`sorted(dic.iteritems(), key =lambda x:x[1], reverse=True)`中，第一个参数传给第二个参数“键-键值”，第二个参数取出其中的键([0])或键值(1])

**iteritems()**
功能: iteritems()以迭代器对象返回字典键值对
区别: 和item相比:items以list形式返回字典键值对



```Python
>>> dic
{'a': 3, 'c': 1, 'b': 2}
>>> print dic.items()
[('a', 3), ('c', 1), ('b', 2)]
>>> dic.iteritems
<built-in method iteritems of dict object at 0x0000000002E04378>
```
