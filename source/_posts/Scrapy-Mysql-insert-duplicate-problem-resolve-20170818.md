---
title: Scrapy Mysql 数据库插入出现重复解决办法
date: 2017-08-18 16:30:09
tags: 	[Python, Scrapy, Mysql]
categories: [Python]
---

我在自己的Scrapy中加了两种过滤，一是使用Redis数据库作为Middleware过滤重复已经爬取的网页，不再爬取； 另一种是在item插入mysql之前进行判断数据库中是否已经有这篇文章。

很完美？

但是很遗憾，Mysql中出现了不少的重复，让人很恼火。。。

为什么？

在仔细调试了代码之后依然没有解决，最后通过搜索找到了原因与解决办法：

***

### 原因

重要参考: [将爬虫数据插入SQL数据库是为什么出现重复？](https://segmentfault.com/q/1010000003070627)

重要参考: [scrapy爬虫，数据入库后一直有重复数据，百思不得其解！！求指导](http://bbs.csdn.net/topics/391847368)


因为数据库柄插入操作是次线程的，与主线程不同步，但是你传递给数据库柄的item 是引用传递，如果主线程中改变item的值了，那么次线程中的item也会改变，所以当数据库真正要插入第一条记录时，可能已经成了第三条记录了。前面两条记录就没有了。解决办法：将引用传递 修改成值传递，或者 生成多个item对象，每次yeild的item对象不是同一个。


**用户：Mycro，这样解释：**

数据爬出来的是对的，入库才会出现重复，问题出在：

```Python
 query = self.dbpool.runInteraction(self._Cate_insert, Item)
 _Cate_insert()
```
这句话上，这句话之前打印到文件就是对的，之后打印的就会出错，有重复有丢失，但数目刚好是一样的。

当`_Cate_insert()` 执行的时候，Item已经变了。

因此问题就出在  这句话是异步的，Item ，这个对象，在出现了共享出现了问题。


**用户qq_33245827**：

其原因是由于Spider的速率比较快，而scapy操作数据库操作比较慢，导致pipeline中的方法调用较慢，这样当一个变量正在处理的时候，一个新的变量过来，之前的变量的值就会被覆盖，比如pipline的速率是1TPS，而spider的速率是5TPS，那么数据库应该会有5条重复数据。

解决方案是对变量进行保存，在保存的变量进行操作，通过互斥确保变量不被修改

```Python
#pipeline默认调用
def process_item(self, item, spider):
    #深拷贝
    asynItem = copy.deepcopy(item)
    d = self.dbpool.runInteraction(self._do_upinsert, asynItem, spider)
```


***

### 解决办法

```Python
import copy

def process_item(self, item, spider):
    #对象拷贝，深拷贝
    asynItem = copy.deepcopy(item) 
    query = self.dbpool.runInteraction(self._Cate_insert,asynItem, spider)
    return asynItem
```
引用传参的问题


***

### 另一个可能原因

在尝试了以上的解决方法之后，依然存在重复内容，很是郁闷，一直怀疑是因为settings里面的middleware和pipline顺序的问题，但是对scrapy理解不够深刻，不知怎么修改

在Google之后，找到了另一种可能造成重复的原因，start_urls中存在多个链接，这些链接下面的内容会有重复，在scrapy中默认是对这些url不进行过滤的。

参考链接： [How to filter out duplicate URLs from Scrapy’s start_urls](https://hexfox.com/p/how-to-filter-out-duplicate-urls-from-scrapys-start-urls/)

Scrapy provides a duplicate URL filter for all spiders by default, which means that any URL that looks the same to Scrapy during a crawl will not be visited twice. But for `start_urls`, the URLs you set as the first one’s a spider should crawl, this de-duplication is deliberately disabled. Why is it disabled you ask? Because Scrapy expects the list you give it to be the definitive URLs you want it to scrape first (rather than URLs it finds automatically later) and therefore if it was to ignore that definitive list and start de-duplicating it, things could get confusing for the user.

What Scrapy does in the background though when kicking off a crawl is to call a method on the Spider class named
`.start_requests()` which by default looks like this:

```Python
def start_requests(self):
    for url in self.start_urls:
        yield Request(url, dont_filter=True)

```

As you can see, all it does is loop through all the `start_urls` you specified and creates a request for each one while explicitly making sure that each request does not get dupe filtered by setting `dont_filter=True`. This is obviously the opposite of what we want!

**修复方法**

Therefore, and you’ve probably got there already, all we need to do to make sure your `start_urls` go through the de-duplication filter is to override this with our own `start_requests` method. That simply involves pasting the following code on to your spider class:

```Python
def start_requests(self):
    for url in self.start_urls:
        yield Request(url)
```

Because we haven’t passed `dont_filter=True` to the Request instance, it will simply fall back to it’s default behaviour which is to deliberately check for duplicate URLs.

Now next time your spider runs it will use this method, and voila, the duplicate URLs in your `start_urls` will
only be scraped once.
