---
title: kali linux 2019.4 替换burpsuite pro
date: 2019-12-03 12:06:51
tags: [kali]
categories: [Linux]
---

参考：[kali linux 2019.1 替换burpsuite pro 1.7.37](https://www.cnblogs.com/dbxiaobai/p/10646298.html)

文章中的办法总体可行，但是，具体实现有问题

1. 先切换JDK版本为1.8.执行

   ```bash
   update-alternatives  --config  java
   ```

   然后会显示多个jdk版本，1.8选2

2. 输入`java –version`会显示jdk为1.8，把下载来的破解jar和原版burpsuite放到`/usr/bin`。

3. 执行破解burpsuite的流程，教程特别多。

4. bash进入`/usr/bin`目录，先删除以前的免费版burpsuite，

   ```bash
   rm –rf burpsuite
   ```

   vi新建一个文件burpsuite，

   ```bash
   vim burpsuite
   ```

   内容为

   ```bash
   #!/bin/sh
   #java -jar burp-loader-keygen-2_1_07.jar
   java -noverify -Xbootclasspath/p:/usr/bin/burp-loader-keygen-2_1_07.jar -jar /usr/bin/burpsuite_pro_v2.1.07.jar
   
   ```

   此处，调试很长时间都不行，个人认为和burp的破解版本有关系，也就是`burp_loader.jar`或者`burp_helper.jar`，在一些版本中上面方法可行，但是有些版本又不行。  多尝试几个版本

   ```bash
   1. 将burp-loader-keygen-2_1_07.jar和burpsuite_pro_v2.1.07.jar放入/usr/bin目录
   2. bash界面，进入/usr/bin目录cd /usr/bin；运行：java -jar /usr/bin/burp-loader-keygen-2_1_05.jar
   3. 正常情况下会进入破解的流程，如果无法启动主程序，那么需要更换其它help.jar 和loader.jar尝试；
   4. 破解成功之后，如果需要运行burp,直接在bash中输入burpsuite即可
   ```

   

5. 保存退出，增加执行权限`chmod +x burpsuite`

6. 进入`/usr/share/applications`，并编辑burpsuite的快捷方式

   ```bash
   cd /usr/share/applications
   vi kali-burpsuite.desktop
   ```

7. `Exec=sh -c "java -jar /usr/bin/burpsuite"`改为`Exec=sh -c "/usr/bin/burpsuite"`

   此处，本人修改为`Exec=sh -c "cd /usr/bin; /usr/bin/burpsuite"`

8. 然后就可以点桌面的快捷方式运行，开始快乐的使用吧！

