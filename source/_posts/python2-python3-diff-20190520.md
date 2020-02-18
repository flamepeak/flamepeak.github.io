---
title: python2 python3 差异
date: 2019-05-20 11:07:36
tags:
categories: [Python]
---

时代在发展，没有人可以抗拒时间的力量，鉴于此，我想有必要将python2和python3的差异写在一起，便于查询。

当然，这是积累，而不是一篇关于python2与python3区别的长篇大论。

---

### urllib、urllib2 以及Request

推荐阅读：[python2.X和python3.X中的urllib、urllib2，以及Request](https://blog.csdn.net/weixin_38706928/article/details/80362823)

在python2中，有urllib和urllib2库；
在python3中，urllib2库被合并到urllib库中。

在Pytho2.x中使用import urllib2——-对应的，在Python3.x中会使用import urllib.request，urllib.error。
在Pytho2.x中使用import urllib——-对应的，在Python3.x中会使用import urllib.request，urllib.error，urllib.parse
在Pytho2.x中使用import urlparse——-对应的，在Python3.x中会使用import urllib.parse。
在Pytho2.x中使用import urlopen——-对应的，在Python3.x中会使用import urllib.request.urlopen。
在Pytho2.x中使用import urlencode——-对应的，在Python3.x中会使用import urllib.parse.urlencode。
在Pytho2.x中使用import urllib.quote——-对应的，在Python3.x中会使用import urllib.request.quote。
在Pytho2.x中使用cookielib.CookieJar——-对应的，在Python3.x中会使用http.CookieJar。
在Pytho2.x中使用urllib2.Request——-对应的，在Python3.x中会使用urllib.request.Request。


