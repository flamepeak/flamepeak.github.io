---
title: Python Selenium 和 PhantomJS 简介
date: 2016-01-15 15:39:57
tags: Python
categories: [Python]
---

*Selenium* is a poweful web scraping tool developed originally for website testing.Selenium works by automating browsers to load the website, retrieve the required data, and even take screenshots or assert that certain actions happen on the website.
Selenium does not contain its own web browser, it requires integration with thirdparty browsers in order to run.

*PhantomJS* is what is known as a “handless” browser. It loads websites into memory and executes javaScript on the page, but does without any graphic rendering of the website to the user.
So, By combining Selenium with PhantomJS, you can run an extremely powerful web scraper that handles cookies, JavaScript, headers, and everything else you need.

The Selenium library is an API called WebDriver. The WebDriver is a bit like a browser in that it can load websites, but it can also be used like a BeautifulSoup object to find page elements, interact with elements on the page(semd text , click, etc.), and do other actions to drive the web scrapers.
``` Python
import time
from selenium import webdriver

driver = webdriver.PhantomJS()
driver.get("http://pythonscraping.com/pages/javascript/ajaxDemo.html")
time.sleep(3)
print driver.find_element_by_id("content").text
driver.close()
```

Other Selector:
``` Python
driver.find\_element\_by\_css\_selector("#content")
driver.find\_element\_by\_tag\_name("div")
#and, find All emelents:
driver.find\_elements\_by\_css\_selector("#content")
driver.find\_elements\_by\_tag\_name("div")
#and, you can use BeautifulSoup too:
pageSource = driver.page_source
bsObj = BeautifulSoup(pageSource)
print(bsObj.find(id="content").get_text())
```

More powerfull ones:
``` Python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.PhantomJS()
driver.get("http://pythonscraping.com/pages/javascript/ajaxDemo.html")
try:
    element = WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.ID, "loadedButton")))
finally:
    print driver.find_element_by_id('content').text
    driver.close()
```
