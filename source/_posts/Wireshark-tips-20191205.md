---
title: Wireshark 使用技巧
date: 2019-12-05 09:32:25
tags: [Wireshark]
categories: [Network Security]
---


### 字符串搜索

1. 搜索字符串用`ctrl + F`，配置选项，选择`Packet Bytes`和`String`，然后输入想要查询的内容即可；

2. 可以在过滤框中直接输入`tcp and frame contains "xxxxxxx"`，来搜索内容

3. 可以使用`frame matches "xxxxxx"`，关键词matches使用与Perl语言兼容的正则表达式来搜索匹配。例如：

   ```
   frame matches "(?i)bhi"   #其中(?i)表示忽略大小写
   frame matches "(?i)bh[is]"    #匹配 BHS  BHI bHi bhi等
   frame matches "(?i)(username|password)"   #匹配username 或 password，忽略大小写
   frame matches "\.(?i)(exe|zip|doc|xls|ppt|jar)"  #\.表示转义，扩展名匹配
   frame matches "\.com$"   #匹配.com结尾   ^表示开头，$表示结尾
   ```













 The 'frame matches' is a little different. This is a fairly flexible display filter and we will not cover all the options here. The keyword 'matches' is a "Regex next" to Wireshark - a Perl-compatible regular expression. 


