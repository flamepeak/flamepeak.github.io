---
title: Python 微信接口
date: 2017-03-20 10:51:16
tags: [Python]
categories:
---

[wxpy](https://github.com/youfou/wxpy): 用 Python 玩微信

微信机器人 / 优雅的微信个人号API，基于 itchat，全面优化接口，更有 Python 范儿。

### Install
使用 Python 3.x

`pip3 install -U wxpy`

### Usage

```Python
from wxpy import *

#初始化机器人，扫码登录
bot = Bot()


#搜索名称含有“游否”的男性深圳好友
my_friend = bot.friends().search('游否', sex=MALE, city='深圳')[0]

#发送消息
my_friend.send('Hello WeChat!')
my_friend.send_image('my_picture.jpg')

#打印来自其他好友、群聊和公众号的消息
@bot.register()
def print_others(msg):
    print (msg)

#回复my_frend 的消息（优先匹配后注册的函数！）
@bot.register(my_friend)
def reply_my_friend(msg):
    return 'received: {} ({})'.format(msg.text, msg.type)

# 开始监听和自动处理消息
bot.start()
```

### 用微信监控你的程序
通过利用微信强大的通知能力，我们可以把程序中的警告/日志发到自己的微信上。
wxpy 提供以下两种方式来实现这个需求。

#### 获得专用 Logger
```Python
from wxpy import get_wechat_logger

# 获得一个专用 Logger
# 当不设置 `receiver` 时，会将日志发送到随后扫码登陆的微信的"文件传输助手"
logger = get_wechat_logger()

# 发送警告
logger.warning('这是一条 WARNING 等级的日志，你收到了吗？')

# 接收捕获的异常
try:
    1 / 0
except:
    logger.exception('现在你又收到了什么？')
```

#### 加入到现有的 Logger
```Python
import logging
from wxpy import WeChatLoggingHandler

# 这是你现有的 Logger
logger = logging.getLogger(__name__)

# 初始化一个微信 Handler
wechat_handler = WeChatLoggingHandler()
# 加到入现有的 Logger
logger.addHandler(wechat_handler)

logger.warning('你有一条新的告警，请查收。')
```

#### 指定接收者
```Python
from wxpy import *

# 初始化机器人
bot = Bot()
# 找到需要接收日志的群 -- `ensure_one()` 用于确保找到的结果是唯一的，避免发错地方
group_receiver = ensure_one(bot.groups().search('XX业务-告警通知'))

# 指定这个群为接收者
logger = get_wechat_logger(group_receiver)

logger.error('打扰大家了，但这是一条重要的错误日志...')
```
