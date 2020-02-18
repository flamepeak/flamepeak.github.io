---
title: 翻译：利用SHA1碰撞的BitErrant攻击
date: 2017-03-06 10:14:24
tags: [翻译]
categories: [Cryptography]
---


![biterrant_logo.jpg](/sourcepictures/2017/03/biterrant_logo.jpg)

## BitErrant attack

The BitErrant attack is a fun little exploit that shows what can go wrong in the BitTorrent protocol when SHA1 collisions become reality.
SHA1 collisions resulting in chunks of the downloaded file become replaceable with a chunk that is completely different, corrupting the downloaded file OR triggering backdoor functionalities.
An attacker can alter the execution path of the executable by serving altered chunks when the victim is downloading the executable using the BitTorrent protocol.
Thank you good folks at Google and CWI for making SHA1 collision become a reality with the SHAttered attack!

BitErrant 攻击
BitErrant攻击是一个有趣的漏洞，它证明了当SHA1碰撞攻击成为可能时BitTorrent协议可能会出错。SHA1碰撞攻击导致下载的文件数据块（chunk）可能被完全不同的数据块替换，损坏下载的文件或者触发后门功能。

当受害者使用BitTorrent协议下载可执行文件时，攻击者可以通过提供更改后的数据块，来改变可执行文件的执行路径。

谢谢Google和CWI的好伙伴，使SHA1碰撞攻击成为现实！

***

### Proof-of-concept 概念验证
Here are two EXE files with different functionality (evil has a meterpreter that will listen on all interfaces), but yielding the same .torrent file. biterrant_poc.zip
Password: biterrant.io
SHA1: eed49a31e0a605464b41df46fbca189dcc620fc5 (ya know, because what can go wrong?)
Also, here is a sophisticated (LOL) framework on how to generate such executables.
Proof that most AV software are a piece of crap:
Good file on virustotal
Evil file on virustotal

这里有两个具有不同功能的EXE文件（恶意文件有一个meterpreter将会监听所有网络接口），但是产生相同的.torrent文件。[biterrant_poc.zip](https://biterrant.io/files/biterrant_poc.zip)
密码：biterrant.io
SHA1: eed49a31e0a605464b41df46fbca189dcc620fc5 (你知道，因为什么原因它会出错)
此外，[这里](https://github.com/skelsec/BitErrant)有一个复杂的（LOL）框架，关于如何生成这样的可执行文件。

**事实证明大多数杀毒软件都是胡说八道：**
正常文件在[virustotal](https://virustotal.com/en/file/aab71ef7bf13e4fe8613d4f1f9ae136cd7f03474c0e576f0de6f9fc4c15edd97/analysis/1488732404/)
恶意文件在[virustotal](https://virustotal.com/en/file/0624ed0bad3edf8308004b323d6f3cfd70751395dc93bd1108f7a6df87223102/analysis/1488732438/)

***

### How BitTorrent works (oversimplified explanation) BitTorrent工作原理

![biterrant_info_1.jpg](/sourcepictures/2017/03/biterrant_info_1.jpg)

The first step to distribute a file over BitTorrent is to generate a ".torrent" file from the original file (DATA). This is done by slicing the file into fix sized chunks then calculating SHA1 hash on each of the chunks. The hash bytes are then concatenated together and stored under the torrent file's "pieces" dictionary key.

When someone tries to download the DATA file using BitTorrent, first the "DATA.torrent" file needs to be downloaded and parsed. Based on the information stored in the DATA.torrent file, the client searches for peers and downloads chunks of the original file (DATA). In order to make sure rouge peers will not be sending malicious data, the client verifies each downloaded chunk against the hash data stored in the DATA.torrent file. If the hash data in the torrent file doesn't match the SHA1 hash of the downloaded chunk, then the bad chunk gets discarded.

通过BitTorrent分发文件的第一步是：从原始文件（DATA）生成一个".torrent"文件。它是通过下面的步骤来实现的，首先将原始文件分割成固定大小的数据块，然后对每一块计算其SHA1哈希值。随后这些哈希值被连接在一起，并存储在torrent文件的"pieces"字典键值下。

![biterrant_info_2.jpg](/sourcepictures/2017/03/biterrant_info_2.jpg)

当有人试图使用BitTorrent下载这个DATA文件时，首先"DATA.torrent"文件需要被下载并解析。根据DATA.torrent文件存储的信息，BT客户端搜索peers并下载原始文件(DATA)的数据块。为了确保peers不会发送恶意数据，客户端会使用存储在DATA.torrent文件中的哈希数据验证每一个下载的数据块。如果torrent文件中的哈希值与下载的数据块SHA1哈希值不同，这个有问题的数据块就被丢弃。

***

### The malicious intent 恶意文件的意图

![biterrant_info_6.jpg](/sourcepictures/2017/03/biterrant_info_6.jpg)


An attacker can create an executable file which when executed looks harmless, but will change its execution path based on what data is inside the SHATTER region. Of course when checked with AntiVirus software the file will look okay as the malicious code is hidden in an encrypted blob, and will never get executed. Right?

Well, not quite. If the attacker has two chunk sized blob of data with matching SHA1 hash -looking at you SHAttered- and taking some constraints into account, generating two executables with different data but yielding the same .torrent file is possible!

攻击者可以创建一个可执行文件，这个文件在执行时看起来是无害的，但是会根据SHATTER区域内的数据更改其执行路径。当然，当用反病毒软件进行检查时，这个文件看起来是没有问题的，因为恶意代码被隐藏在一个加密的二进制对象（blob）中，并且永远也不会被执行。是这样吗？

![biterrant_info_3.jpg](/sourcepictures/2017/03/biterrant_info_3.jpg)

好吧，不完全是这样。如果攻击者有两个大小相同的二进制数据块，具有相同的SHA1哈希值并考虑一些限制条件，生成两个数据不同的可执行文件但是产生相同的.torrent文件是可能的！

![biterrant_info_4.jpg](/sourcepictures/2017/03/biterrant_info_4.jpg)

If the previously mentioned constraints are fulfilled, the two executables now have one chunk of data which is interchangeable. The attacker can replace this chunk to trigger the malicious functionality in the code.

One example on how the shellcode can be executed (or not) based on which chunk has been retrieved over the BitTorrent protocol.

The principle is that the decryption yields garbage when the incorrect SHATTER data is used. For example, shatter-2.pdf collision data is used for encryption, shatter-1.pdf data is used for distribution. During the download the attacker starts seeding his file where the shatter-2.pdf data is used, effectively replacing that one chunk thus triggering the decrpyion and execution of the shellcode in clients who were uncluky enough to download that specific chunk from the attacker.

如果之前提到的限制条件都已经满足，那么这两个可执行文件现在有一个可以互换的数据块。攻击者可以替换这个数据块来触发代码中的恶意功能。

![biterrant_info_5.jpg](/sourcepictures/2017/03/biterrant_info_5.jpg)

上图，根据哪一个数据块被BitTorrent协议获取来决定shellcode是否被执行。

原理是，当使用不正确的SHATTER数据，解密会产生垃圾数据。例如，shatter-2.pdf 碰撞数据被用来加密，shatter-1.pdf数据被用来分发。在下载过程中，攻击者开始把使用shatter-2.pdf数据的文件做种，从而有效的替换那个数据块，这样对于那些非常不幸从攻击者下载了特定数据块的用户，将会触发shellcode的解密与执行。



***

### FAQ
Is this something serious?
NO. at least not right now. I might reevaluate this statement when this gets used in the wild.
How can I protect myself?
Always cross-check the MD4 MD5 SHA1 SHA256 hash of the downloaded file. Good luck finding torrent sites that publish such hashes :)
There is an option when generating torrent files to include the MD5 hash of the full datafile in the generated torrent file. Most of the time it's not used, not even sure if all torrent clients respect it.

这个事情严重吗？
不严重，至少现在不严重。我可能会评估这个报告当这种方式被使用时。

我如何保护自己？
始终交叉检查下载文件的MD4 MD5 SHA1 SHA256哈希值。 祝你找到发布哈希值的torrent网站。
这里还有一个选择，当生成torrent文件时，在生成的torrent文件中包含完整数据文件的MD5值。大多数时候它没有被使用，甚至不确定是不是所有torrent客户端会检查它。
