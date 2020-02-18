---
title: 使用Selenium模拟登录网站和常见问题
date: 2016-01-20 15:46:37
tags: Python
categories: [Python]
---

遇到一个网站，登录验证过程复杂，使用Firefox、Chrome登录均不行，必须使用IE。在编写爬虫过程中，使用requests等工具均没有成功，水平太差。
最后，选择使用Selenium配合IE模拟登录，成功获取到页面。
总结如下：
``` python
# -*- coding: utf-8 -*-

from selenium import webdriver
from selenium.common.exceptions import TimeoutException
from selenium.webdriver.support.ui import WebDriverWait # available since 2.4.0
import time


# Create a new instance of the browser driver
driver = webdriver.Ie()  ##可以替换为Ie(), FireFox(),Chrome()


# go to the google home page
url = 'http://10.34.16.8'
driver.get(url)


# find the element that's name attribute is q (the google search box)
#inputElement = driver.find_element_by_name("password")
#print inputElement.text


# type in the search
#inputElement.send_keys("Cheese!")


# submit the form. (although google automatically searches now without submitting)
#inputElement.submit()


# the page is ajaxy so the title is originally this:
print driver.title


driver.find_element_by_id("username1").clear()
driver.find_element_by_id("username1").send_keys("020")
time.sleep(1)
driver.find_element_by_id("password1").send_keys("c123")
time.sleep(1)
driver.find_element_by_id("button").click()
time.sleep(1)


#print cookie
for cookie in driver.get_cookies():
    print "%s:%s" %(cookie['name'],cookie['value'])
#Get New page
driver.get("http://10.34.16.8:8000/prpall/business/prepareEdit.do?bizType=PROPOSAL&editType=NEW")


#try:
#    # we have to wait for the page to refresh, the last thing that seems to be updated is the title
#    WebDriverWait(driver, 10).until(lambda driver : driver.title.lower().startswith("cheese!"))
#    #You should see "cheese! - Google Search"
#    print driver.title
#
#finally:
#    driver.quit()```

以上代码参考自[Selenium + python的自动化框架搭建](http://blog.csdn.net/five3/article/details/7030704),有所修改，感谢


## 错误1

`driver = webdriver.Ie()  ##可以替换为Ie(), FireFox(),Chrome()
`
此行代码最开始时写为IE()，出现错误，必须写成Ie()

## 错误2

`driver.find\_element\_by\_id("username1").clear()
`
在浏览器中如果有表格自动填写的话，要使用clear()清楚文本框数据

## 错误3：如何隐藏Firefox 窗口(Firefox WebDriver)

事实上，我的IE隐藏的问题没有解决。但是看到Firefox解决方案。
The easiest way to hide the browser is to install PhantomJS. Then, change this line:

`driver = webdriver.Firefox()`
to:
`driver = webdriver.PhantomJS()`
The rest of your code won’t need to be changed and no browser will open. For debugging purposes, use `driver.save_screenshot(‘screen.png’)` at different steps of your code or just switch to the Firefox webdriver again.

## 错误4

Python: Error:you would need to set the protected mode settings to same for all zones in IE
解决方法：

1. Open IE
2. Go to Tools -> Internet Options -> Security
3. Set all zones to the same protected mode, enabled or disabled should not matter.


## 错误5

IE浏览器”是否只查看安全传送的网页内容”解决方案
此错误出现在http网页跳转到https时，要想去掉可以按如下方式操作：Intemt选项 ->安全->internet->自定义安全级别->在“其他”分类里边找到“显示混合内容”选“启用”或者“禁用”都可以
