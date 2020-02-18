---
title: 图解密码技术 读书笔记:对称密码与分组密码
date: 2016-11-17 15:56:05
tags: [笔记, cryptography]
categories: [Cryptography]
---

本书分为三个部分：
第一部分：密码
+ 对称密码
+ 分组密码
+ 公钥密码
+ 混合密码系统

第二部分：认证
+ 单项散列函数
+ 消息认证码
+ 数字签名
+ 证书

第三部分：密钥、随机数与应用技术
+ 密钥
+ 随机数
+ PGP
+ SSL/TLS
+ 密码技术与现实社会

---

密码角色：

|名称|说明|
|---|---|
|Alice |一般角色|
|Bob |一般角色 |
|Eve |窃听者，可窃听通信内容 |
|Mallory |主动攻击者，可妨碍通信、伪造消息等 |
|Trent |可信任第三方 |
|Victor |验证者 |

名词：
加密：cryptography
加密：encrypt
解密：decrypt
明文：plaintext
密文：ciphertext
机密性：confidentiality
密码破译：cryptanalysis
破译者：cryptanalyst
算法：algorithm
密钥:key
密钥空间：keyspace
对称密码：symmetric cryptography,其它别名 **公共密钥密码**(common-key cryptography),**传统密码**(conventional cryptography)，**私钥密码**（secret-key cryptography）,**共享密钥密码**(shared-key cryptography)
非对称密码：asymmetric cryptography,又称为公钥密码(public-key cryptography)
混合密码系统:hybrid cryptosystem,将对称密码和公钥密码结合起来的密码系统

单向散列函数(one-way hash function):计算hash，用于保护数据的完整性（integrity）
散列值(hash)又称为哈希值、密码校验和(cryptography checksum)、指纹(fingerprint)、消息摘要(message digest).

消息认证码（message authentication code）:通过消息认证码不仅可以确认消息是否被篡改，而且能确认消息是否来自期待的通信对象。也就是说，消息认证码不仅能保证完整性，还能提供认证(authentication)机制。

数字签名（digital signature）：能够确保完整性、提供认证并防止否认的密码技术。
伪装(spoofing)
否认（repudiation）
验证（verify）

伪随机数生成器(Pseudo Random Number Generator, PRNG)：模拟产生随机数的算法

隐写术（steganography）:它不是让消息内容变得无法解读，而是能够隐藏消息本身。

###  密码与信息安全常识
+ 不要使用保密的密码算法
+ 使用低强度的密码比不进行任何加密更危险
+ 任何密码总有一天都会被破解
+ 密码只是信息安全的一部分

试图通过密码算法本身进行保密来确保安全性的行为，一般称为**隐蔽式安全性**(security by obsecurity)，这种行为是危险且愚蠢的。

严格来说，绝对不会被破解的密码算法是存在的，这种算法成为**一次一密**(one-time pad)，但它不是一种现实可用的密码。

要保证良好的安全性，就需要理解“系统”这一概念的本质。复杂的系统就像一根由无数环节相连组成的链条，如果用力拉，就会从其中最脆弱的环节处断开。因此系统的强度取决于其中最脆弱的环节的强度。

------

凯撒密码(Caesar cipher)

Enigma

密钥加密密钥（Key Encrypting Key,KEK）

---

### 对称密码
编码（encoding）：将现实世界中的东西映射为比特序列的操作
XOR（exclusive or）:异或

一次一密密码是又维纳(G.S.Vernam)于1917年提出，并获得专利，因此又称为维纳密码(Vernam cipher)(该专利已经失效)。一次一密无法破译这一特性由香农（C.E.Shannon）于1949年通过数学方法加以证明。一次一密是**无条件安全的**(unconditionally secure)，在理论上是**无法破译的**(theoretically unbreakable).
一次一密不实用的原因是：
+ 密钥的配送：由于密钥和明文同长度，如果有安全的方法传送密钥，那同样可以传送明文，加密就没有必要。
+ 密钥的保存：密钥绝不能重用
+ 密钥的同步：当明文很长的时候，密钥也会很长。
+ 密钥的生成：一次一密需要大量的随机数，这些随机数并不是伪随机数，而是真正的随机数。

**DES**
DES是一种将64比特明文加密成64比特密文的对称密码算法，它的密钥长度是56比特。尽管从规格上来说，DES的密钥长度是64比特，但是由于每隔7比特会设置一个用于错误校验的比特，因此实质上其密钥长度是56bit。
DES是以64bit明文为一个单位进行加密的，这个单位称为**分组**.一般来说，以分组为单位进行处理的密码算法称为**分组密码**(block cipher)。DES是分组密码的一种。
DES每次只能加密64bit的数据，如果要加密的明文比较长，就需要对DES加密进行迭代（反复），而迭代的具体方式就成为**模式**(mode)。

**DES的结构**(Feistel网络)
DES的基本结构是由Horst Feistel设计，因此也称为**Feistel网络**（Feistel network）、**Feistel结构**(Feistel structure)或者**Feistel密码**(Feistel cipher)。这一结构不仅用于DES，在其他很多算法中也有应用。
在Feistel网络中，加密的每个步骤成为**轮**(round)，整个加密过程就是进行若干次轮的循环。
DES是一种16轮循环的Feistel网络。
![Feistel网络中的一轮](/sourcepictures/20161116/Feistel_round.png)

中间的**子密钥**（subkey）是指本轮加密所使用的密钥，在Feistel网络中，每一轮都需要使用一个不同的子密钥。
**轮函数**的作用是根据“右侧”和子密钥，生成与左侧进行加密的比特序列，它是密码系统的核心。
但是，“右侧”根本没有被加密，因此我们需要用不同的子密钥对一轮的处理重复若干次，并在每两轮处理之间将左侧和右侧的数据对调。
下图，是一个3轮的Feistel网络，3轮加密计算需要进行两次左右对调，对调只在两轮之间进行，最后一轮结束之后不进行对调。
![Feistel网络的三轮加密](/sourcepictures/20161116/Feistel_round3.png)

至于解密，Feistel网络的解密操作只要按照相反的顺序来使用子密钥就可以实现，而Feistel网络本身的结构，在加密和解密时都是完全相同的。
![用相同的子密钥运行两次Feistel网络就能将数据还原](/sourcepictures/20161116/Feistel_round_decrypt.png)
![Feistel网络的三轮解密](/sourcepictures/20161116/Feistel_round_decrypt3.png)

**Feistel网络的性质**
+ Feistel网络的轮数可以任意增加。无论运行多少轮的加密计算，都不会发送无法解密的情况。
+ 加密时无论使用任何函数作为轮函数都可以正确解密。轮函数可以无需考虑解密的问题，可以被设计的任意复杂。Feistel网络实际上是从加密算法中抽取出“密码的本质部分”并将其封装成一个轮函数。
+ 加密和解密可以用相同的结构来实现。

AES最终候选算法的5个算法之中，有三个算法（MARS、RC6、Twofish）都是使用Feistel网络。然而，AES最终选择的Rijndael算法却没有使用Feistel网络，Rijndael所使用的结构称为SPN结构。


**三重DES**
三重DES（triple-DES）是为了增加DES的强度，将DES重复3次所得到的一种密码算法，通常称为3DES。
![三重DES的加密](/sourcepictures/20161116/3DES_encrypt.png)

3DES的密钥长度为56*3=168比特。并不是三次DES加密（加密-->加密-->加密），而是加密-->解密-->加密的过程，主要原因是为了兼容DES。当三次密钥均相同时，3DES与DES相同。
![三重DES的解密](/sourcepictures/20161116/3DES_decrypt.png)

现状，尽管3DES目前还被银行等机构使用，但其处理速度不高，而且在安全性方面也逐渐出现一些问题。


**AES**（Advanced Encryption Standard）
用来取代DES的对称密码算法。
有5个算法入选了最终候选名单：MARS（来自IBM公司）、RC6（来自RSA公司）、Rijndael（来自比利时密码学家Joan Daemon与Vincent Rijmen）、Serpent、Twofish（来自Counterpane公司）。
最终，Rijndael算法被选为AES标准。

**Rijndael**
由比利时密码学家Joan Daemen和Vicent Rijmen设计的分组密码算法。分组长度为128bit，密钥长度可以以32bit为单位在128bit到256bit的范围内选择。（在AES的规格之中，密钥长度只有128、192、256比特三种）

和DES一样，Rijndael算法也是使用多轮结构，但是Rijndael没有使用Feistel网络，而是使用SPN结构。
![Rijndael加密中的一轮](/sourcepictures/20161116/Rijndael_round.png)
Rijndael的输入分组为128比特，也就是16字节。
1. 首先，需要逐个字节地对16字节的输入数据进行SubBytes处理。所谓SubBytes，就是以每个字节的值(0～255中的任意值)为索引，从一张拥有256个值的替换表(S-Box)中查找出对应值的处理，也就是说，将一个1字节的值替换成另一个1字节的值。
2. SubBytes之后需要进行ShiftRows处理，即将SubBytes的输出以字节为单位进行打乱处理。从图中可以看到这种打乱是有规律的。
3. ShiftRows之后要进行MixColumns处理，即对一个4字节的值进行比特运算，将其变为另外一个4字节值。
4. 最后，将MixColumns的输出与轮密钥进行XOR，即进行AddRoundKey处理。

这样，Rijndael的一轮就结束了。实际上，在Rijndael中需要重复进行10～14轮计算。

解密流程如下：
![Rijndael解密中的一轮](/sourcepictures/20161116/Rijndael_round_decrypt.png)
从图中我们可以看出，SubBytes、ShiftRows、MixColumns分别存在反向运算InvSubBytes、InvShiftRows、InvMixColumns，这是因为Rijndael不像Feistel网络一样能够用同一种结构实现加密和解密。

Rijndael的算法背后有着严谨的数学结构，也就是说从明文到密文的计算过程可以全部用数学公式来表达，这是以前任何算法都不具备的性质。如果Rijndael的公式能够通过数学运算来求解，那么就意味着Rijndael能够通过数学方法进行破译，而这也为新的攻击方式提供了可能。不过，这只是一种假设而已，实际上到现在为止还没有出现针对Rijndael的有效攻击。

---

### 分组密码的模式
关于分组模式更详细的介绍请参考:[Block cipher mode of operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)
DES和AES都是分组密码，它们只能加密固定长度的明文，如果需要加密任意长度的明文，就需要对分组密码进行迭代，而分组密码的迭代方法就称为分组密码的“模式”。
分组密码有很多模式，如果模式选择不当，就无法保证机密性。
主要的模式有：ECB、CBC、CFB、OFB、CTR
+ ECB模式：Electronic CodeBook mode（电子密码本模式）
+ CBC模式：Cipher Block Chaining mode（密码分组链接模式）
+ CFB模式：Cipher FeedBack mode （密文反馈模式）
+ OFB模式：Output FeedBack mode（输出反馈模式）
+ CTR模式：Counter mode（计数器模式）

密码算法可以分为分组密码和流密码。分组密码(block cipher)是每次只能处理特定长度的一块数据的一类密码算法。这里的一块就是一个分组(block).分组的比特数就是分组长度(block length)。
例如：DES和3DES分组长度都是64比特。
AES的分组长度可以从128、192和256比特中选择，当选择128比特的分组长度时，AES一次可加密128比特的明文，并生成128bit的密文。
**流密码**（stream cipher）是对数据流进行连续处理的一类密码算法。流密码中一般以1bit、8bit或32bit等单位进行加密和解密。

分组密码处理完一个分组就结束了，因此不需要通过内部状态来记录加密的进度；流密码是对一串数据流进行连续处理，因此需要保持内部状态。

**ECB模式**
将明文分组直接加密的方式就是ECB模式，这种方式非常简单，但是由于存在弱点通常不会被使用。
![ECB模式的加密](/sourcepictures/20161116/ECB_encypt.png)
使用ECB模式加密时，相同的明文分组会转换为相同的密文分组，也就是说，我们可以把它理解为一个巨大的“明文分组-->密文分组”的对应表，因此ECB模式也称为电子密码本模式。
当最后一个明文分组内容小于分组长度时，需要一些特定的数据进行填充(padding)。

ECB模式是所有模式中最简单的一种。ECB模式中，明文分组与密文分组是一一对应的关系，如果明文中存在多个相同的明文分组，那么这些明文分组最终都会被转换为相同的密文分组。
ECB模式中，每个明文分组都各自独立地进行加密和解密，这是一个很大的弱点。

**CBC模式**
在CBC(Cipher Block Chaining)模式中，首先将明文分组与前一个密文分组进行XOR运算，然后再进行加密。
如下图：
![CBC模式的加密](/sourcepictures/20161116/CBC_encryption.svg)
当加密第一个明文分组时，由于不存在“前一个密文分组”，因此需要一个序列来代替，这个序列称为**初始化向量**(initialization vector),通常简写为IV。一般来说，每次加密时都会随机产生一个不同的比特序列来作为初始化向量。

![CBC模式的解密](/sourcepictures/20161116/CBC_decryption.svg)
对于解密过程，现在我们假设CBC模式加密的密文分组中有一个分组损坏了，在这种情况下，只要密文分组的长度没有发生变化，则解密时最多只有2个分组受到数据损坏的影响。如图：
![CBC模式加密的密文分组损坏，影响两个明文分组](/sourcepictures/20161116/CBC_decrypt_block_error.png)
假设CBC模式的密文分组中有一些比特缺失，那么即使是只缺失1比特，也会导致密文分组的长度发生变化，此后的分组发生错位，这样一来，缺失比特的位置之后的密文分组也就全部无法解密了。

CBC的应用：IPsec、3DES-CBC、AES-CBC，Kerberos version5认证系统


**CFB模式**
CFB(Cipher FeedBack,密文反馈)模式，在CFB模式中，前一个密文分组会被送到密码算法输入端。
CFB模式的加密：
![CFB模式的加密](/sourcepictures/20161116/CFB_encrypt.png)
CFB模式的解密：
![CFB模式的解密](/sourcepictures/20161116/CFB_decrypt.png)

在CFB模式中，密码算法的输出相当于一次性密码本中的随机比特序列。由于输出是通过计算得到的，并不是真正的随机数，因此CFB模式不可能像一次性密码本那样具备理论不可破译的性质。
CFB模式中，由密码算法生成的比特序列称为**密钥流**(key stream)。
![CFB_CBC比较](/sourcepictures/20161116/CFB_CBC_compare.png)
在CFB模式中，明文数据可以被逐比特加密，因此我们可以将CFB模式看做是一种**使用分组密码来实现流密码**的方式。

需要注意的是，CFB模式解密时，分组密码算法依然执行的是加密操作，因为密钥流是通过加密操作来生成的。

对CFB模式可以实施**重放攻击**(replay attack)。

**OFB模式**
OFB模式全称是Output Feedback模式（输出反馈模式）。在OFB模式中，密码算法的输出会反馈到密码算法的输入中。
OFB模式并不是通过密码算法对明文直接进行加密的，而是通过将“明文分组”和“密码算法的输出”进行XOR来产生“密文分组”的。
![OFB模式](/sourcepictures/20161116/OFB_encrypt_decrypt.png)

OFB模式与CFB模式对比：
![CFB_OFB比较](/sourcepictures/20161116/CFB_OFB_compare.png)
在OFB模式中，XOR所需要的比特序列(密钥流)可以事先通过密码算法生成，和明文分组无关。只要实现准备好所需的密钥流，则在实际明文生成密文的过程中，就完全不需要动用密码算法了，只需要明文与密钥流进行XOR就可以了。


**CTR模式**
CTR模式的全称是Counter模式（计数器模式）。CTR模式是一种通过将逐次累加的计数器进行加密来生成密钥流的密码。
CTR模式中，每个分组对应一个逐次累加的计数器，并通过对计数器进行加密来生成密钥流。最终的密文分组是通过将计数器加密得到的比特序列与明文分组进行XOR得到的。
如图：
![CTR模式加密](/sourcepictures/20161116/CTR_encrypt.png)
![CTR模式解密](/sourcepictures/20161116/CTR_decrypt.png)
或者：
![CTR模式加密与解密](/sourcepictures/20161116/CTR_encrypt_decrypt.png)

CTR模式能够以任意顺序处理分组，就意味着能够实现并行计算。在支持并行计算的系统中，CTR模式的额速度是非常快的。

CTR模式与OFB模式都不存在错误扩散。

**分组密码模式比较**
![分组密码模式比较1](/sourcepictures/20161116/Block_cipher_mode_compare1.png)
![分组密码模式比较2](/sourcepictures/20161116/Block_cipher_mode_compare2.png)
