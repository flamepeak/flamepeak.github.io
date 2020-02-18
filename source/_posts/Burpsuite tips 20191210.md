---
title: BurpSuite 使用
date: 2019-12-10 15:14:28
tags: [Burpsuite]
categories: [Tools]
---

### 过滤不想拦截的网站

[Burpsuite: just passthrough firefox detect portal](https://security.stackexchange.com/questions/187069/burpsuite-just-passthrough-firefox-detect-portal)

如果我们在Burpsuite遇到一个不想再拦截的请求，点击`Action`按钮，然后`Do not intercept...`，选择`requests to this host`即可。

对于Firefox portal 请求，可以在浏览器地址栏输入`about:config`，然后搜索` network.captive-portal-service.enabled `，将`true`改为`false`





