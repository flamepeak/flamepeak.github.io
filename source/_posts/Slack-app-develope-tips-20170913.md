---
title: Slack API 使用二三事
date: 2017-09-13 14:33:17
tags: [Slack]
categories: [Tools]
---

在使用Slack过程中，想实现一些外部推送通知到channel，刚开始是开发SLACK APP，很是头大感觉杀鸡牛刀,看了一中午，略微对slack api 有所了解，感觉api还是很简单的，把bot代码写了出来。 

需求很简单，就是把日志每天让机器人推送到channel。

推荐阅读的文章： 
[How to Build Your First Slack Bot with Python](https://www.fullstackpython.com/blog/build-first-slack-bot-python.html)  
[Slack Developer Kit for Python](http://slackapi.github.io/python-slackclient/basic_usage.html)

下面写一下鄙人的难看的代码：  

```Python
SLACK_BOT_TOKEN = xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
if __name__ == "__main__":


    #----------------------------------------------------
    # PostMessage mysql spider count result
    # Great
    #----------------------------------------------------
    json_result = crawl_count.mysql_count('yestoday', 'today')

    sc = SlackClient(SLACK_BOT_TOKEN)
    rst = sc.api_call(
        "chat.postMessage",
        channel = "#news",
        username = 'monitor',
        #attachments = json_result,
        icon_url = 'https://ca.slack-edge.com/T6XLZGS78-U733C4ZPG',
        text=json_result)
    print rst
```

如果只需要bot定期推送消息，就用上面这个就可以了，如果想通过bot请求调用函数并返回，那还得Real Time Messaging（RTM）之类的，貌似不是很难。


