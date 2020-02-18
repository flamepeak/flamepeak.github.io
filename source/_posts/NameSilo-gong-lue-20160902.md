---
title: NameSilo 注册使用全攻略
date: 2016-09-02 13:00:15
tags: [Hexo, NameSilo]
categories: [Web后端]
---

### 一、介绍
　　NameSilo因其稳定的服务，高性价比深受长广大站长用户的喜爱，在V2ex有很高的知名度。NameSilo只有域名产品，可以在NameSilo新注册、转入以及抢注域名。
　　NameSilo注册域名的价格比Godady实惠，COM域名只需要8.99美元,包括ICANN税费和永久隐私保护。
　　现NameSilo已经支持支付宝付款，使支付方式更加灵活多样。
　　在使用上NameSilo操作简便，支持两步验证，提高了安全性。
所以越来越多的人选择使用NameSilo，并把Godady中的域名迁移到NameSilo上。
　　笔者刚刚注册，现在把整个流程写一下，帮助后来人。

---

### 二、注册
官方网站：[NameSilo](https://www.namesilo.com/?rid=2999638qp): [https://www.namesilo.com](https://www.namesilo.com/?rid=2999638qp)
**1.**打开官网后，在search栏输入想注册的域名，然后点击`SEARCH`.
![First Step](/sourcepictures/20160902/1_search.jpg)

**2.**选择需要域名，点击`REGISTER CHECKED DOMAINS`,进入购物车。
如果域名已经被注册，会显示Registered，选择自己需要的域名打勾，如果：
![Second Step](/sourcepictures/20160902/2_search_result.jpg)

**3.**修改设置，输入优惠码
![Third Step](/sourcepictures/20160902/3_cart_content.jpg)
DNS服务器：可以不修改
是否自动续费：Yes
是否打开隐私保护：WHOIS Privacy
注册年限:自己确定自己要买几年的
> NameSilo优惠码：
> *savingone*
> *silooneoff*
> *onesaving*
> 以上三个优惠码为我们在新注册，续费或者转移域名时候都可以使用的，一个订单只能使用一个，一个账户只能使用1次

输入验证码后，记得一定要`Submit`，看一下总的价格是不是少了$1.
都确定完以后，就点击`CONTINUE`

**4.**填入注册信息，如图
![Fourth Step](/sourcepictures/20160902/4_user_register.jpg)

**5.**注册成功后付款，如图，选择自己的付款方式：
![Fifth Step](/sourcepictures/20160902/5_payment_ways.png)

---

### 三、使用管理
管理界面很简洁，把主要的列出来，如图，接下来重点讲一下域名防护，因为出现过不少域名被盗的情况，特别是域名价值较大的时候
![Sixth Step](/sourcepictures/20160902/6_manager_panel.jpg)

#### （一）域名防护
介绍一下NameSilo的安全防护机制：
> **两种安全问题的保护**：
> 一种是恢复密码安全问题，另外一种是 Domain Defender中的安全问题，即所有敏感操作（例如修改账户信息）都需要验证该安全问题后才可以操作

> **全面的通知设置**：
> 登录、解锁域名、修改任何信息，你都可以及时获得通知（短信 或 Email）

> **永久免费的 WHOIS Doamin 保护**：
> 再也不用担心黑客通过 WHOIS 反查撞库去破解你的帐号了

#### （二）两步验证
查看你的主界面，是否两步验证：
![Seventh Step](/sourcepictures/20160902/7_security_condition.png)
如果没有的话就点两步验证的链接，进入两步验证。因为我做两步验证的时候没有截图，做完界面就没有了，从Google中找了个基本一样的过来，如下：
![Eighth Step](/sourcepictures/20160902/8_2factor_auth.png)
**大致说一下流程：**
**1.**NameSilo使用的是AUTHY app来实现两步验证，要下载Authy app，安卓手机国内如果访问不了google app store就有到第三方市场下载，我在酷市场下载的。
**2.**安装Authy，安装后打开
> 1. 创建属于你的 Authy 账号
> 2. 通过电话(Phone call)或短信(SMS)验证账号
> 3. 可能会提示你backup, 建议设个密码备份一下，以免手机丢失或者格机

**3.**添加两步验证，扫描二维码，或者输入那一长串字符
**4.**成功之后将Authy界面显示的6位数字，输入框中，提交即可

最后，强调一下，两步验证建议使用，安全性高，以后每次登录时都要输入密码，然后输入Authy生成的6位数字


#### （三）domain defender 设置
配置安全问题，设定通知事项，这个自己选择，看不懂的网上翻译一下，选项太多了
![ninth Step](/sourcepictures/20160902/9_defender_config.jpg)

#### （四）NameSilo Discount Program 打折优惠计划
可以参加，打开页面勾选即可

---

### 四、GitHub Pages绑定顶级域名的方法
在NameSilo中右侧面板Account Option ->domain manager会显示你的域名，点击域名
![10th Step](/sourcepictures/20160902/10_domain_console.jpg)
修改`DNS Records`:
![11th Step](/sourcepictures/20160902/11_dns_records.jpg)

> 1、在source文件夹中新建一个CNAME文件（无后缀名），然后用文本编辑器打开，在首行添加你的网站域名，如flamepeak.com，注意前面没有http://，也没有www，然后使用hexo g && hexo d上传部署。
> 2、在域名解析提供商进行配置，下面以dnspod为例。
（1）先添加一个CNAME，主机记录写@，后面记录值写上你的xxxx.github.io
（2）再添加一个CNAME，主机记录写www，后面记录值也是xxxx.github.io
这样别人用www和不用www都能访问你的网站（其实www的方式，会先解析成xxxx.github.io，然后根据CNAME再变成xxx.com，即中间是经过一次转换的）
> 3、等十分钟左右，刷新浏览器，用你自己域名访问下试试

以上摘自知乎，步骤需要添加一项：
修改`DNS Records`，将DNSPod的NS添加到这里:
![12th Step](/sourcepictures/20160902/12_nameserver_entry.jpg)
我不知道如何写好，就间隔写的，有大神的话可以指导一下。
但是，我设置之后20分钟都不能访问，再等等。
不到30分钟，域名可以访问。

至此，设置完成！

这个我个人写的第一篇完整的攻略，请尊重原创，转载请注明[FlamePeak.com](FlamePeak.com).
