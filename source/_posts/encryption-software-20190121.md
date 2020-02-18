---
title: encryption software 20190121
date: 2019-01-21 14:58:48
tags:
categories:
---


[Boxcrypto](https://www.boxcryptor.com/en/): Boxcrypto是一款云存储第三方加密软件。对文件采用AES-256标准进行加密再上传到云端，保障用户的绝对隐私，用Boxcryptor加密后的文件，需要结合Boxcryptor进行查看

----

[Cryptomator](https://cryptomator.org/):Free client-side encryption for your cloud files.
Open source software: No backdoors, no registration.Cryptomator 是一款开源、免费、专门为保护网盘文件而设计的加密软件，可以全自动地帮你加密整个网盘以防资料被盗.它理论上可以配合任何「同步型」的网盘进行使用，包括 Dropbox、百度网盘、BT Sync、ownCloud、SeaFile 等，也能当作独立的单机加密软件来使用。

你要创建一个「Vault」(加密文件库)，将它的路径设置在你的同步盘文件夹里，并为它设置一个密码。这个 Vault (库) 实际上只是个普通的文件夹，但里面存储的将会是你全部被加密的文件，所以我们最好不要去动它，只需把它丢在网盘的路径下，让这个文件夹自动同步即可。

Cryptomator使用了AES-256位加密算法，并且全程加密均在客户端进行，无需注册账户、无需任何在线服务，因此「即便是离线状态也能无障碍使用」。

----

TrueCrypt:是一款免费，开源的支持多PC平台的绿色虚拟加密磁盘工具，可以在硬盘上创建一个或多个虚拟磁盘，所有虚拟磁盘上的文件都被自动加密，需要通过密码来进行访问。TrueCrypt提供多种加密算法，如AES-256、Blowfish(448-bitkey)、CAST5、Serpent、Triple DES等，其他特性还包括支持FAT32和NTFS分区、隐藏卷标和热键启动。但TrueCrypt除了创建虚拟磁盘外，没有什么多余的附加功能。不过，它允许用户选择不同的加密算法，这也可以说是软件的一个亮点。据TrueCrypt官网消息，在微软结束支持Windows XP后，TrueCrypt的开发于2014年5月终止，官网声明中建议用户将任何TrueCrypt加密的数据迁移到平台支持的加密磁盘或虚拟磁盘镜像。


---

[VeraCrypt](https://www.veracrypt.fr/en/Home.html):成立于2013年6月，是从TrueCrypt衍生而来的开源项目。在加密软件TrueCrypt停止开发之后，要寻找TrueCrypt的替代，可能没有VeraCrypt比更好的了。


----

BitLocker是Windows自带的磁盘加密工具，是完全闭源的软件。



----

[GnuPG](https://www.gnupg.org/)：是GNU项目中的一员，是信息加密技术中，最“朴实”的工具，因为这实际上也就是使用GnuPG的命令。

---

PGP Desktop Professional它包含邮件加密与身份确认，资料公钥&私钥加密，硬盘及移动盘全盘密码保护，网络共享资料加密，PGP自解压文档创建，资料安全擦除等众多功能。最终版本：PGP 10.02[build13]（PGP SDK 4.0.0）。由于赛门铁克的公司的收购影响，PGP从10.0.2以后以安全插件等的形式集成于诺顿等赛门铁克公司安全产品里。

**但需要注意的是，一定要在重装系统前记得备份“我的文档”中的“PGP”文件夹里的所有文件，以备重装后恢复你的私钥。切记切记，否则将永远没有可能再次打开曾经在该系统下创建的任何加密文件！**


