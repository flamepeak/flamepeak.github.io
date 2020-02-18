---
title: Python urllib decompress gzip
date: 2017-02-16 10:38:28
tags: [Python]
categories: [Python]
---

Check if the content is gzipped and decompresses it:
```Python
from StringIO import StringIO
import gzip

request = urllib2.Request('http://example.com/')
request.add_header('Accept-encoding', 'gzip')
response = urllib2.urlopen(request)
if response.info().get('Content-Encoding') == 'gzip':
    buf = StringIO(response.read())
    f = gzip.GzipFile(fileobj=buf)
    data = f.read()
```

### StringIO BytesIO
In Python3,the `StringIO` and `cStringIO` modules are gone. Instead, import the `io` module and use `io.StringIO` or `io.BytesIO` for text and data respectively.
[More from docs](https://docs.python.org/3/library/io.html)

Compatible with both Python2 and Python3:
```Python
try:
    from StringIO import StringIO
except ImportError:
    from io import StringIO
```
