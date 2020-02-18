---
title: Python urllib2 timeout 
date: 2018-01-11 16:51:51
tags: [Python]
categories: [Python]
---

python 2 的urllib2的超时代码：

```Python
import urllib2
import socket

class MyException(Exception):
    pass

try:
    urllib2.urlopen("http://example.com", timeout = 1)
except urllib2.URLError as e:
    print type(e)    #not catch
except socket.timeout as e:
    print type(e)    #catched
    raise MyException("There was an error: %r" % e)
```



或者

```Python
import urllib2
import socket

class MyException(Exception):
    pass

try:
    urllib2.urlopen("http://example.com", timeout = 1)
except urllib2.URLError, e:
    # For Python 2.6
    if isinstance(e.reason, socket.timeout):
        raise MyException("There was an error: %r" % e)
    else:
        # reraise the original error
        raise
except socket.timeout, e:
    # For Python 2.7
    raise MyException("There was an error: %r" % e)
```
