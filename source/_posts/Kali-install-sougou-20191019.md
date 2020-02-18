---
title: Kali install google-pinyin input
date: 2019-10-19 16:21:49
tags: [Linux, Kali]
categories: [Linux]
---

## kali 2019.3

[Kali install Chinese Chinese input method (one success without error)](http://www.programmersought.com/article/1738186330/)


1. View your own kernel information, the kernel version is too low, the source you need

2. Use the command `vim /etc/apt/sources.list` , configure Alibaba Cloud image source (only need one Ali cloud source)

```sh
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
```

```
#中科大 
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib 
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib 
#阿里云
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib 
deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib 
#东软大学
deb http://mirrors.neusoft.edu.cn/kali kali-rolling/main non-free contrib 
deb-src http://mirrors.neusoft.edu.cn/kali kali-rolling/main non-free contrib 
#官方源 
deb http://http.kali.org/kali kali-rolling main non-free contrib 
deb-src http://http.kali.org/kali kali-rolling main non-free contrib
```

 

3. Update with commands `apt-get update && apt-get upgrade` Synchronize the index of the source, upgrade the package
 

4. Use the command `apt-get install fcitx` Install input method framework



 

5. Use the command `apt-get install fcitx-googlepinyin` Install Google Input Method



 

6. Use the command `reboot` , restart the system



7. Click on the menu



8. Click ALL---Click on the little penguin `fcitx configuration` input method(ie input method framework)



9. The default input method is EnglishUS (according to the system).Click `+`



10. Cancel check mark---Search Google



11. Google Input MethodPlaced on the first input



12. VerificationCtrl+Space Switch input method, Firefox browser test in kali is successful



## kali 2019.4

### 安装中文支持

[解决Kali Linux 2019.4中文乱码问题](https://defcon.cn/110.html)

```bash
vim /etc/apt/sources.list
```

```
#中科大 
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib 
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib 
#阿里云
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib 
deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib 
#东软大学
deb http://mirrors.neusoft.edu.cn/kali kali-rolling/main non-free contrib 
deb-src http://mirrors.neusoft.edu.cn/kali kali-rolling/main non-free contrib 
#官方源 
deb http://http.kali.org/kali kali-rolling main non-free contrib 
deb-src http://http.kali.org/kali kali-rolling main non-free contrib
```

```bash
apt-get update && apt-get upgrade && apt-get clean
```

```bash
dpkg-reconfigure locales
选中en_US.UTF-8 UTF-8和zh_CN.UTF-8 UTF-8（空格是选择，tab是切换，*是选中）
```


安装中文字体
```bash
apt-get install xfonts-intl-chinese 
apt-get install ttf-wqy-microhei
```

在终端中使用`kali-undercover`可以在两个主题之间切换，一个win10既视感。

