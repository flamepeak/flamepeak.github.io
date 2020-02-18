---
title: Python 编码问题的一些经验与例子
date: 2017-08-16 08:29:00
tags: [Python]
categories: [Python]
---

### 推荐阅读

[说说 Python 字符编码的二三事](https://www.v2ex.com/t/339142)

### 将GB2312编码的字符串，转换回汉字

服务器传过来的字符串是人名使用 gb2312 编码后的字符串，例如：%BB%C6%B3%C9%CE%C4，黄成文，现在我要用 Python 把这个字符串转回汉字。

解决办法：

```Python
print type(urllib.unquote('%BB%C6%B3%C9%CE%C4')),urllib.unquote('%BB%C6%B3%C9%CE%C4'),repr(urllib.unquote('%BB%C6%B3%C9%CE%C4'))
result =  (urllib.unquote('%BB%C6%B3%C9%CE%C4')).decode('gb2312') #gbk也可以
print type(result),result
```
以上输出为：
```
<type 'str'> »Æ³ÉÎÄ '\xbb\xc6\xb3\xc9\xce\xc4'
<type 'unicode'> 黄成文
```

#### 延伸一下
请看下面代码，注意观察编码变化：

```Python
print type(urllib.unquote('%BB%C6%B3%C9%CE%C4')),urllib.unquote('%BB%C6%B3%C9%CE%C4'),repr(urllib.unquote('%BB%C6%B3%C9%CE%C4'))
rest =  (urllib.unquote('%BB%C6%B3%C9%CE%C4')).decode('gb2312')
print type(rest),repr(rest)
print rest.encode('utf-8'),repr(rest.encode('utf-8'))

car_owner_name = '黄成文'
print type(car_owner_name),repr(car_owner_name)

输出结果如下：
<type 'str'> ﾻƳÉÎÄ '\xbb\xc6\xb3\xc9\xce\xc4'

<type 'unicode'> u'\u9ec4\u6210\u6587' 

黄成文 '\xe9\xbb\x84\xe6\x88\x90\xe6\x96\x87'

<type 'str'> '\xe9\xbb\x84\xe6\x88\x90\xe6\x96\x87'

```


###



