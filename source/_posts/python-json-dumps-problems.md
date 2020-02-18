---
title: python中json使用遇到一些编码问题
date: 2017-09-20 09:21:29
tags: [Python]
categories: [Python]
mathjax: true
---


### python中json.dumps使用的坑以及字符编码

在使用json.dumps处理dict时遇到编码问题，最后通过下面的文章解决，在此致谢！

重要参考：
[python中json.dumps使用的坑以及字符编码](http://www.cnblogs.com/stubborn412/p/3818423.html)

我们知道，python中的字符串分普通字符串和unicode字符串，一般从数据库中读取的字符串会自动被转换为unicode字符串

**下面回到重点，使用json.dumps时，一般的用法为：**

```Python
obj={"name":"测试"}

>>> json.dumps(obj)
'{"name": "\\u6d4b\\u8bd5"}'

>>> print json.dumps(obj)
{"name": "\u6d4b\u8bd5"}

>>> json.dumps(obj).encode("utf-8") 
'{"name": "\\u6d4b\\u8bd5"}'
```

可以看到这里输出的字符串为普通字符串，但是里面的内容却是unicode字符串的内容，即使对结果进行encode("utf-8") ，因为这个字符串本身就已经编码过了，所有进行encode不会有变化

要想得到字符串的真实表示，需要用到参数`ensure_ascii=False(默认为True)`：

```Python
>>> json.dumps(obj,ensure_ascii=False)  
'{"name": "\xe6\xb5\x8b\xe8\xaf\x95"}'

>>> print json.dumps(obj,ensure_ascii=False)
{"name": "测试"}
```
如果不加 `ensure_ascii=False`  输出的汉字都默认给转换成一堆编码 如果加上的话就能正常显示汉字

坑：试试下面的用法（比如key是从数据库中读取的，则会以unicode字符串形式存在）：

```Python
>>> key=u"name"
>>> obj={key:"测试"}   
>>> json.dumps(obj,ensure_ascii=False)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib64/python2.6/json/__init__.py", line 237, in dumps
    **kw).encode(obj)
  File "/usr/lib64/python2.6/json/encoder.py", line 368, in encode
    return ''.join(chunks)
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe6 in position 1: ordinal not in range(128)
```

这是因为key和value不能以混合普通字符串和unicode字符串的形式存在

改成下面则正常了（同时为普通字符串或同时为unicode字符串）：

```Python
>>> key=u"name"
>>> obj={key:u"测试"}
>>> json.dumps(obj,ensure_ascii=False)
u'{"name": "\u6d4b\u8bd5"}'
>>> obj={key.encode("utf-8"):u"测试".encode("utf-8")}
>>> json.dumps(obj,ensure_ascii=False)               
'{"name": "\xe6\xb5\x8b\xe8\xaf\x95"}'
```
 

另外说说还有一个参数default

考虑下面的场景：

```Python
>>> class Data:
...     def __init__(self):
...         self.name = ""
...         self.detail = ""
...

>>> data=Data()
>>> data.name="名字"
>>> data.detail="细节"
>>> obj={"data":data}
>>> json.dumps(obj,ensure_ascii=False)
```

会报下面的异常：

```Python
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib64/python2.6/json/__init__.py", line 237, in dumps
    **kw).encode(obj)
  File "/usr/lib64/python2.6/json/encoder.py", line 367, in encode
    chunks = list(self.iterencode(o))
  File "/usr/lib64/python2.6/json/encoder.py", line 309, in _iterencode
    for chunk in self._iterencode_dict(o, markers):
  File "/usr/lib64/python2.6/json/encoder.py", line 275, in _iterencode_dict
    for chunk in self._iterencode(value, markers):
  File "/usr/lib64/python2.6/json/encoder.py", line 317, in _iterencode
    for chunk in self._iterencode_default(o, markers):
  File "/usr/lib64/python2.6/json/encoder.py", line 323, in _iterencode_default
    newobj = self.default(o)
  File "/usr/lib64/python2.6/json/encoder.py", line 344, in default
    raise TypeError(repr(o) + " is not JSON serializable")
TypeError: <__main__.Data instance at 0x11e87e8> is not JSON serializable
```

这是因为json.dumps不知道如何对Data对象进行序列化，需要定义一个函数，并赋给参数default：

```Python
>>> def convert_to_builtin_type(obj):
...     d = {}
...     d.update(obj.__dict__)
...     return d
...

>>> json.dumps(obj,ensure_ascii=False, default=convert_to_builtin_type)
'{"data": {"name": "\xe5\x90\x8d\xe5\xad\x97", "detail": "\xe7\xbb\x86\xe8\x8a\x82"}}'
>>> print json.dumps(obj,ensure_ascii=False, default=convert_to_builtin_type)
{"data": {"name": "名字", "detail": "细节"}}

def convert_to_builtin_type(obj):
    d = {}
    d.update(obj.__dict__)
    return d
```




### python 自带json.loads() 默认解析为unicode问题

![python 自带json.loads() 默认解析为unicode问题](http://0ff.me/article/298)

官方自带的json库的loads()方法会把json文件解析成unicode对象, (大概是字符串前面加个u like -> u'zifuchuan' )
当你把它当做str处理的时候一般会抛出异常

用loads()解析后,用下面方法转为str再处理

```Python
#我也是搬运工
def byteify(input):
    if isinstance(input, dict):
        return {byteify(key): byteify(value) for key, value in input.iteritems()}
    elif isinstance(input, list):
        return [byteify(element) for element in input]
    elif isinstance(input, unicode):
        return input.encode('utf-8')
    else:
        return input
```

