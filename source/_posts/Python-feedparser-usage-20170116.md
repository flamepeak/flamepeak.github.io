---
title: Python feedparser 网页无法打开问题解决
date: 2017-01-16 22:20:49
tags: [Python]
categories: [Python]
---

在看Programming Collection Intelligence时，由于generatefeedvector.py代码使用的feed数据部分失效，导致有些feed无法打开，程序出错。
详细来说，由于d = feedparser.parse(url)使用的url失效，导致d.feed.title、d.status等出现下列错误
```
AttributeError: object has no attribute 'title'
AttributeError: object has no attribute 'status'
```

在查询[feedparser官方文档](https://pythonhosted.org/feedparser/)之后，发现有一种方法可以解决：Bozo Detection
Universal Feed Parser can parse feeds whether they are well-formed XML or not. However, since some applications may wish to reject or warn users about non-well-formed feeds, Universal Feed Parser sets the `bozo` bit when it detects that a feed is not well-formed.

**Detecting a non-well-formed feed**
```Python
>>> d = feedparser.parse('http://feedparser.org/docs/examples/atom10.xml')
>>> d.bozo
0
>>> d = feedparser.parse('http://feedparser.org/tests/illformed/rss/aaa_illformed.xml')
>>> d.bozo
1
>>> d.bozo_exception
<xml.sax._exceptions.SAXParseException instance at 0x00BAAA08>
>>> exc = d.bozo_exception
>>> exc.getMessage()
"expected '>'\\n"
>>> exc.getLineNumber()
6
```

所以我们可以判断`d.bozo`值是否为1来确定是否获取了正确的feed。
经过测试，还有一种方法可行，判断`d.feed`是否为`{}`:
  ```Python
  d = feedparser.parse(url)
  if d.feed == {}:
          return 0
  ```





s
