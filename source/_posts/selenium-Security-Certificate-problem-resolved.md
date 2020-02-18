---
title: Selenium 此网站的安全证书有问题 解决办法
date: 2016-01-25 15:55:02
tags: Python
---

在使用selenium登录复杂的网站时遇到安全证书问题。由于证书的问题，网页不能直接打开，会显示包含如下内容的网页：

>此网站的安全证书有问题。
此网站出具的安全证书已过期或还未生效。
安全证书问题可能显示试图欺骗您或截获您向服务器发送的数据。
建议关闭此网页，并且不要继续浏览该网站。
单击此处关闭该网页。
继续浏览此网站(不推荐)。

通过中文搜索引擎没有找到可行的解决方案，无论是导入证书还是更改浏览器设置，都不可行。
最后通过google在stackoverflow中找到解决方案：

\#region SSL workaround for IE
``` python
if "Certificate Error" in driver.title:
    driver.get("javascript:document.getElementById('overridelink').click();") ```

对于中文的IE浏览器，我们只需修改一下即可：
``` python
if u'证书错误' in driver.title:
    driver.get("javascript:document.getElementById('overridelink').click();") ```
