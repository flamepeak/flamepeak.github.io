---
title: python requests 301/302/303重定向cookie和Location问题
date: 2016-01-14 15:37:00
tags: Python
categories: [Python]
---

---

Simple One:适用cookie 不会变化的情形
``` python
import requests

params = {'username': 'Ryan',\
           'password': 'password'}
r = requests.post("http://pythonscraping.com/pages/cookies/welcome.php", data=params)
print "Cookie is set to:"
print r.cookies.get_dict()
print "-----------------"
print "Going to profile page..."
r = requests.get("http://pythonscraping.com/pages/cookies/profile.php", cookies = r.cookies)
print r.text
```


Complex One:适用于cookie可能会被修改的情况下, 使用request.Session
``` python
import requests

sess = requests.Session()
params = {'username': 'Ryan',\
           'password': 'password'}
s = sess.post("http://pythonscraping.com/pages/cookies/welcome.php", data=params)
print "Cookie is set to:"
print s.cookies.get_dict()
print "----------------"
print "Going to profile page..."
s = sess.get("http://pythonscraping.com/pages/cookies/profile.php")
print s.text
```

---

[python requests session的用法](http://docs.python-requests.org/zh_CN/latest/user/advanced.html)

跨请求保持一些 cookie

---

在爬取网页时，要注意返回的response里是否有`Location`，如果有Location就意味着可能会有跳转，即重定向

可以参考: [Python requests 301/302/303重定向（跨域/本域）cookie、Location问题](https://www.cnblogs.com/zengguowang/archive/2018/02/02/8404087.html)

原文如下：

今天使用request的get方法获取一个网站的登录页信息，结果使用charles抓包的时候发现该网站登录页303(重定向的问题)，网上查了很多资料，原因如下：

### 一、 Cookie

原因：利用requests模拟登录时，直接使用request.get(url),容易造成 301/302/303 重定向，因为cookie不持久，造成重定向之后的cookie等信息获取不到

#### （1） 同一个Host下边重定向

解决方法：使用requests.Session()方法，会使该连接持久化，并且保存请求的状态（session、cookie等），这样在重定向之后，我们使用requests.cookies就能获取到cookie了

```Python
result = requests.Session()
header = {
    'Host' : 'xxxxx',
    'Accept' : 'xxxx',
    'User-Agent': 'xxxx' # 这个最重要，建议手动设置（有时候没有设置，造成获取数据失败，本人被坑过......）
}
response = result.get(url, headers=header)
```

`注意：上面讲的重定向是在同一个host下重定向，跨域的重定向用上边的方法就获取不到cookie `


#### (2) 跨域重定向

  示例：A(http://a.kuyu.com/login)->B(http://b.baidu.com/xxxxx)->C(http://b.baidu.com/xxxx/xxxx)，类似这种形式，那么使用第一种解决办法就无法获取到cookie，这时候就需要变换一种方式来进行获取

```Python
# 获取请求url域名
def getHost(url):
    pattern = re.compile(r'(.*?)://(.*?)/', re.S)
    response = re.search(pattern, url)
    if response:
        return {'header':str(response.group(1)).strip(), 'host': str(response.group(2)).strip()}
    else:
        return None
cookieType = {} # 保存域名对应的cookie
def getRedirectCookie(url):
    locationList = set()
    cookie = ''
    resUrl = url  # 获取最后请求url
    header = {
        'User-Agen': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/61.0.3163.100 Safari/537.36'
    }
    try:
        while True:
            # 请求
            response = requests.get(url, headers=header, allow_redirects=False)
            # 获取host
            hostObj = getHost(resUrl)
            if hostObj is None:
                return None
            # 处理cookie
            cookie = response.cookies.get_dict()
            if cookie == {}:
                pass
            else:
                cookieType[str(hostObj['host']).strip()] = json.dumps(cookie)  # 保存cookie
            # 获取跳转的url
            if 'Location' in response.headers.keys():
                url = response.headers.get('Location')
                if not 'http' in url:
                    url = hostObj['header'] + '://' + hostObj['host'] + url  # 拼接host域名

                resUrl = url
                if url in locationList: break
                locationList.add(url)
            else:
                break

        return {'url': str(resUrl), 'content': response.content, 'header': response.headers}
    except urllib2.URLError, e:
        if hasattr(e, 'reason'):
            print '请求失败，原因：' + e.reason
        return None
```

**注意**： 有时候执行带有重定向的时候会出现："requests.exceptions.TooManyRedirects: Exceeded 30 redirects."的情况，那么这时候是说重定向次数上限了，也可以使用上边的这种方式来解决

### Location（重定向url）:获取重定向url

有时候遇到重定向，我们只需要获取重定向的url即可，这个时候使用上述方法开启一个保持状态的长连接，并请求时获取不到的请求的headers的，那么我们该怎么办呢？

解决：

```Python
result = requests.Session()
header = {
    'Host' : 'xxxxx',
    'Accept' : 'xxxx',
    'User-Agent': 'xxxx' # 这个最重要，建议手动设置（有时候没有设置，造成获取数据失败，本人被坑过......）
}
response = result.get(url, headers=header, allow_redirects=False)
location = response.headers['Location'] # 注意有些header返回的Location中的url是不带host部分的，需要注意一下
```

`allow_redirects=False` 的意义为拒绝默认的301/302/303重定向从而可以通过response.headers['Location']拿到重定向的URL。


