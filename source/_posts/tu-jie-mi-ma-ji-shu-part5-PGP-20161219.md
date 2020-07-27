---
title: 图解密码技术 读书笔记：PGP
date: 2016-12-19 14:55:05
tags: [笔记, cryptography]
categories: [Cryptography]
---

PGP（Pretty Good Privacy）——密码技术的完美组合

### PGP简介
#### 什么是PGP
PGP是于1990年左右由Philip Zimmermann编写的密码软件。
PGP支持多个平台，版本包括商用版和免费版，此外还有一个GNU遵照OpenGPG（RFC4880）规范编写的叫做GnuPG（GNU Privacy Guard）的免费软件。

#### PGP的功能
+ **对称密码**
  支持用对称密码进行加密和解密。对称密码可以单独使用，也可以和公钥密码组合成混合密码系统使用。
  可以使用的对称密码算法包括AES、IDEA、CAST、3DES、Blowfish、Twofish等。分组密码的模式使用CFB模式（Cipher FeedBack mode ，密文反馈模式）。
+ **公钥密码**
  PGP支持生成公钥密码密钥对，以及用公钥密码进行加密和解密。可以使用的算法包括RSA和ElGamal等。
+ **数字签名**
  PGP支持数字签名的生成和验证，也可以将数字签名附加到文件中，或者从文件中分离出数字签名。可以使用的数字签名算法包括RSA和DSA等。
+ **单向散列函数**
  PGP可以使用单向散列函数计算和显示消息的散列值。可以使用的单向散列函数算法包括MD5、SHA-1额RIPEMD-160等。
+ **证书**
  PGP可以生成OpenGPG中规定格式的证书，以及与X.509规范兼容的证书。还可以颁发公钥的作废证明。
+ **压缩**
  PGP支持数据的压缩和解压缩，压缩采用ZIP格式。
+ **文本数据**
  PGP可以将二进制数据和文本数据相互转换。
+ **钥匙串管理**
  PGP可以管理所生成的密钥对和从外部获取的公钥。用于管理密钥的文件称为钥匙串（key ring）。


### PGP加密
步骤如图：
![PGP_encryption](/sourcepictures/20161219/PGP_encryption.png)

步骤可分为两部分：
+ **生成和加密会话密钥**
  1.用伪随机数生成器生成会话密钥；
  2.用公钥密码加密会话密钥，这里使用的密钥是接受者的公钥；
+ **压缩和加密消息**
  3.压缩消息；
  4.使用对称密码对压缩的消息进行加密，这里使用的密钥是步骤1中生成的会话密钥；
  5.将加密的会话密钥与加密的消息拼合起来；
  6.将步骤5的结果转换为文本数据，转换后的结果就是报文数据。

用公钥密码加密会话密钥，用对称密码加密消息就是混合密码系统的特点。

### PGP解密
步骤如图：
![PGP_decryption](/sourcepictures/20161219/PGP_decryption.png)

步骤可分为三个部分：
+ **解密私钥**
  PGP的死要是保存在用户的钥匙串中的。为了防止钥匙串被盗，私钥都是以加密状态保存的，并在保存时使用基于口令的密码（PBE）。
  解密私钥的步骤如下：
  1.接收者输入解密的口令；
  2.求口令的散列值，生成用于解密私钥的密钥；
  3.将钥匙串中经过加密的私钥进行解密；
+ **解密会话密钥**
  4.将报文数据（文本数据）转换成二进制数据；
  5.将二进制数据分解成两个部分：加密的会话密钥、经压缩和加密的消息；
  6.用公钥密码解密会话密钥，这里使用步骤3中生成的接收者的私钥；
+ **解密和解压缩消息**
  7.对步骤5中得到的经过压缩和加密的消息用对称密码进行解密，这里使用会话密钥；
  8.对步骤7中的消息进行解压缩；
  9.得到原始消息。

---

### 生成和验证数字签名
#### 生成数字签名
流程如下：
![digital signature generate](/sourcepictures/20161219/PGP_generate_digital_signature.png)
步骤如下：
+ **解密私钥**
  在钥匙串中，私钥是通过口令进行加密保存的，因此不知道口令的人就无法使用相应的私钥。
  1.发送者输入签名用的口令；
  2.求出口令的散列值，生成用于解密私钥的密钥；
  3.将钥匙串中经过加密的私钥进行解密；
+ **生成数字签名**
  4.用单向散列函数计算消息的散列值；
  5.对步骤4中得到的散列值进行签名。这一步相当于使用步骤3中得到的私钥进行加密；
  6.将步骤5中生成的数字签名与消息进行拼合；
  7.将步骤6的结果进行压缩；
  8.将步骤7的结果转换为文本数据；
  9.步骤8的结果就是报文数据；

#### 验证数字签名
![PGP_verify_digital_signature](/sourcepictures/20161219/PGP_verify_digital_signature.png)
步骤如下：
+ **恢复发送者发送的散列值**
  1.将报文数据（文本数据）转换为二进制数据；
  2.对经过压缩的数据进行解压缩；
  3.将解压缩后的数据分解成经过签名的散列值和消息两部分；
  4.将经过签名的散列值用发送者的公钥进行解密，恢复出发送者发送的散列值；
+ **对比散列值**
  5.将步骤3中分解出的消息输入单向散列函数计算散列值；
  6.将步骤4中得到的散列值与步骤5中得到的散列值进行对比；
  7.如果步骤6中的结果相等则数字签名成功，不相等则验证失败。
  8.步骤3中分解出的消息就是发送者发送的消息。

### 生成数字签名并加密以及解密并验证数字签名
用PGP生成数字签名并加密
![PGP signature encrypt](/sourcepictures/20161219/PGP_signature_encrypt.png)

用PGP解密并验证数字签名
![PGP signature decrypt](/sourcepictures/20161219/PGP_signature_decrypt.png)

---

### 信任网
在使用PGP时，确认自己所得到的公钥是否真的属于正确的人（**公钥合法性**）是非常重要的，因为公钥可能会通过中间人攻击被替换。
证书是确认公钥合法性的方法之一。PGP中没有使用认证机构，而是采用一种叫做**信任网**（web of trust）的方法。在这种方法中，PGP用户会互相对对方的公钥进行数字签名。

信任网的要点是“不依赖认证机构，而是建立每个人之间的信任关系”。