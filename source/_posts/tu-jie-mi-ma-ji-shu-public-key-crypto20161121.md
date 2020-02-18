---
title: 图解密码技术 读书笔记:公钥密码与混合密码系统
date: 2016-11-22 21:36:55
tags: [cryptography, 笔记]
categories: [Cryptography]
---
分为两个部分：
+ 公钥密码
+ 混合密码系统

---

## 一、公钥密码
### 1.密钥分发问题(key distribution problem)
解决密码分发问题有以下几种方法：
+ 通过事先共享密钥来解决
+ 通过密钥分发中心来解决
+ 通过Diffie-Hellman密钥交换来解决
+ 通过公钥密码来解决

#### 通过事先共享密钥来解决
这是密钥分发中最简单的解决办法，就是事先用安全的方式将密钥交给对方。
这种方式有效，但是有一定局限性。如何保证安全？如何解决密钥数量巨大的情况？

#### 通过密钥分发中心来解决
如果参加加密通信的人都事先需要共享密钥，那么数量会很大，在这样的情况下，我们可以通过使用**密钥分发中心**(Key Distribution Center, KDC)来解决密钥分发问题。
当需要进行加密通信时，密钥分配中心会生成一个通信密钥，每个人只要和密钥分发中心事先共享密钥就可以。

当Alice向Bob发送加密邮件时，就需要进行以下步骤：
1. Alice想密钥分发中心发出希望与Bob进行通信的请求。
2. 密钥分发中心通过伪随机数生成器生成一个会话密钥，这个密钥是供Alice与Bob在本次通信中使用的临时密钥。
3. 密钥分发中心从数据库中取出Alice的密钥和Bob的密钥。
4. 密钥分发中心用Alice的密钥对会话密钥进行加密，并发送给Alice。
5. 密钥分发中心用Bob的密钥对会话密钥进行加密，并发送给Bob。
6. Alice对来自密钥分发中心的会话密钥（已使用Alice的密钥进行加密）进行解密，得到会话密钥。
7. Alice用会话密钥对邮件进行加密，并将邮件发送给Bob。
8. Bob对来自密钥分发中心的会话密钥（已使用Bob的密钥进行加密）进行解密，得到会话密钥。
9. Bob用会话密钥对来自Alice的密文进行解密。
10. Alice和Bob删除会话密钥。

这就是通过密钥分发中欧给你新完成Alice和Bob的通信的过程。
密钥分发中心尽管有效，但是也有局限性。首先，随着需求的增大，密钥分发中心的负荷也会随之增加，而且一旦密钥分发中心故障，整个系统就会瘫痪。另外，一旦入侵者攻入密钥分发中心，并盗取数据库，那么所有加密通信都会被破译。

#### 通过Diffie-Hellman密钥交换来解决密钥分发问题
在Diffie-Hellman密钥交换中，进行加密通信的双方需要交换信息，而这些信息即使被窃听者听到也没有问题。根据交换的信息，双方可以各自生成相同的密钥，而窃听者Eve却无法生成相同的密钥。

#### 通过公钥密码来解决密钥分发问题
在对称密码中，加密密钥和解密密钥是相同的，但是公钥密码中，加密密钥和解密密钥是不同的。只要有加密密钥，任何人都可以进行加密，但只有拥有解密密钥的人才可以解密。

---

### 2. 公钥密码(public-key cryptography)
又称为**非对称密码**（asymmetric cryptography）
公钥(public key)
私钥(private key)：有时被称为私人密钥、私有密钥、非公开密钥、秘密密钥(secret key)

公钥和私钥是一一对应的，一对公私钥统称为(key pair)。

英国电子通信安全局(Communications Electronics Security Group):是GCHQ旗下的信息安全部门，它主要负责英国境内的信息安全保障。其于1973年设计出了与RSA类似的密码，早于RSA。

公钥密码问题：
1. 公钥密码解决了密钥分发的问题，但是这并不意味着他能解决所有问题，因为我们需要判断所得到的公钥是否正确合法，这个问题被称为*公钥认证***问题。
2. 公钥密码的速度只有对称密码的几百分之一。

---

### 3. RSA
详细描述可以参考：
[阮一峰博客：RSA算法原理一](http://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html)
[阮一峰博客：RSA算法原理二](http://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html)
RSA是一种公钥密码算法，可以被用于公钥密码和数字签名。
在RSA中，明文、密钥和密文都是数字。RSA的加密是求**明文的E次方mod N**，因此只要知道E和N这两个数，任何人都可以完成加密运算，E和N的组合就是公钥。一般写为"公钥（E, N）"或者"公钥{E，N}"

RSA的解密：对密文进行D次方mod N就可以得到明文，D和N的组合就是私钥。
密钥生成步骤：
1. 随机选择两个不相等的质数p和q;
2. 计算p和q的乘积n;
3. 计算欧拉函数φ(n);
`φ(n)=(p-1)(q-1)`
4. 随机选择一个整数e，条件是`1<e<φ(n)`,且e与φ(n)互质;
5. 计算e对于φ(n)的模反元素d;
6. 那么公钥就是(n,e),私钥就是(n,d);

> 模反元素
如果两个正整数a和n互质，那么一定可以找到整数b，使得`ab-1`被n整除，或者说ab被n整除的余数是1。这时，b就叫做a对于n的模反元素。

在RSA中：
> 计算e对于φ(n)的模反元素d，模反元素就是指有一个整数d，可以使得ed被φ(n)除的余数为1.
`ed ≡ 1 (mod φ(n))`
这个式子等价于：
`ed - 1 = kφ(n)`
于是，找到模反元素d，实质上就是对下面这个二元一次方程求解。
`ex + φ(n)y = 1`, 已知e和φ(n)
这个方程可以用扩展欧几里德算法求解。

注意：**质数p和q不能被密码破译者知道**，把p和q交给密码破译者与私钥交给破译者是等价的。

有效的攻击方法：中间人攻击
![RSA中间人攻击](/sourcepictures/20161121/RSA_man_in_middle_attack.png)
存在问题：
只要对N进行质因数分解并求出p和q，就能求出D。
但是至于“求D”与“对N进行质因数分解”是否等价，这个问题没有得到数学方法的证明。也就是说可能存在不需要对N进行质因数分解也可以由E和N求出D的方法，虽然现在还没有出现这种方法。
### 4. 其它公钥密码
#### ElGamal方式
ElGamal方式是由Taher ElGamal设计的公钥算法。RSA利用了质因数分解的困难度，而ElGamal方式则利用了mod N下求离散对数的困难度。
ElGamal方式有一个缺点，就是经过加密的密文长度会变为明文长度的两倍。密码软件GnuPG中就支持这种方式。
#### Rabin方式
Rabin方式是由M.O.Rabin设计的公钥算法。Rabin方式利用了mod N下求平方根的困难度。破译Rabin公钥密码的困难度与质因数分解则是相当的，这一点已经被证明。
#### 椭圆曲线密码ECC
椭圆曲线密码(Elliptic Curve Cryptosystem, ECC)是一种备受关注的公钥密码算法。它的特点是所需的密钥长度比RSA短。
椭圆曲线密码是通过将椭圆曲线上的特定点进行特殊的乘法运算实现的，它利用了这种乘法运算的逆运算非常困难这一特性。

#### 公钥密码与对称密码比较
公钥密码与对称密码抵御暴力破解的密钥长度对比
![公钥密码与对称密码抵御暴力破解的密钥长度对比](/sourcepictures/20161121/compare_sm_asm.png)

质数会不会被用光？
不会。512比特能容纳的质数数量大约为10的150次方，这个数量比整个宇宙中原子的数量还要多。






---

## 二、混合密码系统
基本思想：用对称密码提高加解密速度，用公钥密码保护会话密钥
机制如下:
+ 用对称密码加密消息
+ 通过伪随机数生成器生成对称密码加密中使用的会话密钥
+ 用公钥密码加密会话密钥
+ 从混合密码系统外部赋予公钥密码加密时使用的密钥

混合密码系统加密：
![混合密码系统加密](/sourcepictures/20161121/hybrid_encrypt.png)