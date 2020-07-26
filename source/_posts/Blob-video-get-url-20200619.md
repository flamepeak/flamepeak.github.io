---
title: blob视频网址分析
date: 2020-06-16 09:35:12
tags: [Tools]
categories: [Tools]
---

Blob格式视频下载一直比较困难，在此记录一些思路：



1. 首先找到blob网址，如下：`blob:https://xxxxx.h5.xxxxx.com/77e8b2d1-81a7-42f4-83bc-0897b5a82978`

2. 在Chrome浏览器中，F12打开开发者工具

3. 刷新含有视频的目标网页，然后在Network选项卡中查看请求列表

4. 寻找m3u8扩展名，然后打开指定链接：如`https://1252524126.vod2.xxxx.com/522ff1e0vodcq1252524126/867b83575285890790665095068/playlist.f3.m3u8?time=1592553016262`

5. 事实上，m3u8是一个文本文件，使用记事本之类工具打开，可以看到如下内容：

   ```
   #EXTM3U
   #EXT-X-VERSION:3
   #EXT-X-TARGETDURATION:18
   #EXT-X-MEDIA-SEQUENCE:0
   #EXTINF:16.666667,
   playlist.f3.ts?start=0&end=5506707&type=mpegts
   #EXTINF:8.333333,
   playlist.f3.ts?start=5506708&end=8260155&type=mpegts
   #EXTINF:8.333333,
   playlist.f3.ts?start=8260156&end=11013603&type=mpegts
   #EXTINF:8.333333,
   playlist.f3.ts?start=11013604&end=13767427&type=mpegts
   #EXTINF:8.333333,
   playlist.f3.ts?start=13767428&end=16520875&type=mpegts
   #EXTINF:16.666667,
   playlist.f3.ts?start=16520876&end=22027771&type=mpegts
   ...
   ...
   ```

   这样就可以分析出视频地址了，就是

   ```
   `https://1252524126.vod2.xxxx.com/522ff1e0vodcq1252524126/867b83575285890790665095068/playlist.f3.ts
   ```

   直接下载视频即可

